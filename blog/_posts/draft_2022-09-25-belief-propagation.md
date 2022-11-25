---
title: Belief propagation decoders
tags: [quantum-computing]
description: >
comments: true
related_posts: 
    - 
---

The belief propagation (BP) decoder, also called message-passing decoder, is one of the most commonly used decoders for classical LDPC codes, and its high performance having contributed to a renewed interest for LDPC codes in the 1990s. It consists in iteratively calculating the probability that each bit has an error, by locally passing messages between parity-checks and data bits. While there is a straightforward generalization of the BP decoder to quantum LDPC codes, the fact it is not more widely used is due to several quantum-specific issues. In particular, the degeneracy problem---the idea that many errors of equal weight can yield a given syndrome---and the presence of short loops in the Tanner graph of quantum codes can cause a failure of the BP algorithm. To circumvent those problems, many adaptations of the algorithm have been made, such as post-processing the BP algorithm with a second decoder, the ordered-statistics decoder (OSD), in case of failure of the BP procedure. OSD takes the probabilities calculated by the BP algorithm as input, and returns a valid correction that fits the syndrome.

In this post, we will start by describing the belief propagation algorithm and how it can be used to decode quantum codes. We will then study its issues, and in particular the degeneracy problem, and finally see how the OSD procedure can make use of the BP output to give a valid decoding solution.

## Belief propagation algorithm

Belief propagation is a special type of **inference** algorithm: given 
a syndrome $$\bm{s}$$, we want to infer the probability $$P(\bm{e}|\bm{s})$$ of each error $$\bm{e}$$. Since an $$N$$-bit message can have $$2^N$$ possible errors $$\bm{e}$$, just storing this probability would require an exponential space. Therefore, we simplify this problem by slightly altering our goal. Instead of inferring the full probability, we will aim to compute each marginal probability

$$
\begin{aligned}
    P(e_n | s) = \sum_{e_k, k \neq n} P(e_1,...,e_N | \bm{s})
\end{aligned}
$$

Applying Baye's rule to each term of the sum gives

$$
\begin{aligned}
    P(\bm{e}|\bm{s}) &= \frac{P(\bm{s}, \bm{e})}{P(\bm{s})} \\
    & \propto P(\bm{s}, \bm{e})
\end{aligned}
$$

The joint probability $$P(\bm{s}, \bm{e})$$ can be factored as

$$
\begin{aligned}
    P(\bm{s}, \bm{e})&=P(\bm{s}|\bm{e})P(\bm{e}) \\
    &= \ind{ \bm{H}\bm{e}=\bm{s} } P(\bm{e}) \\
    & = \prod_m \ind{s_m = \sum_{n \in \mathcal{N}(m)} e_n} \prod_n P(e_n) \label{eq:factorization}
\end{aligned}
$$

Therefore, we obtain for the marginal probability:

$$
\begin{aligned} \label{eq:sum-product}
    P(e_n | s) \propto \sum_{e_k: k \neq n} \prod_m \ind{s_m = \sum_{n \in \mathcal{N}(m)} e_n} \prod_n P(e_n)
\end{aligned}
$$

The normalization constant is often not so important in practice, as it can either be calculated at the end or eliminated completely using probability ratios. 

The idea of belief propagation is to calculate the probability $$P(e_n|\bm{s})$$ by exploiting the sum-product structure of Eq.~\eqref{eq:sum-product}. The intuition is that a sum of products can be factorized into a product of sums, which can often be calculated using a reduced number of operations. For instance, in

$$
\begin{aligned}
    ac+ad+bc+bd = (a+b)(a+c),
\end{aligned}
$$

calculating the LHS involves seven operations (four products and three additions), while the RHS involves only three operations (one product and two additions).

To understand how the BP algorithm works, we first need to introduce the notion of **factor graph**. Let us consider a factorizable function over $$N$$ variables

$$
\begin{aligned}
    f(x_1,...,x_N) = \prod_j f_j(\bm{x}_j),
\end{aligned}
$$

where each $$\bm{x}_j$$ is a subset of the variables $$\{x_i\}$$. Its factor graph consists in $$N$$ data nodes, representing the $$N$$ variables $$x_i$$, and $$M$$ factor nodes, representing the $$M$$ functions $$f_j$$. There is an edge between node $$i$$ and node $$j$$ if $$f_j$$ depends on $$x_i$$. For instance, the factor graph of $$P(\bm{e},\bm{s})$$, corresponding to Eq.~\ref{eq:factorization}, has a data node for each bit of the message and a factor node for each parity check, with an edge between $$s_j$$ and $$e_i$$ when $$s_j$$ depends on $$e_i$$. In other words, the factor graph of the decoding problem is the same as the Tanner graph of the code.

Belief propagation then works by iteratively propagating messages between check nodes and data nodes. It works as follow:

* **Initialization**: the messages from data nodes $$e_n$$ to check nodes $$s_m$$, that we denote $$m^{x}_{n \rightarrow m}$$, are initialized to the prior probabilities $$P(e_n=x)$$, for $$x \in \{ 0, 1\}$$.
* **Check to data**: we send the following messages from check nodes to data nodes

$$
\begin{aligned}
    m^x_{m \rightarrow n} 
    &= 
    \sum_{e_{n'}: n' \in \mathcal{N}(m)\setminus n} 
    \ind{s_m =x + \sum_{n' \in \mathcal{N}(m) \setminus n} e_{n'}}
    \prod_{n' \in \mathcal{N}(m) \setminus n} m^{e_{n'}}_{n' \rightarrow m}
\end{aligned}
$$

where $$\mathcal{N}(m)$$ denotes all the data nodes that are connected to $$s_m$$ in the Tanner graph.
* **Data to check**: we send the following messages from data nodes to check nodes:

$$
\begin{aligned}
    m^x_{n \rightarrow m} = \alpha_{nm} P(e_n=x) \prod_{m' \in \mathcal{N}(n) \setminus m} m^x_{m' \rightarrow n}
\end{aligned}
$$

where $$\alpha_{nm}$$ is a normalization constant and $$\mathcal{N}(n)$$ denotes all the check nodes that are connected to $$e_n$$ in the Tanner graph.
* **Calculation of the probabilities**: we then compute an approximation of the probability that each error is equal to $$1$$ as

$$
\begin{aligned}
    p_n = \alpha_n P(e_n=x)\prod_{m \in \mathcal{N}(n)} m^1_{m \rightarrow n}
\end{aligned}
$$

where $$\alpha_n$$ is a normalization constant.
From those probabilities, we can compute a correction as

$$
\begin{aligned}
    \widetilde{e}_n = \ind{p_n > \frac{1}{2}}
\end{aligned}
$$

* **Stop**: when the syndrome equation $$\bm{H}\bm{\widetilde{e}} = \bm{s}$$ is verified, or after a maximal number of iterations has been reached (in this case, we return that the BP decoder has failed).

While convergence is guaranteed when the factor graph is a tree, the presence of loops can hinder those guarantee \cite{mackay2003information}. In those cases, the BP algorithm can however still be used as a powerful heuristic.

The algorithm described above is sometimes called **sum-product algorithm** in contrast with one of its variant called **min-sum algorithm** and widely used in the context of decoding. In the min-sum version of the BP algorithm, we want to directly compute the error that has the highest probability

$$
\begin{aligned}
    \max_{\bm{e}} P(\bm{e} | \bm{s}).
\end{aligned}
$$

This maximization can be decomposed as

$$
\begin{aligned}
    \max_{e_n} \max_{e_k, k \neq n} P(e_1,...,e_N | \bm{s}).
\end{aligned}
$$

The goal of the min-sum algorithm is to evaluate the first part:

$$
\begin{aligned}
 \max_{e_k, k \neq n} P(e_1,...,e_N | \bm{s}).
\end{aligned}
$$

Replacing $$P(\bm{e} | \bm{s})$$ by $$-\log\left(P(\bm{e} | \bm{s})\right)$$, we get the following new problem:

$$
\begin{aligned}
 \min_{e_k, k \neq n} \sum_m - \log\left( \ind{s_m = \sum_{n \in \mathcal{N}(m)} e_n} \right) + \sum_n -\log\left((P(e_n)\right).
\end{aligned}
$$

In order words, we have replaced the *sum* by a *min* and the *product* by a *sum*. Since the BP algorithm only uses the distributivity property of the product over the sum, it can be generalized to any field. In particular, $$\left(\mathbb{R}, +, \min \right)$$ is a field since we have the distributivity property
$$

\begin{aligned}
    a + \min(b,c) = \min(a+b, a+c) 
\end{aligned}
$$

and the sum-product algorithm described above can therefore directly be transformed into a min-sum algorithm with those new operations.

A more detailed account of belief propagation decoding is given in Ref. \cite{mackay2003information}, and the exact version of the min-sum algorithm we implemented is given in Ref. \cite{roffe2020decoding}. While the generalization of the BP algorithm to the quantum case is straightforward \cite{poulin2006optimal, poulin2008iterative}, care needs to be taken to avoid a quantum-specific issues, as we will see next.

## The degeneracy problem

Applying the BP algorithm to quantum codes causes two important problems:

1. The Tanner graph of quantum LDPC codes inevitably contains $$4$$-loops, as shown in Ref. \cite{poulin2008iterative}, while the BP algorithm is only guaranteed to converge to the correct probability distribution for trees. However, this issue is not too problematic in practice, as our goal is not to infer the exact probability distribution but only a valid correction that has a high probability of being correct.
2. Toric codes contain many degeneracies: some syndromes can be caused by many errors of equal weights. Whenever a degenerate syndrome occurs, the BP algorithm might assign an equally high probability to all the possibilities, leading to a meaningless correction where all the possible low-weight errors are applied at the same time. Simple examples of such phenomenon are given in Ref. \cite{poulin2008iterative}.
Several solutions to the degeneracy problem have been proposed in the literature, such as breaking the degeneracy with random noise \cite{poulin2008iterative}, using a neural network to learn the BP procedure, with a loss function tailored to avoid degeneracies \cite{liu2019neural}, adding memory effects \cite{kuo2021exploiting}, or complementing the BP decoder with a second decoder such as the Ordered-Statistics Decoding (OSD) \cite{roffe2020decoding}. In this work, we used this last solution, that we will describe now.

## Ordered-Statistics Decoding

The decoding problem can be formulated as an inverse problem: for a syndrome $$\bm{s}$$, we want to find an error $$\bm{e}$$ that solves the equation

$$
\begin{aligned}
    \bm{H} \bm{e} = \bm{s}
\end{aligned}
$$

However, this system of equations is highly underdetermined: many combinations of errors can fit a given syndrome. The idea of the Ordered-Statistics Decoder (OSD) is to fix some values of $$\bm{e}$$ at a given value (for instance 0), such as the system applied to the remaining variables is full-rank. To choose the subset of $$\bm{e}$$ that is kept in the system, OSD takes as input a prior probability on each error $$e_i$$. In the BP-OSD decoder, this prior probability corresponds to the output of the Belief Propagation algorithm.

The OSD algorithm then works as follow \cite{roffe2020decoding}:

1. Order the columns of $$\bm{H}$$ by decreasing probability of error.
2. Select the $$r$$ independent columns of $$\bm{H}$$ with the highest probability, where $$r$$ is the rank of $$\bm{H}$$.
3. Select $$r$$ independent rows from the new matrix, obtaining a full-rank square matrix $$\bm{H}_{\text{r}}$$.
4. Solve the reduced system

$$
\begin{aligned}
    \bm{H}_{\text{r}} \bm{e}_{\text{r}} = \bm{s}_{\text{r}}
\end{aligned}
$$

where $$\bm{e}_{\text{r}}$$ contains the $$r$$ selected components of the error (ordered by probability), and $$\bm{s}_{\text{r}}$$ the $$r$$ selected components of the syndrome.
5. Set the remaining components of $$\bm{e}$$ (not contained in $$\bm{e}_{\text{r}}$$) at $$0$$.
Higher-order versions of OSD that set the remaining components in Step 5 to values other than zero have been proposed \cite{roffe2020decoding}, but we have not implemented them in this thesis.
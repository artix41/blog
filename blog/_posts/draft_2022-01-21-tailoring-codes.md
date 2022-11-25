---
title: Improve your favorite quantum error-correcting code under biased noise!
tags: [quantum-computing]
description: >
comments: true
related_posts: 
    - 
---

Recently, a lot of work has been released on how to improve quantum error correction under biased noise. With the XZZX surface code or the deformed 3D toric code, threshold values can almost double for realistic bias levels, and even reach to $$50\%$$ at infinite bias! In this tutorial, we will see that tailoring a code to biased noise is not black magic. A simple procedure based on analysing the symmetries of a code can help find the most appropriate deformation for your code. We will start by illustrating the process with one of the first examples of tailored code, the XZZX surface code, and then apply our skills on different versions of the 3D toric code.


## Biased noise: what is it and why should it help?

The existence of a noise threshold below $$50\%$$ is a purely quantum phenomenon: classical codes either have a threshold of exactly $$50\%$$ or no threshold at all, meaning that increasing the size of the code always improves the performance.
Take the repetition code for instance. If we use a majority vote decoding strategy, we will fail whenever the number of errors is above $$(N+1)/2$$ (with $$N$$ the number of physical bits). So what's the probability of that, given a physical error rate $$p$$? We can model the process as a Binomial law, giving us

$$
\begin{aligned}
P_{\text{fail}} = \sum_{k=(N-1)/2}^{N} {N \choose k} p^k (1-p)^{n-k}
\end{aligned}
$$

We can plot it against the physical error rate $$p$$, obtaining the following plot:

[Insert plot like Eric's one]

So we see that we have a threshold at $$50\%$$.
In fact, if we knew that the probability of errors was higher than $$50\%$$,
we could use a different decoding strategy consisting in doing a *minority vote*, obtaining a symmetric curve around $$p=50\%$$ and no threshold at all.

So what's going wrong with quantum codes? A first thing is that we wouldn't be able to use majority vote decoding, since we can't measure the state directly. We would need to only use parity-checks (i.e. stabilizers). However, this is not the main problem, since it's possible to show that minimum-weight perfect matching on the resulting 1D graph leads to a $$50\%$$ threshold as well. No, the real problem comes from the fact that we have both $$X$$ and $$Z$$ errors. In this case, a simple repetition code wouldn't be able to correct both errors, hence the need of specific quantum codes like Shor's code or the surface code. On the other hand, if we were given a quantum device with much more $$Z$$ errors than $$X$$ errors (or the reverse), the noise would be closer to classical noise, and the correction process should in principle be easier. However, if you take for instance the surface code, it is not the case: due to the duality of vertex and plaquette stabilizers, the difficulty of the decoding problem doesn't change with increased biased. 

But why should we care about biased noise at all? It happens that in most quantum experiments, noise is actually biased. You can notice it by looking at the values of $$T1$$ and $$T2$$ usually given in the specs of quantum computers. $$T1$$ is the typical time to go from $$\vert1\rangle$$ to $$\vert0\rangle$$ (corresponding to an $$X$$ error), while T2 is the typical time to go from $$\vert+\rangle$$ to $$\vert-\rangle$$ (corresponding to a $$Z$$ error). The fact that those two values are most often very different is a first indication of biased noise. However, this is just a hint: to characterize the noise bias more rigorously, we also need to look at gate errors, etc. Some groups have published such a characterization, showing that you can potentially reach bias ratios of more than 100 (i.e. 100 times more $$Z$$ errors than $$X$$ errors).

So, if realistic noise is biased, can we tailor quantum codes to deal with this information? 

## Tailoring the surface code

Starting in 2017, a series of papers was published showing that simple modifications to the surface code can lead to very high thresholds in the biased noise regime[^1][^2][^3][^4]. In particular, the XZZX surface, with its threshold of $$50\%$$ in the pure $$Z$$ noise regime, was one of the most [scited](https://scirate.com/arxiv/2009.07851) papers in 2020 and even [made the news](https://www.youtube.com/watch?v=cYtcakejW-w) on Australian TV! 
Let's now understand how it works.

### XZZX surface code: definition

The XZZX surface is quite simple to describe. Instead of having the usual plaquette stabilizers made of $$X$$ operators, and the vertex stabilizers made of $$Z$$ operators, $$X$$ and $$Z$$ operators now appear in all the stabilizers:

[Insert picture with the transition between the CSS and XZZX stabilizers]

We can also understand this code as the application of a Hadamard gate on all vertical qubits, resulting in the mapping $$X \leftrightarrow Z$$ on this axis. You can check that this operation leads to a valid quantum code, i.e. all stabilizers still commute. More generally, any Clifford operation, i.e. operation that turn one Pauli to another (such as a Hadamard), will lead to a valid code. Indeed, for any pair of stabilizer $$S_1, S_2$$ and Clifford operation $$U$$, we have

$$
\begin{aligned}
[U S_1 U^\dag, U S_2 U^\dag] = U [S_1, S_2] U^\dag = 0
\end{aligned}
$$

meaning that our stabilizers still commute. Note that the Clifford property was not needed to prove the commutation property, but it is needed to have stabilizers made of Pauli measurements only.

So what does this modification actually do to our original surface code? Let's see what happens if a $$Z$$ error occurs:

[Insert picture with Z errors vertically and horizontally]

We see that if it occurs on 

### Materialized symmetries: an important tool to analyse deformations

## Tailoring the 3D toric code

### Regular lattice

### Rhombic lattice

## Another motivation for bias-tailoring: the Hashing bound

At infinite bias, we know that the channel capacity should be 50%... (see Joschka's paper on tailored LDPC code)

## References

[^1]: David K. Tuckett, Stephen D. Bartlett, Steven T. Flammia, [Ultrahigh Error Threshold for Surface Codes with Biased Noise](https://arxiv.org/abs/1708.08474), 2017
[^2]: David K. Tuckett, Andrew S. Darmawan, Christopher T. Chubb, Sergey Bravyi, Stephen D. Bartlett, Steven T. Flammia, [Tailoring surface codes for highly biased noise](https://arxiv.org/abs/1812.08186), 2018
[^3]: David K. Tuckett, Stephen D. Bartlett, Steven T. Flammia, Benjamin J. Brown, [Fault-tolerant thresholds for the surface code in excess of 5% under biased noise](https://arxiv.org/abs/1907.02554), 2019
[^4]: J. Pablo Bonilla Ataides, David K. Tuckett, Stephen D. Bartlett, Steven T. Flammia, Benjamin J. Brown, [The XZZX Surface Code](https://arxiv.org/abs/2009.07851), 2020
 
---
title: "The stabilizer trilogy III — Parity-check matrices and decoding"
tags: [quantum-computing]
description: >
comments: true
related_posts:
    -
---

Intro.

## Motivation

When studying classical coding theory, we saw that a code can be defined as a binary matrix $$\bm{H}$$, called the parity-check matrix, such that any codeword $$\bm{x}$$ satisfies

$$
\begin{aligned}
\bm{H} \bm{x} = \bm{0}
\end{aligned}
$$

In consequence, a codeword corrupted by an error $$\bm{e}$$, $$\widetilde{\bm{x}}=\bm{x} + \bm{e}$$, satisfies

$$
\begin{aligned}
\bm{H} \widetilde{\bm{x}} = \bm{H} \bm{e} = \bm{s}
\end{aligned}
$$

where $$\bm{s}$$ is the syndrome. This formulation of linear coding theory was useful for many reasons:

* It allows to express the decoding problem as a constrained optimization problem

$$
\begin{aligned}
\max_{\bm{e} \in \mathbb{Z}_2^n} P(\bm{e}) \; \text{ s.t. } \; \bm{H} \bm{e} = \bm{s}
\end{aligned}
$$

* It makes syndrome calculations straightforward, as a simple matrix multiplication
* Many properties of a code can be deduced directly from its parity-check (such as the weight of its checks via the sparsity of the matrix, the number of encoded qubits via its rank, etc.)
* The parity-check matrix can be seen as the adjacency matrix of a graph, the Tanner graph, which is used for instance when solving the decoding problem with belief propagation.

We will now see that such a formulation is possible in the stabilizer formalism.
There are two different methods to arrive at a definition of a quantum parity-check matrix:
1. The $$GF(4)$$ format, where the elements of the parity-check matrix belong to a Galois field with four elements (instead of the binary field in the classical case).
2. The binary symplectic format, where the parity-check matrix remains binary, but doubles in size compared to the classical one.

Even though the $$GF(4)$$ formulation is sometimes useful[^1], the binary symplectic formulation is often preferred nowadays. For instance, this is the one used by most quantum error correction libraries I know of[^2].
This is therefore the one I will present in this post. For the $$GF(4)$$ formulation, see Gottesman's notes.

## Binary symplectic format

The core idea behind the binary symplectic format is the observation that Paulis can be mapped to two-bit words using the following dictionary:

$$
\begin{aligned}
(0\vert0) & \rightarrow I \\
(1\vert0) & \rightarrow X \\
(0\vert1) & \rightarrow Z \\
(1\vert1) & \rightarrow Y
\end{aligned}
$$

In this representation, the first bit indicates the presence of an $$X$$ and the second bit the presence of a $$Z$$.
So $$(0\vert0)$$ is the identity operator (no $$X$$, no $$Z$$) and $$(1\vert1)$$ is the $$Y$$ operator (as it can be written $$XZ$$).
We can also write this more formally as $$(x \vert z) \leftrightarrow X^{x} Z^{z}$$.
Note that from now on, we will ignore the phases arising when multiplying Pauli operators, as they don't play any role in our formalism. For instance, we will write $$Y=XZ$$ instead of $$Y=iXZ$$.

What's nice with this representation is that the multiplication of Pauli elements maps to the addition (modulo 2) of two-bit words [^3]. Indeed,

$$
\begin{aligned}
(x \vert z) + (x' \vert z') = (x+x' \vert z+z') \leftrightarrow X^{x} Z^{z} X^{x'} Z^{z'} = X^{x+x'} Z^{z+z'}
\end{aligned}
$$

We were able to commute things through in the last equality as Pauli operators commute up to a phase, which we can safely ignore.

For instance, $$(1\vert0) + (0\vert1) = (1\vert1) \leftrightarrow XZ = Y$$, or $$(1\vert0) + (1\vert0) = (0\vert0) \leftrightarrow X^2 = I$$.

This representation can be generalized to many-qubit Pauli operators as well. A Pauli vector $$P=P_1 \otimes \ldots \otimes P_n$$ is mapped to the following vector in the binary symplectic format:

$$
\begin{aligned}
(x_1 \ldots x_n \vert z_1 \ldots z_n)
\end{aligned}
$$

where $$(x_i \vert z_i)$$ is the representation of $$P_i$$. For example, the operator $$Z \otimes Y$$ can be written $$(01 \vert 11)$$.

The next step is to express commutation relations in this format. Indeed, remember that the syndrome depends entirely on the commutation relations between stabilizers and errors (the syndrome is $$0$$ if they commute and $$1$$ if they anticommute). This is where the "symplectic" part of the binary symplectic format kicks in.

Let's define the **symplectic product** $$\odot$$ between two binary symplectic vectors $$(\bm{x} \vert \bm{z})$$ and $$(\bm{x'} \vert \bm{z'})$$ as

$$
\begin{aligned}
(\bm{x} \vert \bm{z}) \odot (\bm{x'} \vert \bm{z'}) = (\bm{x} \vert \bm{z}) \Omega (\bm{x'} \vert \bm{z'})^T = \bm{x} \cdot \bm{z'} + \bm{z} \cdot \bm{x'}
\end{aligned}
$$

where $$\Omega$$ is the **symplectic matrix**, defined as:

$$
\begin{aligned}
\Omega = \left(
\begin{matrix}
0 & I_n \\
I_n & 0
\end{matrix}
\right)
\end{aligned}
$$

and $$\cdot$$ is the usual dot product modulo $$2$$. The interpretation of this product is that two Pauli operators commute if their binary symplectic product is 0, and anticommute if it is $$1$$.
To see this, notice that $$x_i z'_i + z_i x'_i$$ is equal to $$1$$ if and only if there is an $$X$$ and a $$Z$$ intersecting on qubit $$i$$, but not a $$Y$$ on both (which would give $$2$$, or $$0$$ modulo $$2$$). In other words, it is $$1$$ if and only if the two Pauli elements acting on qubit $$i$$ anticommute. Summing over all $$i$$, it means that the symplectic product $$\bm{x} \cdot \bm{z'} + \bm{z} \cdot \bm{x'}$$ is equal to $$1$$ if and only if there is an odd number of Pauli elements that anticommute, or equivalently, if the two Pauli operators anticommute.

## The quantum parity-check matrix

We are now ready to talk about parity-check matrices. Let $$\mathcal{S}$$ a stabilizer group on $$n$$ qubits, generated by a set of $$m$$ stabilizers $$\{S_i\}$$. The **quantum parity-check matrix** of the code, associated to those generators, is the $$m \times 2n$$ matrix where each row is a stabilizer written in the binary symplectic format. For example, here is the parity-check matrix of the Steane code, with the plaquette stabilizers as generators:

$$
\begin{aligned}
\bm{H} = \left(
\begin{matrix}
\color{OrangeRed} 1 & \color{OrangeRed} 1 & \color{OrangeRed} 1 & \color{OrangeRed} 1 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \vert & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 \\
\color{RoyalBlue} 0 & \color{RoyalBlue} 1 & \color{RoyalBlue} 1 & \color{RoyalBlue} 0 & \color{RoyalBlue} 1 & \color{RoyalBlue} 1 & \color{RoyalBlue} 0 & \vert & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 \\
\color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 1 & \color{ForestGreen} 1 & \color{ForestGreen} 0 & \color{ForestGreen} 1 & \color{ForestGreen} 1 & \vert & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 \\

\color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \vert & \color{OrangeRed} 1 & \color{OrangeRed} 1 & \color{OrangeRed} 1 & \color{OrangeRed} 1 & \color{OrangeRed} 0 & \color{OrangeRed} 0 & \color{OrangeRed} 0 \\
\color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \color{RoyalBlue} 0 & \vert & \color{RoyalBlue} 0 & \color{RoyalBlue} 1 & \color{RoyalBlue} 1 & \color{RoyalBlue} 0 & \color{RoyalBlue} 1 & \color{RoyalBlue} 1 & \color{RoyalBlue} 0 \\
\color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \vert & \color{ForestGreen} 0 & \color{ForestGreen} 0 & \color{ForestGreen} 1 & \color{ForestGreen} 1 & \color{ForestGreen} 0 & \color{ForestGreen} 1 & \color{ForestGreen} 1 \\
\end{matrix}
\right)
\end{aligned}
$$

I have colored each row in accordance to the plaquette it corresponds to. For convenience, here are the plaquette stabilizers of the Steane code again:

![](/assets/img/blog/stabilizer-formalism-1/steane-code-stabilizers.png)
{:.figure}

**Exercise**: Write the parity-check matrix of Shor's code
{:.message}

One important thing to notice in the matrix above is that it can be written:

$$
\begin{aligned}
\bm{H} = \left(
\begin{matrix}
H_{\text{Hamming}} & \vert & \bm{0} \\
\bm{0} & \vert & H_{\text{Hamming}}
\end{matrix}
\right)
\end{aligned}
$$

where $$H_{\text{Hamming}}$$ is the parity-check matrix of the classical Hamming code.

More generally, the parity-check matrix of any CSS code can be decomposed as:

$$
\begin{aligned}
\bm{H} = \left(
\begin{matrix}
H_X & \vert & \bm{0} \\
\bm{0} & \vert & H_Z
\end{matrix}
\right)
\end{aligned}
$$

with $$H_X$$ and $$H_Z$$ are the parity-check matrices of the two classical codes $$C_X$$ and $$C_Z$$.
The condition that all the $$X$$ stabilizers should commute with all the $$Z$$ stabilizers can be written as

$$
\begin{aligned}
H_X H_Z^T = \bm{0}
\end{aligned}
$$

Indeed, each component $$i,j$$ of the product $$H_X H_Z^T$$ is equal to the dot product (modulo $$2$$) between the $$X$$-check $$i$$ and the $$Z$$-check $$j$$. This dot product will be $$0$$ if and only if the two checks intersect on an even number of bits, or in other words, if the corresponding stabilizers commute.

Even more generally, the parity-check matrix $$\bm{H}$$ of any stabilizer code must satisfy:

$$
\begin{aligned}
\bm{H} \odot \bm{H} = \bm{0}
\end{aligned}
$$

where $$\odot$$ is the matrix version of the binary symplectic product, defined such that each component $$i,j$$ is the binary symplectic product between row $$i$$ of the first matrix and row $$j$$ of the second matrix. This constraint is what makes it hard to design quantum codes by using classical constructions: most classical codes don't satisfy this property. But that's also what makes quantum error correction so unique and interesting, as very specific tools have been introduced in the field in order to come up with new quantum codes. For instance, the topological interpretation of this constraint (as a chain complex) explains the wide intersection between topology and quantum error correction, which doesn't arise classically. We will start studying topological quantum error correction in the next post, but for now, let's continue studying the properties of those parity-check matrices.

## Syndrome and logical operators

Given an error $$\bm{e}$$ written in the binary symplectic format, we can obtain the syndrome $$\bm{s}$$ using

$$
\begin{aligned}
\bm{H} \odot \bm{e} = \bm{s}
\end{aligned}
$$

Indeed, each component of the syndrome tells us whether a given stabilizer commutes with the error.
Note that this is the same equation as the classical one, with the matrix product replaced by the symplectic product.
Now, we saw that for classical parity-check matrices, we had $$\bm{H} \bm{x} = \bm{0}$$ for every codeword $$\bm{x}$$. However, we don't really have "codewords" in quantum error correction. So what would be the equivalent of $$\bm{x}$$ in this equation, or in other words, what is the kernel of $$\bm{H}$$? Any guess?

The kernel of $$\bm{H}$$ is the set of logical operators! Indeed, as we saw in the first section of this post, we can define logical operators as elements of $$\mathcal{C}(\mathcal{S})$$, which by definition is the set of all the elements that commute with the stabilizers. And if you remember, this set includes both stabilizers and non-trivial logical operators.

This fact is actually very useful in practice: it gives us a systematic method to find the non-trivial logicals of a given a code. Find a basis for the kernel of $$\bm{H}$$, and keep all the elements that cannot be written as linear combinations of rows of $$\bm{H}$$, that is, which are not stabilizers.

## Decoding

As for classical error correction, the decoding problem for quantum codes can be expressed using parity-check matrices.
However, there is an important subtlety compared to the classical case.
Instinctively, we would like to express the decoding problem as follow:

$$
\begin{aligned}
\max_{\bm{e} \in \mathbb{Z}_2^{2n}} P(\bm{e}) \; \text{ s.t. } \; \bm{H} \odot \bm{e} = \bm{s}
\end{aligned}
$$

However, the actual goal is not quite exactly to find the error with the highest probability. The goal is to avoid applying a non-trivial logical operator during the correction. Now, remember that if a given correction satisfy the syndrome, any correction + stabilizer will also satisfy the syndrome equation.

## Tanner graph

Many methods to design quantum codes and decoders work by representing stabilizer codes using their so-called Tanner graph.
The **Tanner graph** of a code associated to an $$m \times 2n$$ parity-check matrix $$H$$ is built by creating $$2n+m$$ nodes: two nodes per qubit (one for $$X$$ errors and one for $$Z$$ errors), and one node per stabilizer.
By convention, qubit nodes are often represented by circles and stabilizer nodes by squares.
Each stabilizer node is then connected to the qubits in its support (with the right Pauli operators). In other words, $$H$$ is the biadjacency matrix of the Tanner graph.

As an example, here is the Tanner graph of the Steane code, where I colored the stabilizer nodes to indicate to which plaquette operators they correspond.

<p style="text-align:center;">
    <img src="/assets/img/blog/stabilizer-formalism-2/tanner-graph-steane.png" height="350"/>
</p>
{:.figure}

The fact that this graph contains two separate components (the $$X$$ part and the $$Z$$ part) is due to the CSS nature of the Steane code. In general, a stabilizer node can be connected to both $$X$$ and $$Z$$ qubit nodes.

Many properties of codes and decoders can be interpreted in terms of Tanner graphs. For instance, a low-density parity-check (LDPC) code can be defined as a code whose Tanner graph has constant degree (each qubit is connected to $$O(1)$$ stabilizers and each stabilizer to $$O(1)$$ qubits). Similarly, a hypergraph-product code is best interpreted as a certain product of Tanner graphs. Finally, the performance of the belief propagation decoder on a certain code strongly depends on the size of the shortest cycle (also called the girth) of its Tanner graph.

## Conclusion

## Solution of the exercises

[^1]: See for instance belief propagation defined over GF(4)
[^2]: qecsim, PanQEC, PyMatching, ldpc, etc.
[^3]: If you know a bit of abstract algebra, you will notice that our map defines an isomorphism from the Pauli group (modulo the phase) to the group $$(\mathbb{Z}_2^2, +)$$
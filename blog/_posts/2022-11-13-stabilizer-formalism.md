---
title: From classical to quantum, the stabilizer formalism
tags: [quantum-computing]
description: >
comments: true
related_posts: 
    - 
---

In our [last post](2022-05-21-classical-error-correction.md), we looked at classical linear codes and how they can be described by a set of parity-check equations.
We will now see how to generalize those ideas to build quantum codes, using the so-called stabilizer formalism.

## Motivation: quantizing the repetition code

To understand the challenges in building quantum codes, let's look back at our good ol' quantum repetition code (introduced in the [first post](2022-01-25-intro-qec-1.md) of this series), with our new classical intuition in mind.  As a reminder, the quantum repetition code is defined as the encoding of a single-qubit logical state $$\vert \psi \rangle_L = a \vert 0 \rangle_L + b \vert 1 \rangle_L$$ as a 3-qubit physical state:

$$
\begin{aligned}
\vert \psi \rangle = a \vert 000 \rangle + b \vert 111 \rangle
\end{aligned}
$$

We define the **codespace** of our code as the space of all the states that can be written as above (for any $$a,b$$).
Let's see how errors affect the codespace.
As we saw in the first post, quantum noise comes into two flavours: $$X$$ errors, also known as bit-flips, and $$Z$$ errors, also known as phase-flips.
Let's first focus on $$X$$ errors.
If an $$X$$ error occurs on the first qubit, the state is transformed into

$$
\begin{aligned}
\vert \widetilde{\psi} \rangle = X_1 \vert \psi \rangle = a \vert 100 \rangle + b \vert 011 \rangle
\end{aligned}
$$

In the classical repetition code, errors such as this one could be detected by reading the message and seeing that it is neither $$000$$ or $$111$$, or in other words, it is not a codeword. 
Decoding would then consist of taking a majority vote on the three bits. 

However, in the quantum case, "reading the message" would collapse the state and ruin any further computation we might want to do on this state.
To make error detection work, let's remember that there is another technique to detect errors on linear codes (including the repetition code), that we saw in the last post: we can look at the parity checks of the code! For the classical repetition codes, those parity checks measured the parity of all the pairs of bits. If some of the checks were one, it meant that an error had occurred, and depending on which pairs had a violated parity-check equation, we could then decode any single bit-flip error.  

And that's where the magic comes in: those parity checks can be measured quantumly without collapsing the state!
First, you might wonder what "parity" means for our state, given that we have a superposition of two computational basis elements in the codespace.
However, it's easy to verify that it is actually well-defined. Consider the state $$\vert \psi \rangle$$ subjected to any number of bit-flip errors. Choose one of the two elements of the superposition. Look at the parity of a pair of qubits. This parity will be the same if you had chosen the other element of the superposition. This is due to the fact that bit-flips are always applied simultaneously on both parts of the superposition.

Now, how do we measure this parity? We can simply measure the observable $$Z_i Z_j$$.
Indeed, $$\vert \psi \rangle$$ is an eigenstate of $$Z_i Z_j$$, and remains so when bit-flip errors are applied to the state.
For instance, $$Z_1 Z_2 \vert \psi \rangle = \vert \psi \rangle$$, meaning that measuring $$Z_1 Z_2$$ on the error-free state will give $$+1$$.
On the other hand, $$Z_1 Z_2 \vert \widetilde{\psi} \rangle = -\vert \widetilde{\psi} \rangle$$, so measuring this operator when a bit-flip has occurred on the first qubit gives $$-1$$. 
Checking those calculations on your own, and for other examples of bit-flip errors, should convince you that measuring $$Z_i Z_j$$ gives you exactly the parity between the qubits $$i$$ and $$j$$. In general, the result of measuring those operators is exactly like the syndrome we introduced in the previous post: we get $$+1$$ when a parity-check equation is satisfied, and $$-1$$ when it is not. 

So, what have we done so far? We have found a set of operators (the $$Z_i Z_j$$) such that:
1. Our error-free state is a simultaneous $$+1$$ eigenstate of all those operators
2. Single and two-qubit $$X$$ errors anti-commute with some of them, allowing the detection and correction of errors.

We will soon call those operators "stabilizers", and study their general properties.
But first---you might have been wondering all this time---what about $$Z$$ errors?
As it happens, they are actually undetectable by our code.
For instance, if a $$Z$$ error were to occur on the first qubit of the state $$\vert \psi \rangle$$, we would get the state

$$
\begin{aligned}
\vert \widetilde{\psi} \rangle = Z_1 \vert \psi \rangle = a \vert 000 \rangle - b \vert 111 \rangle
\end{aligned}
$$

This is still in the codespace of our code! Therefore, we have no way to detect this error.
So what do we do from there?
A first idea 

## Steane code, the quantum version of the Hamming code

## Clifford group and stabilizer states

## Stabilizer formalism: first definitions

## Quantum parity-check matrix and Tanner graph

## CSS Codes

## Group-theoretic reformulation

When reading QEC papers, you will quickly notice that most of them use a more abstract definition of stabilizer codes, with words such as "normalizer", "centralizer", "abelian subgroups", etc. being thrown around. The reason is that those notions, taken from group theory, allow to define the stabilizer formalism in a much more compact way. Since this group-theoretic definition of the stabilizer formalism is used everywhere, I couldn't leave you in the wild without mentioning them. So let's define.

A stabilizer set $$\mathcal{S}$$ is an abelian subgroup of the Pauli group. 

The **normalizer** \mathcal{N}(H)$$ of a subgroup $$H$$ of $$G$$ is...

The logicals belong to $$\mathcal{N}(S) \setminus S$$. It means that they commute with the stabilizers, but are not stabilizers themselves.


## Conclusion

Finally ready to learn about the surface code, which is gonna be my next post!
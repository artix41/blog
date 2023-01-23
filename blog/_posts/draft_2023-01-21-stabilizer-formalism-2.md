---
title: "Quantum error correction with the stabilizer formalism — Part II"
tags: [quantum-computing]
description: >
comments: true
related_posts:
    -
---

In the [previous post](2023-01-21-stabilizer-formalism-1.md)...

## Properties of stabilizer codes

### Number of qubits


Let's now study the properties of those stabilizer codes. First, how many logical qubits do they have?
Remember that the number of logical qubits is given by the logarithm of the dimension of the codespace, so we need to compute the dimension of $$\mathcal{C}$$.
Let $$n$$ denote the number of physical qubits of our code, and $$m$$ the number of generators of the stabilizer group (i.e. there are $$m$$ independent elements whose products generate the whole group).
The dimension of the physical Hilbert space is $$2^n$$, and each constraint $$S_i \vert \psi \rangle = \vert \psi \rangle$$ divides this dimension by two. Therefore, we have

$$
\begin{aligned}
\text{dim}(\mathcal{C})=2^{n-m}
\end{aligned}
$$

and the number of logical qubits is $$k=n-m$$.
This should remind you of classical codes, where the same formula applies when $$m$$ is the number of independent parity checks.

### Logical operators

Now that we know the number of encoded qubits, what about the distance?
To describe it, we need to introduce the notion of logical operator. A **logical operator** is a unitary operator $$L$$ that maps the codespace to itself,
i.e. such that $$L \vert \psi \rangle \in \mathcal{C}$$ for all $$\vert \psi \rangle \in \mathcal{C}$$.
Logical operators therefore include stabilizers, but also operators that map one part of the codespace to another part.
For instance, a logical Hadamard maps the logical zero state $$\vert \overline{0} \rangle$$ to the logical plus state $$\vert \overline{+} \rangle$$.

Another way to understand logical operators is through their actions on the stabilizer group. Indeed, we can prove the following proposition:

**Proposition**: a unitary $$L$$ is a logical operator if and only if it maps the stabilizer group $$\mathcal{S}$$ to itself, i.e.
$$L^{\dagger} SL \in \mathcal{S}$$ for all $$S \in \mathcal{S}$$.
{:.message}

Note that this is precisely the definition of the **normalizer** of the group $$\mathcal{S}$$, denoted $$\mathcal{N}(\mathcal{S})$$, and you will often find logical operators defined in the literature simply as the normalizer of the stabilizer group. This proposition explains why those two definitions are equivalent. Let's now prove it.

To prove the first direction, let's suppose that $$L$$ is a logical operator, i.e. $$L$$ maps the codespace to itself.
For $$S \in \mathcal{S}$$, let's show that $$LSL^{\dagger} \in \mathcal{S}$$, that is, $$LSL^{\dagger} \vert \psi \rangle = \vert \psi \rangle$$ for all $$\vert \psi \rangle \in \mathcal{C}$$. For $$\vert \psi \rangle \in \mathcal{C}$$, $$L\vert \psi \rangle \in \mathcal{C}$$ by assumption, so $$SL\vert \psi \rangle = L \vert \psi \rangle$$ and $$L^{\dagger} S L \vert \psi \rangle = L^{\dagger} L \vert \psi \rangle = \vert \psi \rangle$$. In the other direction, let's suppose that $$L \in \mathcal{N}(\mathcal{S})$$, and let's show that for a state $$\vert \psi \rangle \in \mathcal{C}$$, $$L \vert \psi \rangle \in \mathcal{C}$$. It comes down to showing $$SL \vert \psi \rangle = L \vert \psi \rangle$$ for $$S \in \mathcal{S}$$. Since $$L^{\dagger} S L \in \mathcal{S}$$ by assumption, there exists $$S' \in \mathcal{S}$$ such that $$L^{\dagger} S L = S'$$, or equivalently, $$SL = LS'$$. Therefore, $$SL\vert \psi \rangle = LS'\vert \psi \rangle = L \vert \psi \rangle$$. QED.

### Pauli logicals

An important family of logical operators are the **Pauli logical operators** (often abbreviated *Pauli logicals*, or even just *logicals* if the context is clear).
As expected, those are logical operators that can be written as a product of Paulis.
As two given Pauli operators can either commute or anticommute, Pauli logicals could either commute or anticommute with stabilizers.
However, if a Pauli logical anticommute with a stabilizer, i.e. $$SL=-LS$$, it means that $$L^{\dagger} S L = -S \notin \mathcal{S}$$, which contradicts the definition of a logical operator. Therefore, Pauli logicals can be characterized by the following proposition:

**Proposition**: a Pauli operator $$P$$ is a logical operator if and only if it commutes with all the stabilizers
{:.message}

Note that this is precisely the definition of the **centralizer** of the group $$\mathcal{S}$$ in the Pauli group, denoted $$\mathcal{C}(\mathcal{S})$$. The difference between the normalizer and the centralizer is that if $$L \in \mathcal{N}(\mathcal{S})$$, $$LS=S'L$$ for $$S, S'$$ two stabilizers, while if $$L \in \mathcal{C}(\mathcal{S})$$, $$LS=SL$$. For Pauli operators, $$\mathcal{N}(\mathcal{S}) = \mathcal{C}(\mathcal{S})$$ and you will often find Pauli logicals defined with either normalizers or centralizers in the literature.

Finally, let's define **logical errors** (also known as *non-trivial logical operators*) as elements of $$\mathcal{C}(\mathcal{S}) \backslash \mathcal{S}$$, or in other words, operators that commute with all the stabilizers but are not stabilizers themselves. We can now characterize the **distance** of a stabilizer code as the weight of the smallest logical error:

$$
\begin{aligned}
d = \min_{L \in \mathcal{C}(\mathcal{S}) \backslash \mathcal{S}} |L|
\end{aligned}
$$

The study of Pauli logicals gives us another method to count the number of logical qubits of stabilizer codes.
First, note that the set of Pauli logicals forms a group, which can be generated by...


## Steane code again

Study the logicals of Steane code

## Quantum parity-check matrix and Tanner graph

## Stabilizer states and Clifford group
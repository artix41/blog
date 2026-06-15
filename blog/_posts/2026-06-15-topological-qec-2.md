---
title: The topology of topological QEC II — Homology groups
image: /assets/img/blog/topological-qec-2/thumbnail.png
tags: [quantum-computing]
description: >
comments: true
related_posts:
    -
---

In the [previous post](/blog/2026-05-04-topological-qec/), we introduced algebraic topology through one of its most important objects: chain complexes.
We saw how chain complexes can be constructed from cellulations of manifolds and how they give rise to CSS codes.
But the main reason both mathematicians and quantum error correctors like chain complexes is that they allow us to define homology groups!

From a mathematical point of view, homology groups provide topological invariants of manifolds: quantities that do not depend on the particular cellulation we choose and do not change when the manifold is continuously deformed.
From a quantum error correction point of view, they give us the number of logical qubits of a code and help us understand the structure of logical operators.

In Part II of our series on topological quantum error correction, we will introduce homology groups and explain their interpretation in the context of quantum error correction.
This post is filled with examples and exercises, which should hopefully give you a good intuition for what these objects are and how to use them to calculate useful quantities.

The next and final post in this series will focus on cohomology groups.
These are closely related to homology groups, and they will allow us to develop a fuller understanding of the structure of logical operators in CSS codes.
They will also give us many useful theorems and tools for calculating the homology of manifolds and constructing logical gates in quantum codes.

But let's not get ahead of ourselves.
Let's start with homology groups!

# Cycles and boundaries

Let's start by introducing some important terminology.
Given a length-$$D$$ chain complex

$$
\begin{aligned}
    C_D &\xrightarrow{\partial_D} C_{D-1} \xrightarrow{\partial_{D-1}} \cdots \xrightarrow{\partial_2} C_1 \xrightarrow{\partial_1} C_0 ,
\end{aligned}
$$

and $$k \in \{0,\ldots,D\}$$, a $$\bm{k}$$**-chain** is an element of $$C_k$$.
For instance, if the chain complex comes from a cellulation of a manifold, a $$0$$-chain is a set of vertices, represented as a binary vector whose support is that set.
Similarly, a $$1$$-chain is a set of edges, a $$2$$-chain is a set of faces, and so on.

For any $$k \in \{0,\ldots,D-1\}$$, a $$\bm{k}$$**-boundary** is an element of $$\Im(\partial_{k+1})$$.
In other words, a $$k$$-boundary is a $$k$$-chain that can be written as the boundary of a $$(k+1)$$-chain.
For instance, in a cell complex, a $$1$$-boundary is a set of edges that forms the boundary of some faces, and a $$0$$-boundary is a set of vertices that forms the boundary of some edges.

Here is an example of a $$1$$-boundary in a hexagonal cellulation of a torus, where the left-right and top-bottom boundaries are identified:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/chain-1-boundary.png" height="250"/>
</p>
{:.figure}

This is a set of $$14$$ edges, or rather a vector with $$14$$ ones, equal to the boundary of a chain made of three faces.
In other words, it is the image of these three faces under the boundary map $$\partial_2$$.
Similarly, the following set of two vertices is a $$0$$-boundary: it is the boundary of a path made of three edges, or equivalently the image of those edges under $$\partial_1$$.

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/chain-0-boundary.png" height="250"/>
</p>
{:.figure}

We now define a $$\bm{k}$$**-cycle** as an element of $$\ker(\partial_k)$$, for any $$k \in \{1,\ldots,D\}$$.
In other words, a $$k$$-cycle is a $$k$$-chain with no boundary.
For instance, in a 2D cell complex, a $$1$$-cycle is a set of edges forming closed loops, and a $$2$$-cycle is a set of faces whose total boundary cancels.

Returning to our hexagonal cellulation example, what would be a $$2$$-cycle?
There is exactly one non-zero $$2$$-cycle, formed by all the faces, i.e. the all-one vector:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/chain-2-cycle.png" height="250"/>
</p>
{:.figure}

If we had instead chosen a cellulation of a disconnected manifold, such as two disconnected tori, we would have one independent $$2$$-cycle per connected component, formed by taking all the faces of that component.
Equivalently, for a closed 2D manifold, the dimension of $$\ker(\partial_2)$$ is equal to the number of connected components of the manifold.

What about $$1$$-cycles?
The first thing to notice is that the $$1$$-boundaries of our cellulation are also $$1$$-cycles: since they form closed loops, they have no boundary.

Is it always the case that $$k$$-boundaries are $$k$$-cycles for any $$k \geq 1$$, even for a generic chain complex?
Yes, and this is exactly the defining condition of a chain complex.
Recall the chain complex condition:

$$
\begin{aligned}
    \partial_{k} \circ \partial_{k+1} = 0 .
\end{aligned}
$$

This can also be written as

$$
\begin{aligned}
    \Im(\partial_{k+1}) \subseteq \ker(\partial_k),
\end{aligned}
$$

which means precisely that all $$k$$-boundaries are also $$k$$-cycles for $$k \geq 1$$.
This should make intuitive sense: the chain complex condition says that the boundary of a boundary is always empty.
Therefore, if a chain is itself a boundary, then it has no boundary, and hence it is a cycle.

However, not all $$1$$-cycles are $$1$$-boundaries.
For instance, the following set of edges is a $$1$$-cycle but not a $$1$$-boundary:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/chain-1-cycle.png" height="250"/>
</p>
{:.figure}

This observation is the starting point of homology theory: homology measures cycles that are not boundaries.

# Homology groups

The $$\bm{k}$$**-th homology group** of a chain complex $$C_\bullet$$ is defined as the quotient vector space

$$
    H_k(C_\bullet) = \ker(\partial_k) / \Im(\partial_{k+1})
$$

for all $$k \in \{0,\ldots,D\}$$.
The quotient is well-defined because, as we saw, $$\Im(\partial_{k+1}) \subseteq \ker(\partial_k)$$.

To make the definition work for $$k=0$$ and $$k=D$$ as well, we extend the chain complex by adding zero vector spaces at both ends:

$$
\begin{aligned}
    C_{D+1}=0 &\xrightarrow{\partial_{D+1}=0} C_D \xrightarrow{\partial_D} C_{D-1} \xrightarrow{\partial_{D-1}} \cdots \xrightarrow{\partial_2} C_1 \xrightarrow{\partial_1} C_0 \xrightarrow{\partial_{0}=0} C_{-1}=0 .
\end{aligned}
$$

Here, $$\partial_{D+1}$$ is the unique map from $$C_{D+1}=0$$ to $$C_D$$, sending the zero vector of $$C_{D+1}$$ to the zero vector of $$C_D$$.
Similarly, $$\partial_0$$ is the unique map from $$C_0$$ to $$C_{-1}=0$$, sending every vector of $$C_0$$ to the zero vector of $$C_{-1}$$.

The $$k$$-th homology group can be thought of as the space of $$k$$-cycles up to $$k$$-boundaries.
Since it is a quotient of vector spaces, it also has the structure of a vector space.
The name "group" is still correct, since vector spaces are in particular abelian groups, but it is mainly a historical convention.
Nowadays, in our setting, it is often more useful to think of homology groups as vector spaces, or more generally as $$R$$-modules over some ring $$R$$ [^1].

Two $$k$$-cycles are said to be **homologically equivalent** if they belong to the same coset, i.e. if they differ by a $$k$$-boundary.
In other words, two $$k$$-cycles are homologically equivalent if one can be transformed into the other by adding a $$k$$-boundary.
Nonzero elements of the homology group are homology classes that cannot be reduced to zero by adding boundary elements.

For instance, in our hexagonal cellulation example, these two $$1$$-cycles, shown in red, are homologically equivalent, since one can be obtained from the other by adding the boundary of the two red plaquettes:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/homological-equivalence.png" height="250"/>
</p>
{:.figure}

They are also non-trivial: you cannot reduce them to zero by adding any $$1$$-boundary.
A homologically distinct non-trivial coset is represented by the following $$1$$-cycle:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/horizontal-coset.png" height="250"/>
</p>
{:.figure}

What is the dimension of the first homology group $$H_1(C_\bullet)$$ in our example?
Since there are two independent non-trivial homology classes, corresponding to loops going around the torus horizontally and vertically, the homology group has dimension $$2$$.
We could prove this by computing the kernel and image of the boundary maps explicitly, since they are just matrices, and using the formula for the dimension of a quotient space:

$$
\begin{aligned}
    \dim(H_k(C_\bullet)) = \dim(\ker(\partial_k)) - \dim(\Im(\partial_{k+1})) .
\end{aligned}
$$

As we will see later, we can also deduce this directly from the fact that our chain complex corresponds to a cellulation of the $$2$$-torus.

Since any $$\mathbb{Z}_2$$-vector space of dimension $$n$$ is isomorphic to $$\mathbb{Z}_2^n$$, we can write

$$
\begin{aligned}
    H_1(C_\bullet) \cong \mathbb{Z}_2^2 .
\end{aligned}
$$

After choosing the horizontal and vertical homology classes as generators, we can identify $$H_1(C_\bullet)$$ with $$\mathbb{Z}_2^2$$.
Under this identification, the homology class $$(1,0)$$ is represented by a horizontal loop around the torus, the class $$(0,1)$$ is represented by a vertical loop, and the class $$(1,1)$$ is represented by the sum of these two loops.

Let's now discuss the $$0$$-th and $$D$$-th homology groups.
By definition, we have

$$
\begin{aligned}
    H_0(C_\bullet) &= \ker(\partial_0) / \Im(\partial_1) = C_0 / \Im(\partial_1), \\
    H_D(C_\bullet) &= \ker(\partial_D) / \Im(\partial_{D+1}) = \ker(\partial_D) / \{0\} = \ker(\partial_D),
\end{aligned}
$$

since $$\ker(\partial_0) = C_0$$ and $$\Im(\partial_{D+1}) = \{0\}$$.

These homology groups have a simple interpretation in the cell complex case.
The $$0$$-th homology group consists of $$0$$-chains, i.e. sets of vertices, up to the equivalence relation that identifies two vertices if they are connected by a path.
Indeed, $$0$$-boundaries are boundaries of $$1$$-chains, i.e. sets of edges, so adding a $$0$$-boundary allows us to move vertices along paths in the cellulation.
The dimension of $$H_0(C_\bullet)$$ is therefore exactly the number of connected components of the manifold: vertices in the same connected component are homologically equivalent, while vertices in different connected components are not.

What about the $$D$$-th homology group?
It consists of all $$D$$-chains with no boundary.
In the case of a closed $$D$$-dimensional manifold, these are generated by taking the sum of all $$D$$-cells in each connected component.
Thus, $$H_D(C_\bullet)$$ also has one generator per connected component.

The fact that $$H_D(C_\bullet)$$ and $$H_0(C_\bullet)$$ have the same dimension, for closed manifolds, is a special case of a more general result called Poincaré duality, which we will see in the post on cohomology.

# QEC interpretation

Let's now return to quantum error correction and interpret the objects we have just introduced.
Consider the chain complex

$$
\begin{aligned}
    \mathcal{S}_X &\xrightarrow{H_X^T} \mathcal{Q} \xrightarrow{H_Z} \mathcal{S}_Z .
\end{aligned}
$$

The space of $$1$$-cycles is given by $$\ker(H_Z)$$, and can be interpreted as the space of $$X$$ operators that commute with all $$Z$$ stabilizers.
The space of $$1$$-boundaries is given by $$\Im(H_X^T)$$, and can be interpreted as the space of $$X$$ stabilizers.
The first homology group is therefore

$$
\begin{aligned}
    H_1(C_\bullet) = \ker(H_Z) / \Im(H_X^T),
\end{aligned}
$$

and can be interpreted as the space of $$X$$ logical cosets: $$X$$ operators that commute with all $$Z$$ stabilizers, up to equivalence by $$X$$ stabilizers.
As we saw in the [previous post](/blog/2026-05-04-topological-qec/), the dimension of this space is exactly the number $$k$$ of logical qubits of the code.

What about $$Z$$ logical cosets?
Do they have a homological interpretation as well?
Yes: they correspond to the homology group of a different complex, where the roles of $$H_X$$ and $$H_Z$$ are swapped:

$$
\begin{aligned}
    \mathcal{S}_Z &\xrightarrow{H_Z^T} \mathcal{Q} \xrightarrow{H_X} \mathcal{S}_X .
\end{aligned}
$$

This new complex is called the **cochain complex**, and it will be the main protagonist of the next post in this series.

Finally, can the distance be given a homological interpretation as well?
If we assign a weight to elements of $$C_1$$, for instance their Hamming weight, then the $$X$$ distance of the code is the weight of the smallest non-trivial representative of $$H_1(C_\bullet)$$.
Similarly, the $$Z$$ distance is obtained from the cochain complex.

However, while homology groups are topological invariants of manifolds, as we will see in the next section, the distance is not.
It can vary significantly between different cellulations of the same manifold.
Moreover, homological algebra does not provide an obvious general tool for computing the distance.

When dealing with cellulations of manifolds, the main area of mathematics that says something about distance is **systolic geometry**, which studies the length of the shortest non-contractible loop in a manifold.
However, systolic geometry mainly gives general laws for how the distance can grow with the size of the code, sometimes leading to no-go theorems.
It does not usually provide a practical tool for computing the distance of a specific cellulation, and it does not generalize easily beyond manifolds.

In most cases, distance calculations need to be carried out explicitly for the specific code at hand.

<div class="message" markdown="1">
**Exercise 1**: <br>
**(a)** Write down the boundary matrices associated with the chain complex represented by the diagram below, and show that the chain complex condition is satisfied.
Is this a cell complex? <br>
**(b)** Show that the space of $$1$$-cycles has dimension three. <br>
**(c)** Show that the space of $$1$$-boundaries has dimension one. <br>
**(d)** Deduce the dimension of the first homology group. <br>
**(e)** Deduce the parameters of the corresponding CSS code. <br>
[(solution)](#solution-of-the-exercise)

<p style="text-align:center;"> <img src="/assets/img/blog/topological-qec-2/422.png" height="100"/>
</p>
{:.figure}
</div>

# Homology of a manifold

A landmark result of algebraic topology is that the homology groups of a manifold cellulation are independent of the particular cellulation.
They only depend on the underlying manifold, and will also stay the same when transforming the manifold via a homeomorphism (continuous bijection with continuous inverse) or homotopy equivalence (progressive continuous deformation of the manifold).
For instance, any cellulation of the 2-torus will have $$H_1(C_\bullet)=\mathbb{Z}_2^2$$ as its first homology group.
This was not a property of the specific hexagonal cellulation we chose, but a consequence of the fact that we had a 2D lattice with periodic boundary conditions, i.e. a cellulation of a 2-torus.

One way to formalize this theorem is by defining a different notion of homology, called **singular homology**.
Intuitively, singular homology is the homology of an infinitely fine-grained cellulation of the manifold, where we allow $$0$$-chains to be any point of the manifold, $$1$$-chains to be any curve, $$2$$-chains to be any surface, and so on.
Here are examples of singular chains (in red) on a torus:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/torus.png" height="100"/>
</p>
{:.figure}

where we have one non-trivial singular $$1$$-cycle (going around the torus) and one trivial one, whose boundary (a $$2$$-chain) is represented in light red.

The way singular homology works is by defining a chain complex whose space $$C_k$$ is the infinite-dimensional vector space of all continuous maps from the $$k$$-simplex to the manifold.
For instance, a singular $$1$$-chain of the torus would be a map from a segment (1-simplex) to a line on the torus.
A $$1$$-cycle would be a map from the segment to a loop on the torus.
The details of this construction are not too important for us.
The key point is that the homology groups of a manifold cellulation are isomorphic to the singular homology groups of the underlying manifold, and therefore to the homology groups obtained from any other cellulation of the same manifold.
In other words, the homology groups of a cell complex only depend on the underlying manifold.
For a formal version of this result, see for instance [Hatcher's book](https://pi.math.cornell.edu/~hatcher/AT/AT.pdf), Theorem 2.35.
We will denote the homology groups of a manifold $$\mathcal{M}$$ by $$H_i(\mathcal{M})$$.

The nice thing about this result is that algebraic topology has plenty of methods to calculate the homology of a manifold.
The most direct way is to take the smallest cellulation you can find of your manifold, write down boundary matrices, kernels, images, and homology groups for this cellulation, and it will tell you the homology of the manifold.
This is pure linear algebra on small matrices.

Another way is to use homotopy equivalences to map the manifold to a simpler one whose homology is easy to compute.
For instance, if the manifold can be continuously deformed to a point (we say that the manifold is **contractible**), then all its $$k$$-homology groups vanish for $$k \geq 1$$.
You can use this to prove that any $$n$$-dimensional hypercube (or equivalently, the disk $$D^n$$) has trivial homology beyond the zeroth homology (we will use this fact later in the post, when discussing open boundaries).

Another convenient calculation method is to write your manifold as a product of simpler manifolds, and use a formula (called the Künneth formula) to compute the homology of the product from the homology of the factors.
See Exercise 3 below for an example of this.

And this is only the tip of the iceberg!
There are many more tools to compute homology groups: the Mayer-Vietoris sequence (if you can decompose your manifold as a union of simpler manifolds), the long exact sequence in relative homology (see Exercise 6), spectral sequences (recently used to prove the existence of [self-correction quantum memory in 3D](https://arxiv.org/abs/2605.10943)), and so on.

Two important examples of homology calculation include the $$D$$-sphere and $$D$$-torus.
We derive their homology groups in the following two exercises.

<div class="message" markdown="1">
**Exercise 2**: Let's consider a cellulation of the $$D$$-sphere consisting of one $$0$$-cell and one $$D$$-cell attached to it. It looks like this for $$S^1$$ and $$S^2$$ respectively:

<div class="figure" style="display:flex; justify-content:center; align-items:center; gap:2rem; flex-wrap:wrap;">
    <img src="/assets/img/blog/topological-qec-2/s1-cellulation.png" height="100"/>
    <img src="/assets/img/blog/topological-qec-2/s2-cellulation.png" height="100"/>
</div>

While this is not a polytope cellulation as defined in the [previous post](/blog/2026-05-04-topological-qec/), this can be seen as a CW-complex, which generalizes our polytope-based definition (see Footnote 4 of the previous post).
Using it, prove that the homology groups of the $$D$$-sphere are given by

$$
\begin{aligned}
    H_i(S^D) = \begin{cases}
        \mathbb{Z}_2 & \text{if } i \in \{0,D\} \\
        0 & \text{otherwise}
    \end{cases}
\end{aligned}
$$

This implies for instance that all $$1$$-cycles of $$S^2$$ are boundaries, a fact that can be easily visualized:

<div class="figure" style="display:flex; justify-content:center; align-items:center; gap:2rem; flex-wrap:wrap;">
    <img src="/assets/img/blog/topological-qec-2/cycles-on-s2.png" height="100"/>
</div>

[(solution)](#solution-of-the-exercise)
</div>

<div class="message" markdown="1">
**Exercise 3**: A $$D$$-torus can be seen as the Cartesian product of $$D$$ spheres $$S^1$$:

$$
\begin{aligned}
    T^D = \underbrace{S^1 \times S^1 \times \cdots \times S^1}_{D \text{ times}}
\end{aligned}
$$

Indeed, if you think of a $$D$$-torus as the $$D$$-dimensional Euclidean space where $$0$$ and $$2\pi$$ are identified on each component, you can see any element $$x \in T^D$$ as a tuple of angles $$x=(\theta_1,\ldots,\theta_D)$$, where $$\theta_i \in [0, 2\pi)$$, or alternatively, as a tuple of points on the unit circle $$x=(e^{i\theta_1},\ldots,e^{i\theta_D})$$, which is an element of $$S^1 \times \cdots \times S^1$$.

For any manifold $$\mathcal{M}=\mathcal{M}_1 \times \ldots \times \mathcal{M}_k$$, the **Künneth formula** gives us the homology of $$\mathcal{M}$$ from the homology of the factors $$\mathcal{M}_1,\ldots,\mathcal{M}_k$$:

$$
\begin{aligned}
    H_i(\mathcal{M}) = \bigoplus_{i_1+\cdots+i_k=i} H_{i_1}(\mathcal{M}_1) \otimes \cdots \otimes H_{i_k}(\mathcal{M}_k)
\end{aligned}
$$

Using it, show that the homology groups of the $$D$$-torus are given by

$$
\begin{aligned}
    \dim(H_i(T^D)) = \binom{D}{i}
\end{aligned}
$$

for any $$i \in \{0,\ldots,D\}$$.

[(solution)](#solution-of-the-exercise)
</div>

Another cool result of homology theory applied to manifolds is the existence of combinatorial invariants of cellulations, such as the **Euler characteristic**.
Given a cell complex $$C_\bullet$$, it is defined as

$$
\begin{aligned}
    \chi = \sum_{k=0}^D (-1)^k \dim(C_k)
\end{aligned}
$$

It turns out that $$\chi$$ only depends on the manifold, and can be computed from the dimensions of the different homology groups.
Let $$b_k = \dim(H_k(C_\bullet))$$ be the dimension of the $$k$$-th homology group, also called the $$\bm{k}$$**-th Betti number**.
Then, we have

$$
\begin{aligned}
    \chi = \sum_{k=0}^D (-1)^k b_k
\end{aligned}
$$

The proof is actually not too difficult, it is a direct consequence of our definitions, plus a tiny bit of linear algebra (this is the cool thing about reducing topology to linear algebra!).
It is a good exercise to check that you have integrated all the concepts so far!

<div class="message" markdown="1">
**Exercise 4**: Show that the Euler characteristic of a cell complex can be computed from the Betti numbers of the underlying manifold, i.e. $$\chi = \sum_{k=0}^D (-1)^k b_k$$.

[(solution)](#solution-of-the-exercise)
</div>

As an example, for a connected 2D manifold, we know that $$b_0 = b_2=1$$.
Moreover, by construction of the cell complex, $$V=\dim(C_0)$$ is the number of vertices of the cellulation, $$E=\dim(C_1)$$ is the number of edges, and $$F=\dim(C_2)$$ is the number of faces.
We therefore have:

$$
\begin{aligned}
    V - E + F  = 2 - b_1
\end{aligned}
$$

For instance, the 2-sphere has $$b_1=0$$ (see Exercise 2 above), and so $$V - E + F = 2$$.
The formula was originally found in this form by Euler, when studying combinatorial invariants of polyhedra (a polyhedron can be seen as a cellulation of the sphere).
For instance, a cube has $$V=8$$ vertices, $$E=12$$ edges, and $$F=6$$ faces, and indeed we have $$8 - 12 + 6 = 2$$.

The Euler characteristic formula is frequently used in topological quantum error correction.
The following exercise gives an example in the context of the 2D color code.


<div class="message" markdown="1">
**Exercise 5**: A 2D color code is defined from any 3-valent 3-colorable cellulation of a closed connected manifold, meaning that each vertex is incident to exactly three edges, and faces can be colored with three colors such that no two adjacent faces have the same color.
Each vertex hosts a qubit, and each face hosts both an $$X$$ and a $$Z$$ stabilizer.
We denote by $$V$$ the number of vertices, $$E$$ the number of edges, and $$F$$ the number of faces of the cellulation.
An example of such a cellulation of the torus is shown in the figure below.<br><br>
**(a)**  Show that the number of logical qubits is given by

$$
\begin{aligned}
    k = V - 2\tilde{F}
\end{aligned}
$$

where $$\tilde{F}$$ is the number of **independent** faces (i.e. the rank of the map $$A$$ that sends each face to the sum of its vertices).
<br>
**(b)** Using the colorability condition, show that

$$
\begin{aligned}
    \tilde{F}=F-2,
\end{aligned}
$$

that is, there are exactly two independent relations between the faces of the cellulation.
<br>
**(c)** Deduce that

$$
\begin{aligned}
    k=V - 2F + 4
\end{aligned}
$$

<br>
**(d)** Using the valency condition, show that

$$
\begin{aligned}
    3V = 2E
\end{aligned}
$$

<br>
**(e)** Deduce that

$$
\begin{aligned}
    k= 2(2-\chi),
\end{aligned}
$$

where $$\chi = V - E + F$$ is the Euler characteristic of the manifold.
<br>
**(f)** Using the Euler characteristic formula, deduce that

$$
\begin{aligned}
    k = 2b_1
\end{aligned}
$$

[(solution)](#solution-of-the-exercise)

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/488-color-code.png" height="200"/>
</p>
{:.figure}
</div>

Where does this leave us in our quest to find useful quantum error-correcting codes?
Our goal was to find families of LDPC codes with parameters $$[[n,k,d]]$$ such that $$k$$ is non-zero and $$d$$ grows with $$n$$.
We described in the previous post a potential recipe to construct such a code: take a family of manifold cellulations and an integer $$\ell$$, and extract from it the length-2 chain complex with $$\ell$$-cells as qubits.
We now have a new guarantee on such a code: if the manifold has $$b_\ell > 0$$, then the code has $$k=b_\ell > 0$$.
If the cellulation family is constructed from a fixed manifold by progressively refining a small cellulation, then the distance will also usually grow, with the precise distance formula depending on the specific cellulation chosen.

# Manifolds with boundary and relative homology

There is still one big element missing from our discussion so far: how can we understand surface codes with open boundaries in the language of homology?
We saw in [the surface code post](/blog/2023-05-13-surface-code/) that by replacing the periodic boundaries by opposite smooth and rough boundaries, we can get a planar code with $$k=1$$.
Could we have constructed this code from purely algebraic-topological considerations, and calculated $$k$$ as the homology of a certain manifold?

It turns out that algebraic topology does provide us with exactly the right notion to describe surface codes with boundaries: **relative homology**.
Consider a manifold $$\mathcal{M}$$ (possibly with boundary) and a cellulation $$X$$ of $$\mathcal{M}$$ described by the chain complex

$$
\begin{aligned}
    C_D(X) &\xrightarrow{\partial^X_D} C_{D-1}(X) \xrightarrow{\partial^X_{D-1}} \cdots \xrightarrow{\partial^X_2} C_1(X) \xrightarrow{\partial^X_1} C_0(X)
\end{aligned}
$$

Now, consider a submanifold $$\mathcal{N} \subseteq \mathcal{M}$$ (usually, with $\mathcal{N} \subset \partial \mathcal{M}$, but not necessarily) with the induced cellulation $$A$$ obtained by taking all cells of $$X$$ that are contained in $$\mathcal{N}$$.
It is described by the chain complex

$$
\begin{aligned}
    C_D(A) &\xrightarrow{\partial^A_D} C_{D-1}(A) \xrightarrow{\partial^A_{D-1}} \cdots \xrightarrow{\partial^A_2} C_1(A) \xrightarrow{\partial^A_1} C_0(A)
\end{aligned}
$$

We can then define the **relative chain complex** as

$$
\begin{aligned}
    C_D(X,A) &\xrightarrow{\partial_D} C_{D-1}(X,A) \xrightarrow{\partial_{D-1}} \cdots \xrightarrow{\partial_2} C_1(X,A) \xrightarrow{\partial_1} C_0(X,A)
\end{aligned}
$$

where $$C_k(X,A) = C_k(X) / C_k(A)$$ is the quotient of the vector space of $$k$$-chains of $$X$$ by the subspace of $$k$$-chains of $$A$$.
Intuitively, the relative chain complex is obtained by setting the submanifold $$\mathcal{N}$$ to zero.
Elements of $$\ker(\partial_k)$$ are then called **relative $$k$$-cycles**, and elements of $$\Im(\partial_{k+1})$$ are called **relative $$k$$-boundaries**.
The **relative homology groups** are defined as the quotient of relative cycles by relative boundaries:

$$
\begin{aligned}
    H_k(X,A) = \ker(\partial_k) / \Im(\partial_{k+1}).
\end{aligned}
$$

Let's see if we can obtain the open surface code from this construction.
Let's start with the following cellulation $$X$$ of the disk $$D^2$$ (which is topologically equivalent to a square or rectangle):

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/open-boundaries-1.png" height="160"/>
</p>
{:.figure}

Note that the first homology of that cellulation is zero: as discussed before, any disk is contractible and therefore has trivial homology beyond the zeroth homology.
It should also be clear from the picture that any $$1$$-cycle in such a cellulation is the boundary of a collection of faces, so there are no non-trivial homology classes.
Now, let's consider the submanifold consisting of the left and right boundaries of the rectangle, highlighted in red in the figure below:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/open-boundaries-2.png" height="160"/>
</p>
{:.figure}

Setting those boundaries to zero, which is equivalent to removing the corresponding vertices and edges from the cell complex, then gives us the following relative complex:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/open-boundaries-3.png" height="160"/>
</p>
{:.figure}

It's exactly our surface code with rough and smooth boundaries!
So, how can we show that the first homology group of this relative complex has dimension one?
As before, it is possible to show that the relative homology group is also cellulation-independent, meaning that we can simply take the smallest cellulation of the square and compute its relative homology using linear algebra.

Let's pick the following cellulation, and remove the left and right boundaries:

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/small-square.png" height="100"/>
</p>
{:.figure}

The resulting relative complex has zero vertices, two edges, and one face, giving the complex

$$
\begin{aligned}
    \mathbb{Z}_2 &\xrightarrow{\partial_2} \mathbb{Z}_2^2 \xrightarrow{\partial_1} 0
\end{aligned}
$$

with $$\partial_2 = \begin{pmatrix} 1 \\ 1 \end{pmatrix}$$ and $$\partial_1 = 0$$.
The space of relative $$1$$-cycles is then given by $$\ker(\partial_1) = \mathbb{Z}_2^2$$, and the space of relative $$1$$-boundaries is given by $$\Im(\partial_2) = \mathbb{Z}_2$$, so the first relative homology group has dimension one, as expected.

The following exercise shows that this idea generalizes very well to any $$D$$-dimensional hypercube, by setting two opposite sides to zero.
This builds the open boundary version of the $$D$$-dimensional toric code, which has $$k=1$$ for any dimension $$D$$ (the [3D surface code](https://arxiv.org/abs/1801.04255) is an example of this).

<div class="message" markdown="1">
**Exercise 6**: Consider the $$D$$-dimensional hypercube $$[0,1]^D$$, and the submanifold consisting of two opposite faces of the hypercube, such as $$\{(0,x_2,\ldots,x_D) : x_2,\ldots,x_D \in [0,1]\}$$ and $$\{(1,x_2,\ldots,x_D) : x_2,\ldots,x_D \in [0,1]\}$$.
Instead of trying to visualize a $$D$$-dimensional cellulation of the hypercube, we will use here an alternative approach to compute the first homology group.

While we would like to have a simple formula like $$H_k(X,A)=H_k(X)/H_k(A)$$, this is unfortunately not always the case.
But there is an important result of relative homology (see [Hatcher](https://pi.math.cornell.edu/~hatcher/AT/AT.pdf), Theorem 2.16), which can be used to calculate $$H_k(X,A)$$ using the other homology groups $$H_i(X,A)$$, $$H_i(X)$$ and $$H_i(A)$$.
The result is the existence of the following **long exact sequence** of homology groups:

$$
\begin{aligned}
    \cdots \to H_k(A) \xrightarrow{i_k} H_k(X) \xrightarrow{j_k} H_k(X,A) \xrightarrow{\delta_k} H_{k-1}(A) \xrightarrow{i_{k-1}} H_{k-1}(X) \to \cdots
\end{aligned}
$$

where $$i_k$$ is the map induced by the inclusion of $$A$$ in $$X$$, $$j_k$$ is the map induced by the projection of $$C_\bullet(X)$$ to the quotient $$C_\bullet(X)/C_\bullet(A)$$, and $$\delta_k$$ is a map called the **connecting homomorphism**.
An **exact sequence** is a sequence of vector spaces and linear maps between them, such that the image of each map is equal to the kernel of the next map.
Exact sequences are a fundamental tool in algebraic topology, which allow us to calculate homology groups in many situations.

Let's use the sequence above to calculate the first relative homology group of our hypercube with the two opposite faces set to zero.

**(a)** Let $$X$$ be any cellulation of the hypercube, and $$A$$ be the induced cellulation on the two opposite faces.
Show that we have the following sequence around $$H_1(X,A)$$:

$$
\begin{aligned}
    \cdots \to 0 \xrightarrow{j_1} H_1(X,A) \xrightarrow{\delta_1} \mathbb{Z}_2^2 \xrightarrow{i_0} \mathbb{Z}_2 \to \cdots
\end{aligned}
$$

where $$j_1$$ is the zero map, $$i_0(a,b)=a+b$$, and $$\delta_1$$ is injective.

**(b)** Using the rank-nullity theorem, deduce that $$\dim(H_1(X,A))=1$$.

[(solution)](#solution-of-the-exercise)
</div>

# Conclusion

Well done for surviving this long post!
You're now familiar with one of the most profound notions of modern mathematics, and how to use it to concretely calculate the number of logical qubits of a topological quantum code!
This should already give you a good understanding of why the 2D surface code magically worked so well, and how to generalize it to other manifolds and dimensions.

However, we're still missing a few things.
We saw that the homology of our CSS chain complex really tells us about the $$X$$ logicals of the code.
This is enough to tell us how many logical qubits we have, but to characterize $$Z$$ logicals as well, we need to introduce a new notion, called **cohomology**.
We can then show for instance that for a manifold cellulation, $$Z$$ logicals are given by the homology of the **dual lattice** (remember the dual lattice from the [surface code post](/blog/2023-05-13-surface-code/)?).
We can also show that there exists a basis of logical operators such that each logical $$X$$ operator anticommutes with exactly one logical $$Z$$ operator, and commutes with all the others.
Cohomology has also become trendy in QEC recently due to the [construction of certain types of logical gates](https://arxiv.org/abs/2410.16250) using cup products, a concept that belongs to cohomology theory.
There are therefore many reasons to learn cohomology, and I am looking forward to telling you all about it in the next post!


<!-- **Acknowledgment**: Thanks to...
{:.message} -->

# Resources

Algebraic topology for quantum error correction:
* [PhD Thesis: Quantum error correction in three dimensions and beyond, Chapter 3](https://discovery.ucl.ac.uk/id/eprint/10223041/7/phd-thesis-final-corrected.pdf), by myself.
* [PhD Thesis: Homological quantum codes beyond the toric code, Chapter 2](https://arxiv.org/abs/1802.01520), by Nikolas Breuckmann.
* [Topological Codes and Computation](https://drive.google.com/file/d/1AB0Kd5JV5TzqwvR4VysrJt-F3GVin2S4/view), by Dan Browne.

General algebraic topology:
* [Algebraic Topology](https://pi.math.cornell.edu/~hatcher/AT/AT.pdf), by Allen Hatcher (the bible of the subject).



















## Solution of the exercise

<div class="message" markdown="1">
**Exercise 1**: **(a)** Let us denote the basis of $$C_1$$ by $$e_1,e_2,e_3,e_4$$, the unique basis vector of $$C_0$$ by $$v$$, and the unique basis vector of $$C_2$$ by $$f$$.
Reading the diagram as a chain complex, the face $$f$$ is mapped to the sum of the four edges, and each edge is mapped to $$v$$.
Thus, we have

$$
\begin{aligned}
    C_2 \xrightarrow{\partial_2} C_1 \xrightarrow{\partial_1} C_0
\end{aligned}
$$

with

$$
\begin{aligned}
    \partial_2 &=
    \begin{pmatrix}
        1 \\
        1 \\
        1 \\
        1
    \end{pmatrix}, &
    \partial_1 &=
    \begin{pmatrix}
        1 & 1 & 1 & 1
    \end{pmatrix}.
\end{aligned}
$$

The chain complex condition is satisfied since

$$
\begin{aligned}
    \partial_1 \partial_2 =
    \begin{pmatrix}
        1 & 1 & 1 & 1
    \end{pmatrix}
    \begin{pmatrix}
        1 \\
        1 \\
        1 \\
        1
    \end{pmatrix}
    = 0
\end{aligned}
$$

over $$\mathbb{Z}_2$$.
This chain complex is **not** a cell complex: an edge cannot have a boundary equal to a single vertex.
If both endpoints of an edge were identified with the same vertex, the boundary would instead be $$v+v=0$$.
<br>
**(b)** The space of $$1$$-cycles is

$$
\begin{aligned}
    \ker(\partial_1)
    =
    \left\{
        \begin{pmatrix}
            x_1 \\
            x_2 \\
            x_3 \\
            x_4
        \end{pmatrix}
        \in \mathbb{Z}_2^4 \;:\; x_1+x_2+x_3+x_4=0
    \right\}.
\end{aligned}
$$

This is the even-parity subspace of $$\mathbb{Z}_2^4$$, which has dimension $$3$$.
For instance, it is generated by

$$
\begin{aligned}
    \begin{pmatrix} 1 \\ 1 \\ 0 \\ 0 \end{pmatrix},
    \begin{pmatrix} 1 \\ 0 \\ 1 \\ 0 \end{pmatrix},
    \begin{pmatrix} 1 \\ 0 \\ 0 \\ 1 \end{pmatrix}.
\end{aligned}
$$

**(c)** The space of $$1$$-boundaries is

$$
\begin{aligned}
    \Im(\partial_2)
    =
    \mathrm{span}\left\{
        \begin{pmatrix}
            1 \\
            1 \\
            1 \\
            1
        \end{pmatrix}
    \right\},
\end{aligned}
$$

so it has dimension $$1$$.
<br>
**(d)** We can now compute the dimension of the first homology group:

$$
\begin{aligned}
    \dim H_1(C_\bullet)
    = \dim \ker(\partial_1) - \dim \Im(\partial_2)
    = 3 - 1
    = 2.
\end{aligned}
$$

Equivalently, $$H_1(C_\bullet) \cong \mathbb{Z}_2^2$$.
<br>
**(e)** The corresponding CSS code has one qubit for each basis element of $$C_1$$, so $$n=4$$.
The two stabilizer-check matrices are

$$
\begin{aligned}
    H_X =
    \begin{pmatrix}
        1 & 1 & 1 & 1
    \end{pmatrix},
    \qquad
    H_Z =
    \begin{pmatrix}
        1 & 1 & 1 & 1
    \end{pmatrix}.
\end{aligned}
$$

In other words, the stabilizer group is generated by $$XXXX$$ and $$ZZZZ$$.
The number of logical qubits is given by the dimension of the first homology group, which we computed to be $$2$$:

$$
\begin{aligned}
    k = \dim H_1(C_\bullet) = 2.
\end{aligned}
$$

Finally, no weight-one Pauli operator can be logical, since any single-qubit $$X$$ anticommutes with $$ZZZZ$$, and any single-qubit $$Z$$ anticommutes with $$XXXX$$.
However, weight-two operators such as $$X_1X_2$$ and $$Z_1Z_2$$ commute with all stabilizers and are not themselves stabilizers.
Therefore $$d=2$$, and the code is the well-known $$[[4,2,2]]$$ code.

[(Back to section)](#qec-interpretation)
</div>

<div class="message" markdown="1">
**Exercise 2**: The complex described in the exercise has one $$0$$-cell and one $$D$$-cell, so its vector spaces are

$$
\begin{aligned}
    C_i =
    \begin{cases}
        \mathbb{Z}_2 & \text{if } i \in \{0,D\} \\
        0 & \text{otherwise}.
    \end{cases}
\end{aligned}
$$

and the chain complex (with $$C_{-1}$$ and $$C_{D+1}$$ included) can be written as

$$
\begin{aligned}
    0 &\xrightarrow{\partial_{D+1}} \mathbb{Z}_2 \xrightarrow{\partial_D} 0 \xrightarrow{\partial_{D-1}} \cdots \xrightarrow{\partial_2} 0 \xrightarrow{\partial_1} \mathbb{Z}_2 \xrightarrow{\partial_0} 0
\end{aligned}
$$

Moreover, all boundary maps are zero.
Indeed, if $$D > 1$$, either their domain or their codomain is the zero vector space.
For $$D=1$$, the unique $$1$$-cell $$e$$ has the unique vertex $$v$$ at each endpoint, meaning that $$\partial_1(e)=v+v=0$$.

Therefore, for $$i=0$$, we have

$$
\begin{aligned}
    H_0(S^D)
    = \ker(\partial_0)/\Im(\partial_1)
    = C_0 / 0
    = \mathbb{Z}_2.
\end{aligned}
$$

Similarly, for $$i=D$$, we get

$$
\begin{aligned}
    H_D(S^D)
    = \ker(\partial_D)/\Im(\partial_{D+1})
    = C_D / 0
    = \mathbb{Z}_2.
\end{aligned}
$$

For all other values of $$i$$, we have $$C_i=0$$, and hence $$H_i(S^D)=0$$.
Thus

$$
\begin{aligned}
    H_i(S^D) =
    \begin{cases}
        \mathbb{Z}_2 & \text{if } i \in \{0,D\} \\
        0 & \text{otherwise}.
    \end{cases}
\end{aligned}
$$

[(Back to section)](#homology-of-a-manifold)
</div>

<div class="message" markdown="1">
**Exercise 3**: From Exercise 2, applied to $$S^1$$, we have

$$
\begin{aligned}
    H_i(S^1) =
    \begin{cases}
        \mathbb{Z}_2 & \text{if } i \in \{0,1\} \\
        0 & \text{otherwise}.
    \end{cases}
\end{aligned}
$$

Since $$T^D=(S^1)^D$$, the Künneth formula gives

$$
\begin{aligned}
    H_i(T^D)
    =
    \bigoplus_{i_1+\cdots+i_D=i}
    H_{i_1}(S^1)\otimes \cdots \otimes H_{i_D}(S^1).
\end{aligned}
$$


Since tensoring with the zero vector space gives zero, the only nonzero summands occur when each $$i_\ell$$ is either $$0$$ or $$1$$.
Thus,

$$
\begin{aligned}
    H_i(T^D)
    =
    \bigoplus_{i_1,\ldots,i_D \in \{0,1\} \atop i_1+\cdots+i_D=i}
    H_{i_1}(S^1)\otimes \cdots \otimes H_{i_D}(S^1).
\end{aligned}
$$

Since $$\mathbb{Z}_2 \otimes \mathbb{Z}_2 \cong \mathbb{Z}_2$$ (the tensor product of two one-dimensional vector spaces is a one-dimensional vector space), $$H_i(T^D)$$ is a direct sum of copies of $$\mathbb{Z}_2$$, one for each binary $$D$$-tuple $$(i_1,\ldots,i_D)$$ such that $$i_1+\cdots+i_D=i$$.
The number of binary $$D$$-tuples $$(i_1,\dots,i_D)$$ with $$i_1+\cdots+i_D=i$$
is exactly the number of ways to choose which $$i$$ of the $$D$$ entries are equal to $$1$$, namely $$\binom{D}{i}$$.
Hence,

$$
\begin{aligned}
    H_i(T^D)
    =
    \mathbb{Z}_2^{\binom{D}{i}},
\end{aligned}
$$

and in particular

$$
\begin{aligned}
    \dim(H_i(T^D)) = \binom{D}{i}.
\end{aligned}
$$

For example, for the usual $$2$$-torus, this gives $$\dim(H_0(T^2))=1$$, $$\dim(H_1(T^2))=2$$, and $$\dim(H_2(T^2))=1$$, as expected.

[(Back to section)](#homology-of-a-manifold)
</div>

<div class="message" markdown="1">
**Exercise 4**: Let us denote by $$Z_k=\ker(\partial_k)$$ the space of $$k$$-cycles and by $$B_k=\Im(\partial_{k+1})$$ the space of $$k$$-boundaries.
By definition, the $$k$$-th Betti number is

$$
\begin{aligned}
    b_k = \dim(H_k(C_\bullet)) = \dim(Z_k) - \dim(B_k).
\end{aligned}
$$

On the other hand, by the rank-nullity theorem applied to $$\partial_k:C_k \to C_{k-1}$$, we have

$$
\begin{aligned}
    \dim(C_k)
    &= \dim(\ker(\partial_k)) + \dim(\Im(\partial_k)) \\
    &= \dim(Z_k) + \dim(B_{k-1}).
\end{aligned}
$$

where we use the convention $$B_{-1}=0$$.
We can now plug this into the definition of the Euler characteristic:

$$
\begin{aligned}
    \chi
    &= \sum_{k=0}^D (-1)^k \dim(C_k) \\
    &= \sum_{k=0}^D (-1)^k \dim(Z_k)
    + \sum_{k=0}^D (-1)^k \dim(B_{k-1}).
\end{aligned}
$$

In the second sum, we can shift the index by writing $$j=k-1$$:

$$
\begin{aligned}
    \sum_{k=0}^D (-1)^k \dim(B_{k-1})
    &= \sum_{j=-1}^{D-1} (-1)^{j+1} \dim(B_j) \\
    &= - \sum_{j=0}^{D} (-1)^j \dim(B_j),
\end{aligned}
$$

where we used $$B_{-1}=0$$ and $$B_D=\Im(\partial_{D+1})=0$$.
Therefore

$$
\begin{aligned}
    \chi
    &= \sum_{k=0}^D (-1)^k \left(\dim(Z_k)-\dim(B_k)\right) \\
    &= \sum_{k=0}^D (-1)^k b_k.
\end{aligned}
$$

This proves that the Euler characteristic can be computed directly from the Betti numbers.

[(Back to section)](#homology-of-a-manifold)
</div>

<div class="message" markdown="1">
**Exercise 5**: **(a)** Let $$A$$ be the face-vertex incidence matrix of the cellulation.
Since each face hosts one $$X$$ stabilizer and one $$Z$$ stabilizer, the parity-check matrices of the color code are both equal to $$A$$.
The number of physical qubits is $$n=V$$, and the number of independent $$X$$ checks and $$Z$$ checks is $$\tilde{F}=\mathrm{rank}(A)$$.
Therefore

$$
\begin{aligned}
    k
    = n - \mathrm{rank}(H_X) - \mathrm{rank}(H_Z)
    = V - 2\tilde{F}.
\end{aligned}
$$

**(b)** Let us color the faces red, green, and blue.
Since the cellulation is 3-valent and 3-colorable, each vertex is incident to exactly one face of each color.
Therefore, the sum of all red faces and the sum of all green faces have the same boundary on vertices: both are equal to the all-one vector of vertices.
This gives a relation

$$
\begin{aligned}
    A \left ( \sum_{f \text{ red}} f + \sum_{f \text{ green}} f \right ) = 0.
\end{aligned}
$$

Similarly, the red-blue and green-blue sums give relations.
Only two of those three relations are independent, since the sum of all three is zero.
Hence, $$\tilde{F} \leq F-2$$.

Conversely, there are no other relations.
To see this, consider a relation between faces, and assume that the relation involves a blue face $$f$$.
Let $$v$$ be a vertex of $$f$$.
To cancel $$v$$, we must have either the green face or the red face incident to $$v$$ in the relation (but not both).
Assume we have the green face.
Let's denote by $$e$$ the edge between the red and green face, and by $$v'$$ the other end of $$e$$.
Then $$v'$$, which is connected to the green face, must also be canceled, either by the red one or the blue one.
Since we cannot choose the red one (which is incident to $$v$$), we must choose the blue one.
Repeating this argument, we see that the relation must contain all blue faces.
Similarly, if a relation contains a red (green) face, it must contain all red (green) faces.
Therefore, any relation is a linear combination of the three relations we found before.

Hence, there are exactly two independent relations, and

$$
\begin{aligned}
    \tilde{F}=F-2.
\end{aligned}
$$

**(c)** Plugging this into the expression from part (a), we get

$$
\begin{aligned}
    k = V - 2(F-2) = V - 2F + 4.
\end{aligned}
$$

**(d)** Since the cellulation is 3-valent, summing the valencies over all vertices gives $$3V$$.
This counts each edge twice, once for each endpoint.
Therefore

$$
\begin{aligned}
    3V = 2E.
\end{aligned}
$$

**(e)** Using $$3V=2E$$, we can rewrite the Euler characteristic as

$$
\begin{aligned}
    \chi
    = V - E + F
    = V - \frac{3V}{2} + F
    = F - \frac{V}{2}.
\end{aligned}
$$

Hence $$2\chi = 2F - V$$, and the expression from part (c) becomes

$$
\begin{aligned}
    k
    = V - 2F + 4
    = 4 - 2\chi
    = 2(2-\chi).
\end{aligned}
$$

**(f)** For a connected closed 2D cellulation, we have $$b_0=b_2=1$$.
The Euler characteristic formula gives

$$
\begin{aligned}
    \chi = b_0 - b_1 + b_2 = 2-b_1.
\end{aligned}
$$

Therefore

$$
\begin{aligned}
    k = 2(2-\chi) = 2b_1.
\end{aligned}
$$

For instance, on a torus $$b_1=2$$, so the corresponding color code encodes $$k=4$$ logical qubits.

<p style="text-align:center;">
    <img src="/assets/img/blog/topological-qec-2/488-color-code.png" height="200"/>
</p>
{:.figure}

[(Back to section)](#homology-of-a-manifold)
</div>

<div class="message" markdown="1">
**Exercise 6**: **(a)** The hypercube $$X=[0,1]^D$$ has one connected component and is contractible, so its homology groups are

$$
\begin{aligned}
    H_0(X) = \mathbb{Z}_2,
    \qquad
    H_k(X)=0 \text{ for } k\geq 1.
\end{aligned}
$$

The submanifold $$A$$ is the disjoint union of two $$(D-1)$$-dimensional hypercubes, and its zeroth homology is therefore $$H_0(A) = \mathbb{Z}_2^2$$.

Plugging this into the long exact sequence around $$H_1(X,A)$$ gives

$$
\begin{aligned}
    \cdots \to
    H_1(X)
    \xrightarrow{j_1}
    H_1(X,A)
    \xrightarrow{\delta_1}
    H_0(A)
    \xrightarrow{i_0}
    H_0(X)
    \to \cdots,
\end{aligned}
$$

and therefore

$$
\begin{aligned}
    \cdots \to
    0
    \xrightarrow{j_1}
    H_1(X,A)
    \xrightarrow{\delta_1}
    \mathbb{Z}_2^2
    \xrightarrow{i_0}
    \mathbb{Z}_2
    \to \cdots.
\end{aligned}
$$

The map $$j_1$$ is the zero map because its domain is the zero vector space.
By exactness, we have

$$
\begin{aligned}
    \ker(\delta_1) = \Im(j_1)=0,
\end{aligned}
$$

so $$\delta_1$$ is injective.

It remains to identify $$i_0$$.
The two basis elements of $$H_0(A)= \mathbb{Z}_2^2$$ correspond to the two connected components of $$A$$.
Since both components lie in the same connected component of $$X$$, the inclusion sends both of them to the unique generator of $$H_0(X)= \mathbb{Z}_2$$.
Hence

$$
\begin{aligned}
    i_0(a,b)=a+b.
\end{aligned}
$$

**(b)** Since the sequence is exact, we have

$$
\begin{aligned}
    \Im(\delta_1)=\ker(i_0).
\end{aligned}
$$

and therefore

$$
\begin{aligned}
    \dim \Im(\delta_1) = \dim\ker(i_0) = 1.
\end{aligned}
$$

Since $$\delta_1$$ is injective, $$\dim\ker(\delta_1)=0$$, so by rank-nullity we have:

$$
\begin{aligned}
    \dim H_1(X,A)=\dim \Im(\delta_1) + \dim\ker(\delta_1) = 1 + 0 = 1.
\end{aligned}
$$

This recovers the expected single logical qubit for the open-boundary $$D$$-dimensional toric code.

[(Back to section)](#manifolds-with-boundary-and-relative-homology)
</div>

[^1]: An $$R$$-module is a generalization of a vector space in which the coefficients form a ring $$R$$ rather than a field.
    The key difference is that, in a general ring, nonzero elements need not be invertible.
    An archetypal example is the $$\mathbb{Z}$$-module $$\mathbb{Z}^n$$, the set of $$n$$-dimensional vectors with integer coefficients.
    This is a module but not a vector space, since most elements of $$\mathbb{Z}$$, apart from $$1$$ and $$-1$$, do not have an inverse in $$\mathbb{Z}$$.
    Many familiar tools from linear algebra, such as Gaussian elimination or dimension-counting arguments, do not carry over directly to general $$R$$-modules.
    However, algebraic topology is often developed using $$\mathbb{Z}$$-modules, because integer coefficients keep track of cycles with integer multiplicities (think of loops going around multiple times around a hole) and can capture phenomena, such as torsion, that disappear over fields.
    In contrast, when restricting to field coefficients such as $$\mathbb{Z}_2$$, many results from algebraic topology and homological algebra become much simpler.

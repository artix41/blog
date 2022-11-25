---
title: Simulating Large Hamiltonians with Symmetries
tags: [quantum-computing]
description: >
---

For one of my projects at 1QBit, I got interested in simulating the p-spin model, a model invented
by Hidetoshi Nishimori to study phase transitions in quantum annealing. In short, it consists of 
the Hamiltonian

$$
H(s,\lambda) = -s \lambda N \left(\frac{1}{N} \sum_{i=1}^N \sigma^z_i\right)^p 
+ s (1 - \lambda)  N \left(\frac{1}{N} \sum_{i=1}^N \sigma^x_i\right)^k
- (1 - s) \sum_{i=1}^N \sigma^x_i
$$

where $$N$$ is the number of qubits, and $$p \geq 3, \: k \geq 2$$ are integers. 

The goal is to solve the time-dependent Schrodinger's equation

$$
H(t) \psi(t) = i \frac{d\psi}{dt}
$$

starting in the ground-state of $$H(0)=\sum_{i=1}^N \sigma^x_i$$. If you try to solve this equation
numerically in this format, using for instance the library [QuTiP](http://qutip.org/), you will 
quickly encounter a wall in dimensionality due to the exponential scaling of the wave function: in my experiments,
I couldn't get beyond 12 qubits. 

However, if you read papers on the p-spin model, you will frequently see experiments with more than
100 qubits! How did they reach such a scale? As often in physics, by exploiting the symmetries 
of the Hamiltonian!

In this blog post, I want to give you both an intuitive and formal understanding of the notion
of symmetry in quantum mechanics, and show how it can help to simulate large quantum systems 
on a classical computer.

# Block-diagonalization and symmetry

## The notion of symmetry

What does it mean for a Hamiltonian to possess a symmetry? Formally, a symmetry is an operator $$S$$
that commutes with the Hamiltonian:

$$
[H,S]=0
$$

or equivalently that commutes with the unitary operator for the evolution

$$
[e^{iHt}, S]=0
$$

since $$[H,S]=0 \Rightarrow [H^k, S]=0 \Rightarrow [\sum \frac{i^k t^k}{k!} H^k, S]=0
\Rightarrow [e^{iHt}, S]=0$$.

This definition has three immediate consequences that relate to our intuitive
understanding of symmetries:
 
 * If $$S$$ is a Hermitian operator (representing an observable), this observable will be conserved
during the evolution of the system under $$H$$. For instance, if we initialize our system at an eigenstate $$\psi_s$$ of $$S$$, 
i.e. $$S \psi_s = s\psi_s$$ then at any time $$t$$, $$\psi(t)=e^{iHt}\psi_s$$ will still have the 
same value of $$S$$:

$$
S\psi(t)=Se^{iHt}\psi_s = e^{iHt} S \psi_s = s e^{iHt} \psi_s = s \psi(t)
$$

 * If $$S$$ is a unitary (or antiunitary) operator, applying it to a state doesn't change its energy:
 
$$
\langle \psi | S^{\dagger} H S | \psi \rangle = \langle \psi | S^{\dagger} S H | \psi \rangle
= \langle \psi | H | \psi \rangle
$$

For instance, $$H=\sum \sigma_i^x$$

## Block diagonalization of Hamiltonians


# Applications

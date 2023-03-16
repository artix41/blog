---
title: Directions in QEC
tags: [quantum-computing]
description: >
comments: true
related_posts:
    -
---

QIP 2023 finished recently and it was a good occasion to take a step back from my different projects and think about where the field is going. My goal in this article is to do both a literature review of the most important papers and advances that came out in the past few years, and give my perspective of where the field is going. I will only assume basic QEC knowledge and will try to make it as accessible as possible to quantum computer scientists from different subfields.

## Making LDPC codes practical

The most impressive progress in quantum error correction of the past few years probably lies within the realm of low-density parity-check (LDPC) codes. Quantum LDPC codes are defined by the low-weight of their stabilizers: each stabilizer only connects a constant number of qubits and each qubits is only connected to a constant number of stabilizers. This is the case of all topological codes, such as the surface codes, but the main characteristic of LDPC codes is that we remove the geometric locality from the requirements: in a general LDPC code, a stabilizer can connect qubits from arbitrary places of the lattice. This makes them hard to implement in practice.

<!-- Why do we care about the low density of the stabilizers, you may ask? My main intuition is that the lower the weight of a stabilizer, the more informative it will be on the position of the errors. If the weight of all the stabilizers quickly grow with the size of the code, it might be hard to accurately decode the errors. And indeed, high-density parity-check (HDPC) codes such as the Bacon-Shor code often fail to have a threshold. Moreover, syndrome extraction circuits tend to grow with the weight of the stabilizers, so extracting them fault-tolerantly would require a large overhead. Finally, classical LDPC codes are currently state-of-the-art in satellite communication, so it's probably worth looking into them. Despite this, it's important to recognize that fault-tolerant HDPC codes are not completely out-of-reach, with for instance a recent paper presented at QIP that found a low-overhead HDPC code based on concatenation [cite]. But let's remain low-density for this section. -->

So what has happened in the LDPC realm in the past few years? The most important innovation is the construction of *good* LDPC codes, that is codes with a number of logical qubits and distance growing linearly with the number of physical qubits of the code. This is important because such codes can dramatically reduce the overhead of quantum error correction, and the construction has been an open problem in the field for as long as QEC was born. The main construction is what is now called *Tanner codes*, which are based on *balanced product codes*, themselves derived from *hypergraph product codes*.

Now that good codes have been discovered, what are the next open problems? Making them practical! For that, three directions need to be explored: embedding in almost-local architectures, fast decoding and gate construction.

### 1. Embedding in almost-local architectures

The main drawback of LDPC code is the lack of geometric locality: the constructions are indeed highly non-local and require many long-range connections. Can we do better? Unfortunately, not really. Two papers by Nouedyn Baspin et al (presented respectively at QIP 2022 and 2023) showed that non-locality (and a lot of it!) is necessary for good LDPC codes.

So what can we do from there? One solution is to abandon locality all-together and put all our efforts in building non-local quantum devices such as photonic, trapped ions, cold atoms, or silicon-based qubits with shuttling abilities quantum computers. Another solution proposed recently is to try to concatenate LDPC codes with other codes. Why would it work?

Nouedyn's papers. Swap gates, surface code concatenation.

### 2. Fast decoding

Pre-factor problem

### 3. Gate construction

Cohen et al., Huang et al., Fold-transveral gates

Problem: non-Clifford gates, more general transversal gate construction.

## Understanding space-time codes better

Appearing in Gottesman's recent perspective, Barbara Terhal's talk at QIP and many posters, space-time codes are all rage those days! What do I mean by space-time codes? Codes whose properties are defined by the way it evolves in time. The main examples are Floquet codes and cluster states beyond foliation.

Floquet codes: general formalism and definition.

Already better for Majorana fermions.

Cluster states, beyond foliation

Subsystem codes: what else can we do?

## Understanding bosonic codes better

Stabilizer formalism for bosonic codes. Cat states based on group theory.

## Finding alternative to magic state distillation

Just-in-time decoding. Fractal codes.

## Evaluating existing codes and fault-tolerant schemes at the circuit level

Craig Gydney's Stim has made very

## Understanding biased noise better

Since one of my main thesis topic is biased noise, I could not avoid mentioning biased noise!

## Implementing quantum codes in experiments

## References


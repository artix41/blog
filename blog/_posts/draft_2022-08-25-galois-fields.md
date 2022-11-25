---
title: Galois fields and quantum error correction
tags: [quantum-computing]
description: >
comments: true
related_posts: 
    - 
---

We ha

## What is a field?


## What is a Galois field?

Up to an isomorphism, there is a unique finite field, also called Galois fields, of cardinal $$p^k$$. 
If $$k=1$$, this field is simply $$\mathbb{Z}_p$$, otherwise we call it $$GF(p^k)$$ and it can be a bit more complicated to define.

## What can we do with it?

Linear algebra

Counter-example: linear independence in $$Z_4$$ makes no sense, some elements can be linearly dependent with themselves (see Mary Wootters video on the topic)

## What does it have to do with quantum error correction?

However, there is an absolute remarkable fact: when defining the basic operations in a particular way, we can see the Pauli group as the Galois field of order 4, namely $$GF(4)$$!

## Important example: parity-check matrix over $$GF(4)$$
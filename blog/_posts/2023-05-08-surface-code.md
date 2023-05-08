---
title: An interactive introduction to the surface code
tags: [quantum-computing]
description: >
comments: true
related_posts:
    -
---

<script>
    // Repair modulo bug with negative numbers
    Number.prototype.mod = function (n) {
        "use strict";
        return ((this % n) + n) % n;
    };

    // Turn GUI into png
    function drawImage(gui, id, download) {
        gui.animate();

        let dataURL = gui.renderer.domElement.toDataURL();
        let img = new Image();
        img.src = dataURL;

        // Draw the Image object onto a new canvas

        requestAnimationFrame(function() {
            let newCanvas = document.createElement('canvas');
            let oldCanvas = document.getElementById(id).children[0];

            newCanvas.width = oldCanvas.offsetWidth;
            newCanvas.height = oldCanvas.offsetHeight;

            let ctx = newCanvas.getContext('2d');
            ctx.drawImage(img, 0, 0, newCanvas.width, newCanvas.height);

            oldCanvas.remove();

            let parent = document.getElementById(id);
            parent.appendChild(newCanvas);

            if (download) {
                let savedDataURL = newCanvas.toDataURL();
                let link = document.createElement('a');
                link.download = id + '.png';
                link.href = savedDataURL;
                document.body.appendChild(link);
                link.click();
            }
        });
    }

    const keycode = {
        'decode': -1, 'random': -1, 'remove': -1, 'opacity': -1,
        'x-logical': -1, 'z-logical': -1, 'x-error': 0, 'z-error': -1
    };
</script>

Last July, the quantum team at Google released a [milestone paper](https://arxiv.org/abs/2207.06431), in which they show the first experimental demonstration of quantum error correction below threshold. What this means is that their experiment reached noise levels that are low enough such that by increasing the size of their code, they progressively reduced the number of errors in the encoded qubit. And how did they achieve such a milestone? You guessed it, by using the surface code to protect their qubits! The reasons they chose this code are plentiful: it can be layed down on a 2D lattice, it has a very high threshold compared to other codes, it doesn't require too many qubits in its smallest instances, etc. And Google is far from the only company considering the surface code (or some of its variants) as part of their their fault-tolerant architecture!

Apart from its experimental relevance, the surface code is also one of the most beautiful ideas of quantum computing, and if you ask me, of all physics. Inspired by the condensed matter concepts of topological order and anyonic particle, it was discovered by Alexei Kitaev in 1997, in a [paper](https://arxiv.org/abs/quant-ph/9707021) in which he also introduces the idea of topological quantum computing. And indeed, the surface code has deep connections to many areas of maths and physics. For instance, in condensed matter theory, the surface code (more often called *toric code* in this context[^1]) is used as a prime example of topological phase of matter and is related to the more general family of spin liquids. While this post will be mainly concerned with the quantum error correction properties of the surface code, my friend Dominik Kufel wrote an excellent [complementary post](https://dom-kufel.github.io/blog/2023-04-15-toric_code-intro/) which describes the condensed matter perspective.

Finally, the surface code is the simplest example to illustrate the more general concept of topological quantum error-correction. While we will only consider surface codes defined on a 2D square lattice here, they can actually be generalized to the cellulation of any manifold in any dimension (including hyperbolic!). Concepts from algebraic topology (such as homology and cohomology groups, chain complexes, etc.) can then be used to understand and analyze those codes. While we will take a brief glance at topology here, I am planning to write a separate blog post fully dedicated to the maths behind topological quantum error-correction. The goal of this post is to gain a first intuitive understanding of the surface code.

You got it, I love the surface code, and I'm super excited to talk about it in this post!
To do this code the honor it deserves, I've decided to make use of the [interactive code visualizer](gui.quantumcodes.io) I have been developing for some time with my collaborator Eric Huang. I've embedded it in this post[^2], and you will therefore be able to play with some of the lattices presented here (e.g. by inserting errors and stabilizers, visualizing decoding, etc.). I hope you'll enjoy!

## Definition of the surface code

The surface code can be defined on a square grid of size $$L \times L$$, where qubits sit on the edges, as shown here for $$L=4$$:

<!-- <div id="surface-code-lattice" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-lattice';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init();

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-lattice.png"/>
</p>
{:.figure}

The code is easier to analyze at first when considering periodic boundary conditions. Therefore, in the picture above, we identify the left-most and right-most qubits, as well as the top-most and bottom-most qubits. The grid is therefore equivalent to a pac-man grid, or in topological terms, a **torus**. A torus as you might imagine it can be obtained from the grid by wrapping it around top to bottom (gluing the top and bottom edges together), making a cylinder, then left to right (gluing the left and right edges together) to finish the torus:

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/torus-grid.png" height="200"/>
</p>
{:.figure}

The next step is then to describe the **stabilizers** of the code. The surface code is indeed an example of stabilizer codes and can therefore be completely specified by its stabilizer group (if you need a reminder, don't hesitate to read my [blog series]() on the stabilizer formalism). In our case, we have two types of stabilizer generators: the **vertex stabilizers**, defined on every vertex of the lattice as a cross of four $$Z$$ operators, and the **plaquette stabilizers**, defined on every face as a square of four $$X$$ operators. Examples of vertex and plaquette stabilizers are shown below, where *red* means $$X$$ and *blue* mean $$Z$$.

<!-- <div id="surface-code-stabilizers" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-stabilizers';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([1, 4], 'X');
    gui.code.insertError([0, 5], 'X');
    gui.code.insertError([1, 6], 'X');
    gui.code.insertError([2, 5], 'X');

    gui.code.insertError([3, 2], 'Z');
    gui.code.insertError([4, 3], 'Z');
    gui.code.insertError([4, 1], 'Z');
    gui.code.insertError([5, 2], 'Z');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-stabilizers.png"/>
</p>
{:.figure}

To define a valid code, $$X$$ and $$Z$$ stabilizers must commute. Since they are Pauli operators, it means that they must always intersect on an even number of qubits. You can check that this is the case here: vertex and plaquette operators always intersect on either zero or two qubits.

Since the stabilizer group is a group, it means that the product of stabilizers is also a stabilizer. Therefore, any product of plaquettes is also a stabilizer. Here is an example of product of three plaquettes:

<!-- <div id="surface-code-x-loops" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-x-loops'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([0, 3], 'X');
    gui.code.insertError([0, 5], 'X');
    gui.code.insertError([1, 6], 'X');
    gui.code.insertError([2, 5], 'X');
    gui.code.insertError([3, 4], 'X');
    gui.code.insertError([4, 3], 'X');
    gui.code.insertError([3, 2], 'X');
    gui.code.insertError([1, 2], 'X');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-x-loops.png"/>
</p>
{:.figure}

You can notice that this forms a loop! The reason is that when multiplying the plaquette operators, there are always two $$X$$ operators applied on each qubit of the bulk, which cancel each other. This observation is true in general: all the $$X$$ stabilizers are loops in the lattice! To convince yourself of that fact, try inserting plaquette stabilizers in the following panel:

<div style="text-align: center; margin-bottom: 10px; color: gray; font-size: 15px">
    Click on faces to add plaquette stabilizers
</div>

<div id="surface-code-insert-plaquettes" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-surface-code-plaquette-reset'>Remove all plaquettes</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    let id = 'surface-code-insert-plaquettes'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'https://gui.quantumcodes.io', id);

    let button = document.getElementById('button-surface-code-plaquette-reset');
    button.onclick = () => gui.removeAllErrors();

    gui.onDocumentMouseDown = function(event) {
        let canvasBound = gui.renderer.getContext().canvas.getBoundingClientRect();

        gui.mouse.x = ( (event.clientX  - canvasBound.left) / gui.width ) * 2 - 1;
        gui.mouse.y = - ( (event.clientY - canvasBound.top) / gui.height ) * 2 + 1;

        gui.raycaster.setFromCamera(gui.mouse, gui.camera);

        gui.intersects = gui.raycaster.intersectObjects(gui.code.stabilizers);
        if (this.intersects.length == 0) return;

        let selectedStabilizer = this.intersects[0].object;

        if (selectedStabilizer.type == 'face' && event.button == 0) {
            let x = selectedStabilizer.location[0];
            let y = selectedStabilizer.location[1];
            let z = selectedStabilizer.location[2];

            gui.code.insertError([(x+1).mod(8), y], 'X');
            gui.code.insertError([(x-1).mod(8), y], 'X');
            gui.code.insertError([x, (y+1).mod(8)], 'X');
            gui.code.insertError([x, (y-1).mod(8)], 'X');
        }
    }

    await gui.init()
</script>

What about $$Z$$ stabilizers? They also form loops... if we look at them the right way! Try to understand why by inserting vertex stabilizers in the following panel:

<div style="text-align: center; margin-bottom: 10px; color: gray; font-size: 15px">
    Click on vertices to add vertex stabilizers
</div>

<div id="surface-code-insert-vertex" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-surface-code-vertex-reset'>Remove all vertex stabilizers</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    let id = 'surface-code-insert-vertex'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'https://gui.quantumcodes.io', id);

    let button = document.getElementById('button-surface-code-vertex-reset');
    button.onclick = () => gui.removeAllErrors();

    gui.onDocumentMouseDown = function(event) {
        let canvasBound = gui.renderer.getContext().canvas.getBoundingClientRect();

        gui.mouse.x = ( (event.clientX  - canvasBound.left) / gui.width ) * 2 - 1;
        gui.mouse.y = - ( (event.clientY - canvasBound.top) / gui.height ) * 2 + 1;

        gui.raycaster.setFromCamera(gui.mouse, gui.camera);

        gui.intersects = gui.raycaster.intersectObjects(gui.code.stabilizers);
        if (this.intersects.length == 0) return;

        let selectedStabilizer = this.intersects[0].object;

        if (selectedStabilizer.type == 'vertex' && event.button == 0) {
            let x = selectedStabilizer.location[0];
            let y = selectedStabilizer.location[1];
            let z = selectedStabilizer.location[2];

            gui.code.insertError([(x+1).mod(8), y], 'Z');
            gui.code.insertError([(x-1).mod(8), y], 'Z');
            gui.code.insertError([x, (y+1).mod(8)], 'Z');
            gui.code.insertError([x, (y-1).mod(8)], 'Z');
        }
    }

    await gui.init()
</script>

For instance, here is the product of three vertex operators:

<!-- <div id="surface-code-z-loops" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-z-loops'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([4, 5], 'Z');
    gui.code.insertError([5, 4], 'Z');
    gui.code.insertError([5, 2], 'Z');
    gui.code.insertError([4, 1], 'Z');
    gui.code.insertError([2, 1], 'Z');
    gui.code.insertError([1, 2], 'Z');
    gui.code.insertError([2, 3], 'Z');
    gui.code.insertError([3, 4], 'Z');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-z-loops.png"/>
</p>
{:.figure}

Can you see the loop? If no, this picture should make it clearer:

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-z-loops-dual.png" height="320"/>
</p>
{:.figure}

The trick was to draw an edge orthogonal to every blue edge (dashed purple lines on the figure). Formally, this corresponds to representing the operator in the so-called **dual lattice**, a lattice formed by rotating each edge by 90° (dashed grey lattice in the figure). In this lattice, vertex stabilizers have a square-like shape, similar to the plaquette stabilizers of the primal lattice. Therefore, all the properties we can derive for $$X$$ stabilizers, errors and logicals can be directly translated to $$Z$$ operators by simply considering the dual lattice, where $$Z$$ operators behave exactly like $$X$$ operators.

So we've seen that all the $$X$$ and $$Z$$ stabilizers are loops in the lattice. But is the converse true, that is, do all the loops define stabilizers? Try to think about this question, we will come back to it later.

## Detecting errors

So what happens when errors start occurring on our code? Use the panel below to insert $$X$$ and $$Z$$ errors in the code:

<div style="text-align: center; margin-bottom: 10px; color: gray; font-size: 15px">
    Click on edges to add
    <select id='select-surface-code-error-type'>
        <option value='x'>X errors</option>
        <option value='z'>Z errors</option>
    </select>
</div>

<div id="surface-code-insert-errors" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-surface-code-random'>Insert random errors</button>
    <button id='button-surface-code-reset'>Remove all errors</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        errorModel: 'Pure X',
        rotated: false
    };

    let gui = new Interface(params, {}, keycode, 'https://gui.quantumcodes.io', 'surface-code-insert-errors');

    await gui.init()
    gui.addRandomErrors();

    let button1 = document.getElementById('button-surface-code-random');
    button1.onclick = () => gui.addRandomErrors();

    let button2 = document.getElementById('button-surface-code-reset');
    button2.onclick = () => gui.removeAllErrors();

    let selectErrorType = document.getElementById('select-surface-code-error-type');
    selectErrorType.onchange = function() {
        if (selectErrorType.value == 'x') {
            gui.keycode['x-error'] = 0;
            gui.keycode['z-error'] = -1;
            gui.params.errorModel = 'Pure X';
        }
        else {
            gui.keycode['x-error'] = -1;
            gui.keycode['z-error'] = 0;
            gui.params.errorModel = 'Pure Z';
        }
    }
</script>

Vertices lighted up in yellow are the ones which anticommute with the error and are equal to $$-1$$. They are part of the **syndrome** (set of measured stabilizer values) and are often called **excitations** or **defects**. Can you spot a pattern in the way excitations relates to errors? Let me help you. Start by removing all the errors. Then, draw a path of errors. Can you see what happens?

Excitations only appear at the boundary of error paths! Here is an example of pattern that should make that clear:

<!-- <div id="surface-code-z-paths" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-z-paths';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 3], 'Z');
    gui.code.insertError([4, 3], 'Z');
    gui.code.insertError([5, 4], 'Z');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-x-paths.png"/>
</p>
{:.figure}

We can see that excitations are always created in pairs, and move through the lattice when increasing the size of the error string. What about $$Z$$ errors? The same phenomenon occurs if errors paths are created by adding errors on parallel edges:

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/surface-code-z-paths.png"/>
</p>
{:.figure}

As expected, when seen in the dual lattice (i.e. when rotating each edge by 90°), those $$Z$$ paths correspond to regular strings of errors.

The fact that excitations live at the boundary of error paths also means that when a path forms a loop, the excitations disappear. In other words, loops always commute with all the stabilizers. And what is an operator that commutes with all the stabilizers? A [logical operator](/blog/2023-03-16-stabilizer-formalism-2/)!

## Logical operators, loops and topology

A logical operator can either be trivial, in which case it is a stabilizer, or non-trivial, in which case it performs an $$X$$, $$Y$$ or $$Z$$ logical operation on any of the $$k$$ qubits encoded in the code. For the surface code, the distinction between all those different operators depends on the types of loop they form. And this where the connection with topology really begins. Let's first study the structure of loops on a general smooth manifold, before applying it to the surface code.

### Loops on a smooth manifold

Let's consider a smooth manifold $$\mathcal{M}$$ (e.g. a torus). We say that two loops on $$\mathcal{M}$$ are **equivalent** if there exists a smooth deformation of one loop to the other, meaning that we can smoothly move the first loop to the other loop without cutting it[^3]. For instance, the following four loops (green) on the torus are equivalent:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/torus-trivial-loops.png" height="200"/>
</p>

Moreover, we say that a loop is **contractible**, or **trivial**, if it is equivalent to a point, that is, we can smoothly reduce it until it becomes a single point. All the loops in the figure above are examples of contractible loops on the torus. So what do non-contractible loops look like? Here are examples of non-contractible loops:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/torus-non-trivial-loops.png" height="200"/>
</p>

One loop (blue) goes around the middle hole, while two loops (red) goes around the hole formed by the inside of the donut. Note that those types of loop (red and blue) are not equivalent to each other, and cannot be deformed to obtain any of the green loops of the first figure neither.

As always when we define a notion of equivalence, it can be interesting to look at all the different equivalence classes that they lead to. As a reminder, an **equivalence class**, or **coset**, is a set containing all the objects equivalent to a reference object. So let's enumerate all the equivalence classes of loops on the torus. First, we have the contractible loops. They are all equivalent, since each of them can be reduced to a point, and two points can always be smoothly moved to each other. So that's our first equivalent class, that we can call the **trivial class**. Then, we have the red and blue loops of the figure above: one that goes around the middle hole and the other that goes around the hole formed by the inside of the donut. That's two other equivalent classes. Note that a pair of loops is also technically a loop itself, so taking the red and blue loops together forms its own loop, which is not equivalent to either one of them separately. This "double loop" can also be understood as (and is equivalent to) a single loop that goes around both holes, like the orange line in the picture below:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/torus-double-cycle.png" height="200"/>
</p>

So that's a fourth equivalence class. Do we have more?

Yes! Loops going around a hole twice are not equivalent to loops going around the hole once. Therefore, for each $$k \in \mathbb{N}$$, we have a new class of loops going around one of the holes $$k$$ times. Since in general loops are given a direction, we can also consider loops going around each hole in the opposite direction and take $$k \in \mathbb{Z}$$. Overall, there are infinitely-many equivalence classes which can be labeled by two integers $$(k_1,k_2) \in \mathbb{Z}^2$$, where each integer indicates how many times the loops go around the corresponding hole. In this notation, the trivial class corresponds to $$(0,0)$$, the blue and red non-trivial loops correspond to $$(0,1)$$ and $$(1,0)$$, and the orange loop corresponds to $$(1,1)$$.

**Exercise 1**: What is the equivalence class of the following (purple) loop? [(solution)](#solution-of-the-exercise)
{:.message}
<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/torus-exercise.png" height="200"/>
</p>

### Loops on the surface code

How can we apply what we have learned to the surface code? Compared to general smooth manifolds, the surface code has a more discrete structure, and the notion of *smoothly deforming a loop* does not directly apply here. We need a slightly different notion of equivalence. We say that two loops $$\ell_1,\ell_2$$ on the surface code are equivalent if there exists a stabilizer $$S \in \mathcal{S}$$ such that $$\ell_1 = S \ell_2$$. For instance, if we consider $$X$$ errors and $$X$$ stabilizers, two loops of errors are equivalent if we can apply a series of plaquettes to go from one to the other. As an exercise, show that the following two loops are equivalent, by finding some plaquettes that move one loop to the other:

<!-- <div id="surface-code-equivalent-loops-1" style="display: block;float: left; width: 350px; height: 350px">
</div>
<div id="surface-code-equivalent-loops-2" style="display: block;float: right; width: 350px; height: 350px">
</div>

<div style="clear: both"></div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-equivalent-loops-1';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 7], 'X');
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([3, 2], 'X');
    gui.code.insertError([4, 1], 'X');
    gui.code.insertError([4, 7], 'X');
    gui.code.insertError([3, 6], 'X');

    drawImage(gui, id, true);
</script>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-equivalent-loops-2'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([1, 4], 'X');
    gui.code.insertError([0, 3], 'X');
    gui.code.insertError([0, 1], 'X');
    gui.code.insertError([1, 0], 'X');
    gui.code.insertError([3, 0], 'X');
    gui.code.insertError([4, 1], 'X');
    gui.code.insertError([3, 2], 'X');
    gui.code.insertError([2, 3], 'X');

    drawImage(gui, id, true);
</script> -->

<img src="/assets/img/blog/surface-code/surface-code-equivalent-loops-1.png" style="display: block; float: left;"/>
<img src="/assets/img/blog/surface-code/surface-code-equivalent-loops-2.png" style="display: block; float: right;"/>

<div style="clear: both"></div>

Note that this notion of equivalence is exactly the same as the notion of logical equivalence defined in [Part II of the stabilizer formalism series](/blog/2023-03-16-stabilizer-formalism-2/): applying a stabilizer to a logical gives another representation of the same logical. So operationally, two loops are equivalent if they correspond to the same logical operator. Therefore, by looking at all the equivalence classes of loops, we will be able to classify the different logical operators of the code.

Now, we say that a loop is **contractible**, or **trivial**, if it is equivalent to the empty loop (no error). In other words, a loop is trivial if it is a stabilizer. In the case of $$X$$ errors, a trivial loop corresponds to the boundary of a set of faces (the plaquettes that form the stabilizer).

We are now ready to answer our main question. What are the non-trivial loops of the surface code, or in other words, the non-trivial logical operators? For $$X$$ errors, they look like this:

<!-- <div id="surface-code-x-logical-1" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-x-logical-1';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 7], 'X');
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([2, 5], 'X');

    // drawImage(gui, id, true);
</script> -->

<!-- <div id="surface-code-x-logical-2" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-x-logical-2';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([7, 2], 'X');
    gui.code.insertError([1, 2], 'X');
    gui.code.insertError([5, 2], 'X');
    gui.code.insertError([3, 2], 'X');

    drawImage(gui, id, true);
</script> -->

<img src="/assets/img/blog/surface-code/surface-code-x-logical-1.png" style="display: block; float: left"/>
<img src="/assets/img/blog/surface-code/surface-code-x-logical-2.png" style="display: block; float: right"/>

<div style="clear: both"></div>

And since applying plaquette stabilizers does not change the logical operator, the following strings give other valid representatives of the same logicals:

<!-- <div id="surface-code-x-logical-1-other" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-x-logical-1-other';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([4, 7], 'X');
    gui.code.insertError([4, 5], 'X');
    gui.code.insertError([3, 4], 'X');
    gui.code.insertError([1, 4], 'X');
    gui.code.insertError([0, 3], 'X');
    gui.code.insertError([1, 2], 'X');
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([3, 0], 'X');

    // drawImage(gui, id, true);
</script>

<div id="surface-code-x-logical-2-other" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-x-logical-2-other';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([7, 2], 'X');
    gui.code.insertError([1, 2], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([3, 4], 'X');
    gui.code.insertError([4, 3], 'X');
    gui.code.insertError([4, 1], 'X');
    gui.code.insertError([5, 0], 'X');
    gui.code.insertError([6, 1], 'X');

    drawImage(gui, id, true);
</script> -->

<img src="/assets/img/blog/surface-code/surface-code-x-logical-1-other.png" style="display: block; float: left"/>
<img src="/assets/img/blog/surface-code/surface-code-x-logical-2-other.png" style="display: block; float: right"/>

<div style="clear: both"></div>


As in the smooth case, what matters is that the non-trivial loops go around the torus. Indeed, you will not be able to write those operators as products of plaquette stabilizers. Operationally, each of those two loops (the "horizontal" and the "vertical" ones) correspond to applying a logical $$X$$ operator to the code, and since they are not equivalent, they are applying it to different logical qubits. Therefore, we have at least two logical qubits, one for the horizontal loop and one for the vertical loop. Do we have more?

This time, we only have four different cosets of loops. Indeed, contrary to the smooth case, looping around the lattice twice always gives a trivial loop. This can be seen in two ways. The first way consists in observing that a loop going around the lattice twice is always equivalent to two disjoint loops (this was also true in the smooth case).

The next step is then to show that such a two-loop pattern is always a stabilizer. For instance, try to find the plaquette stabilizers that give rise to the two loops on the right picture:

<div style="float: left">
    <div style="text-align: center; margin-bottom: 10px; color: gray; font-size: 15px">
        Click on faces to add plaquette stabilizers
    </div>

    <div id="surface-code-insert-plaquettes-two-loops" style="margin: auto; display: block; width: 350px; height: 350px">
    </div>

    <div style="text-align: center; margin-bottom: 20px; font-size: 15px">
        <button id='button-surface-code-plaquette-two-loops-reset'>Remove all plaquettes</button>
    </div>
</div>

<!-- Hidden text for positioning-->
<div style="float: right">
    <div style="text-align: center; margin-bottom: 10px; visibility: hidden; font-size: 15px">
        Click on faces to add plaquette stabilizers
    </div>
</div>
<img src="/assets/img/blog/surface-code/surface-code-two-loops.png" style="float: right"/>

<div style="clear: both"></div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    let id = 'surface-code-insert-plaquettes-two-loops'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, keycode, 'https://gui.quantumcodes.io', id);

    let button = document.getElementById('button-surface-code-plaquette-two-loops-reset');
    button.onclick = () => gui.removeAllErrors();

    gui.onDocumentMouseDown = function(event) {
        let canvasBound = gui.renderer.getContext().canvas.getBoundingClientRect();

        gui.mouse.x = ( (event.clientX  - canvasBound.left) / gui.width ) * 2 - 1;
        gui.mouse.y = - ( (event.clientY - canvasBound.top) / gui.height ) * 2 + 1;

        gui.raycaster.setFromCamera(gui.mouse, gui.camera);

        gui.intersects = gui.raycaster.intersectObjects(gui.code.stabilizers);
        if (this.intersects.length == 0) return;

        let selectedStabilizer = this.intersects[0].object;

        if (selectedStabilizer.type == 'face' && event.button == 0) {
            let x = selectedStabilizer.location[0];
            let y = selectedStabilizer.location[1];
            let z = selectedStabilizer.location[2];

            gui.code.insertError([(x+1).mod(8), y], 'X');
            gui.code.insertError([(x-1).mod(8), y], 'X');
            gui.code.insertError([x, (y+1).mod(8)], 'X');
            gui.code.insertError([x, (y-1).mod(8)], 'X');
        }
    }

    await gui.init()
</script>

<!-- <script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-two-loops';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 7], 'X');
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([2, 5], 'X');
    gui.code.insertError([4, 7], 'X');
    gui.code.insertError([4, 1], 'X');
    gui.code.insertError([4, 3], 'X');
    gui.code.insertError([4, 5], 'X');

    drawImage(gui, id, true);
</script> -->

<!-- <div id="surface-code-two-loops-solution" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-two-loops-solution';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 7], 'X');
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([2, 5], 'X');
    gui.code.insertError([4, 7], 'X');
    gui.code.insertError([4, 1], 'X');
    gui.code.insertError([4, 3], 'X');
    gui.code.insertError([4, 5], 'X');

    gui.code.stabilizerMap[[3, 1]].material.color.setHex('0xf9690e');
    gui.code.stabilizerMap[[3, 1]].material.opacity = 1;
    gui.code.stabilizerMap[[3, 1]].visible = true;

    gui.code.stabilizerMap[[3, 3]].material.color.setHex('0xf9690e');
    gui.code.stabilizerMap[[3, 3]].material.opacity = 1;
    gui.code.stabilizerMap[[3, 3]].visible = true;

    gui.code.stabilizerMap[[3, 5]].material.color.setHex('0xf9690e');
    gui.code.stabilizerMap[[3, 5]].material.opacity = 1;
    gui.code.stabilizerMap[[3, 5]].visible = true;

    gui.code.stabilizerMap[[3, 7]].material.color.setHex('0xf9690e');
    gui.code.stabilizerMap[[3, 7]].material.opacity = 1;
    gui.code.stabilizerMap[[3, 7]].visible = true;

    drawImage(gui, id, true);
</script> -->


The second way to see this is to remember that a loop applies a logical operator $$P$$, and applying this logical operator twice gives $$P^2=I$$. It means that any double loop lives in the identity coset, and is therefore a stabilizer.

Thus, our equivalence classes for $$X$$ errors can be labeled by two bits $$(k_1,k_2) \in \mathbb{Z}_2 \times \mathbb{Z}_2$$. The corresponding logical operators are $$I$$, $$X_1$$, $$X_2$$ and $$X_1 X_2$$. The fact that our sets are $$\mathbb{Z}_2$$ instead of $$\mathbb{Z}$$ can also be interpreted as a consequence of the fact that we have qubits. For qudits, the generalization of Pauli operators obey $$P^d=I$$, and $$\mathbb{Z}_2$$ is replaced by $$\mathbb{Z}_d$$. For error-correcting codes on continuous-variable systems (roughly, qudits with $$d=\infty$$), we recover $$\mathbb{Z}$$ as our space of equivalence classes[^4].

Let's summarize what we have learned. Loops of the surface code define logical operators. There are four non-equivalent types of loops: the trivial ones (stabilizers), the horizontal ones ($$X_1$$ operator), the vertical ones ($$X_2$$ operator), and those two at the same time ($$X_1 X_2$$ operator). Therefore, the surface code encodes $$k=2$$ logical qubits.

Note that in general, the surface code can be defined on any smooth manifold $$\mathcal{M}$$ by discretizing it. The number of logical qubits of the code is then directly connected to the topological properties of the manifold, and in particular, to the number of holes. For instance, for the torus, the fact that $$k=2$$ is a consequence of the presence of two holes.

So far, we have mainly discussed loops of $$X$$ errors, but what about $$Z$$ errors? As expected, the $$Z_1$$ and $$Z_2$$ logicals correspond to loops going around the torus when considered in the dual lattice:

<!-- <div id="surface-code-z-logical-1" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-z-logical-1';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([3, 0], 'Z');
    gui.code.insertError([3, 2], 'Z');
    gui.code.insertError([3, 4], 'Z');
    gui.code.insertError([3, 6], 'Z');

    // drawImage(gui, id, true);
</script>

<div id="surface-code-z-logical-2" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-z-logical-2';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()

    gui.code.insertError([0, 3], 'Z');
    gui.code.insertError([2, 3], 'Z');
    gui.code.insertError([4, 3], 'Z');
    gui.code.insertError([6, 3], 'Z');

    // drawImage(gui, id, true);
</script> -->

<img src="/assets/img/blog/surface-code/surface-code-z-logical-1.png" style="display: block; float: left"/>
<img src="/assets/img/blog/surface-code/surface-code-z-logical-2.png" style="display: block; float: right"/>

<div style="clear: both"></div>

On the left is the $$Z_1$$ logical (which anticommute with $$X_1$$) and on the right is the $$Z_2$$ logical (which anticommute with $$X_2$$). Those anticommutation relation between $$X_1$$ and $$Z_1$$ can be seen in this picture:

<!-- <div id="surface-code-anticommutation" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-anticommutation';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 7], 'X');
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([2, 5], 'X');

    gui.code.insertError([0, 3], 'Z');
    gui.code.insertError([2, 3], 'Z');
    gui.code.insertError([4, 3], 'Z');
    gui.code.insertError([6, 3], 'Z');

    // drawImage(gui, id, true);
</script> -->

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-anticommutation.png"/>
</p>

Indeed, we can see that the $$X_1$$ and $$Z_1$$ logicals intersect on exactly one qubit (green), meaning that they anticommute. This property is independent on the specific logical representatives you choose: a "horizontal" $$X$$ loop will always intersect with a "vertical" $$Z$$ loop on a single qubit.

You now have all you need to determine the parameters of the surface code!

**Exercise 2**: What are the $$[[n,k,d]]$$ parameters of a surface code with lattice size $$L$$? [(solution)](#solution-of-the-exercise)
{:.message}

## Decoding the surface code

Let's imagine that you observe the following syndrome, and want to find a good correction operator.

<!-- <div id="surface-code-syndrome" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-syndrome';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.toggleStabilizer(gui.code.stabilizerMap[[4, 4]], true);
    gui.code.toggleStabilizer(gui.code.stabilizerMap[[2, 2]], true);

    drawImage(gui, id, false);
</script> -->

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-syndrome.png"/>
</p>

We know that excitations always appear in pairs, and correspond to the boundary of strings of errors.
So the error must be a string that links those two excitations. However, the number of strings that could have given this syndrome is very large! Here are three examples of errors leading to the syndrome shown above:

<!-- <div id="surface-code-decoding-1" style="display: block; float: left; width: 250px; height: 250px">
</div>
<div id="surface-code-decoding-3" style="display: block; float: right; width: 250px; height: 250px">
</div>
<div id="surface-code-decoding-2" style="display: block; float: right; width: 250px; height: 250px">
</div>

<div style="clear: both"></div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js';

    let id = 'surface-code-decoding-1';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([3, 4], 'X');
    gui.code.insertError([2, 3], 'X');

    // drawImage(gui, id, true);
</script>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js';

    let id = 'surface-code-decoding-2';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([4, 3], 'X');
    gui.code.insertError([3, 2], 'X');

    drawImage(gui, id, true);
</script>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js';

    let id = 'surface-code-decoding-3';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([5, 4], 'X');
    gui.code.insertError([6, 3], 'X');
    gui.code.insertError([7, 2], 'X');
    gui.code.insertError([0, 1], 'X');
    gui.code.insertError([1, 0], 'X');
    gui.code.insertError([2, 1], 'X');

    // drawImage(gui, id, true);
</script> -->

<img src="/assets/img/blog/surface-code/surface-code-decoding-1.png" style="display: block; float: left"/>
<img src="/assets/img/blog/surface-code/surface-code-decoding-2.png" style="display: block; float: right"/>
<img src="/assets/img/blog/surface-code/surface-code-decoding-3.png" style="display: block; float: right"/>

<div style="clear: both"></div>

Let's suppose that the leftmost pattern was our actual error, but we chose the middle pattern instead as our correction operator. This is what the final pattern, corresponding to the error (red) plus the correction operator (yellow), would look like:

<!-- <div id="surface-code-correction-1" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-correction-1';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([3, 4], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([4, 3], 'X');
    gui.code.insertError([3, 2], 'X');

    gui.code.qubitMap[[4, 3]].material.color.setHex('0xffdf00');
    gui.code.qubitMap[[3, 2]].material.color.setHex('0xffdf00');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-correction-1.png"/>
</p>

And as you can see, this is a stabilizer! So applying this correction operator puts us back in the original state and the correction is a success. On the other hand, here is what would happen if we had applied the rightmost correction instead:

<!-- <div id="surface-code-correction-2" style="display: block; margin: auto; width: 350px; height: 350px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-correction-2'

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([3, 4], 'X');
    gui.code.insertError([2, 3], 'X');

    gui.code.insertError([5, 4], 'X');
    gui.code.insertError([6, 3], 'X');
    gui.code.insertError([7, 2], 'X');
    gui.code.insertError([0, 1], 'X');
    gui.code.insertError([1, 0], 'X');
    gui.code.insertError([2, 1], 'X');

    gui.code.qubitMap[[5, 4]].material.color.setHex('0xffdf00');
    gui.code.qubitMap[[6, 3]].material.color.setHex('0xffdf00');
    gui.code.qubitMap[[7, 2]].material.color.setHex('0xffdf00');
    gui.code.qubitMap[[0, 1]].material.color.setHex('0xffdf00');
    gui.code.qubitMap[[1, 0]].material.color.setHex('0xffdf00');
    gui.code.qubitMap[[2, 1]].material.color.setHex('0xffdf00');

    drawImage(gui, id, false);
</script> -->

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-correction-2.png"/>
</p>

As you can see, this is a logical operator! So this is an example of correction failure, where we have changed the logical state of our code when applying the correction.

What lessons can we draw from this example? The goal of the surface code decoding problem is to match the excitations such that the final operator is a stabilizer. Let's try to formalize this a little bit. I described the general decoding problem for stabilizer codes in a [previous post](/blog/2023-03-28-stabilizer-formalism-3/), but a short reminder is probably warranted.

Similarly to how logical operators can be partitioned into cosets, we can also enumerate equivalent classes for errors fitting a given syndrome. For instance, the leftmost and middle patterns in our example above are part of the same equivalent class, as they can be related by a plaquette stabilizer. On the other hand, the rightmost plaquette belongs to a different class. The goal of decoding is to find a correction operator that belongs to the same coset as the actual error. Indeed, the product $$CE$$ of the correction operator with the error is equal to a stabilizer if and only if there is a stabilizer $$S$$ such that $$C=ES$$, that is, if $$C$$ and $$E$$ belong to the same class. To solve this problem with the information we have, that is, only the syndrome and the error probabilities, the optimal decoding problem, also called **maximum-likelihood decoding**, can be formulated as finding the coset $$\bm{\bar{C}}$$ with the highest probability:

$$
\begin{aligned}
    \max_{\bm{\bar{C}}} P(\bm{\bar{C}})
\end{aligned}
$$

where $$P(\bm{\bar{C}})$$ can be calculated as a sum over all the operators in the coset: $$P(\bm{\bar{C}}) = \sum_{\bm{C} \in \bm{\bar{C}}} P(\bm{C})$$.

Solving this problem exactly is computationally very hard, as it requires calculating a sum over an exponential number of terms (in the size of the lattice). But for the surface code, it can be approximated very well using [tensor network decoders](https://arxiv.org/abs/2101.04125), which have a complexity of $$O(n \chi^3)$$ (up to some logarithmic factor), with $$n$$ the number of qubits and $$\chi$$ a parameter quantifying the degree of approximation of the decoder (corresponding to the bound dimension of the tensor network). The main downside of this decoder is that it generalizes poorly to the case of imperfect syndrome measurements. In this case, measurements need to be repeated in time, leading to a 3D decoding problem that tensor networks cannot solve efficiently at the moment.

As discussed in my [stabilizer decoding post](/blog/2023-03-28-stabilizer-formalism-3/), the maximum-likelihood decoding problem can also be approximated by solving for the error with the highest probability, instead of the whole coset. Assuming i.i.d. noise, finding the error with the highest probability is equivalent to finding the smallest error that fits the syndrome. In the case of the surface code, this corresponds to matching the excitations with chains of minimum weight.

As it happens, this is completely equivalent to solving a famous graph problem, known as [**minimum-weight perfect matching**](https://en.wikipedia.org/wiki/Maximum_weight_matching)! This problem can be expressed as matching all the vertices of a weighted graph (with an even number of vertices), such that the total weight is minimized. In our case, the graph is constructed as a complete graph with a vertex for each excitation. The weight of each edge between two vertices is then given by the Manhattan distance between the two corresponding excitations. For instance, let's consider the following decoding problem:

<!-- <div id="surface-code-matching" style="display: block; margin: auto; width: 400px; height: 400px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-matching';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 5,
        Ly: 5,
        Lz: 5,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.toggleStabilizer(gui.code.stabilizerMap[[2, 0]], true);
    gui.code.toggleStabilizer(gui.code.stabilizerMap[[2, 4]], true);
    gui.code.toggleStabilizer(gui.code.stabilizerMap[[6, 4]], true);
    gui.code.toggleStabilizer(gui.code.stabilizerMap[[8, 8]], true);

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-matching.png" height="350" />
</p>

The associated graph is then the following:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/matching-graph.png" height="200" />
</p>

By enumerating all the possible matchings, you can quickly see that the one of minimum weight links vertices 1 and 2, and 3 and 4, with a total weight of 5:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/matching-graph-solution.png" height="200" />
</p>

From there, we can deduce our decoding solution:

<!-- <div id="surface-code-decoding-solution" style="display: block; margin: auto; width: 400px; height: 400px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    let id = 'surface-code-decoding-solution';

    const params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 5,
        Ly: 5,
        Lz: 5,
        rotated: false
    };

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([2, 1], 'X');
    gui.code.insertError([2, 3], 'X');
    gui.code.insertError([7, 4], 'X');
    gui.code.insertError([8, 5], 'X');
    gui.code.insertError([8, 7], 'X');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-decoding-solution.png" height="350" />
</p>

It happens that minimum-weight perfect-matching can be solved in polynomial time using the [Blossom algorithm](https://en.wikipedia.org/wiki/Blossom_algorithm), which has a worst-case complexity of $$O(n^3)$$. While this complexity might seem quite high, a [recent modification](https://arxiv.org/abs/2303.15933) of the Blossom algorithm, proposed by Oscar Higgott and Craig Gidney, seems to have an average complexity of $$O(n)$$. It also generalizes very well to the imperfect syndrome case, making it one of the best decoders out there in terms of trade-off between speed and performance (the performance will be reviewed when talking about thresholds in the last section of the post).

You can play with the matching decoder in the following visualization[^5]:

<div style="text-align: center; margin-bottom: 10px; color: gray; font-size: 15px">
    Click on edges to add
    <select id='select-surface-code-decoding-error-type'>
        <option value='x'>X errors</option>
        <option value='z'>Z errors</option>
    </select>
</div>

<div id="surface-code-decoding-insert-errors" style="margin: auto; display: block; width: 350px; height: 350px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-surface-code-decoding-decode'>Decode</button>
    <button id='button-surface-code-decoding-reset'>Remove all errors</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    let params = {
        dimension: 2,
        codeName: 'Toric 2D',
        Lx: 4,
        Ly: 4,
        Lz: 4,
        decoder: 'Matching',
        rotated: false
    };

    let gui = new Interface(params, {}, keycode, 'https://gui.quantumcodes.io', 'surface-code-decoding-insert-errors');

    await gui.init();

    let button1 = document.getElementById('button-surface-code-decoding-decode');
    button1.onclick = () => gui.decode();

    let button2 = document.getElementById('button-surface-code-decoding-reset');
    button2.onclick = () => gui.removeAllErrors();

    let selectErrorType = document.getElementById('select-surface-code-decoding-error-type');
    selectErrorType.onchange = function() {
        if (selectErrorType.value == 'x') {
            gui.keycode['x-error'] = 0;
            gui.keycode['z-error'] = -1;
            gui.params.errorModel = 'Pure X';
        }
        else {
            gui.keycode['x-error'] = -1;
            gui.keycode['z-error'] = 0;
            gui.params.errorModel = 'Pure Z';
        }
    }
</script>

There are many other surface code decoders out there with their own pros and cons, such as [union-find](https://arxiv.org/abs/1709.06218), [neural network-based decoders](https://arxiv.org/abs/1811.12456), [belief propagation](https://arxiv.org/abs/2005.07016), etc. Describing them all in detail is out of scope for this blog post, but I hope to write a separate post one day dedicated to decoding. I also haven't talked much about the decoding problem for imperfect syndrome, which I also leave for a separate blog post.

Let's now answer a question that you might have been wondering this whole time: how the hell do we implement a toric lattice in practice? While it's in principle possible to implement an actual torus experimentally (for example with [cold atoms](https://www.nature.com/articles/s41586-018-0450-2/figures/2)), it's impractical for many quantum computing architecture. Fortunately, there exists a purely planar version of the surface code, that we will discuss now!

## Surface code with open boundaries

Consider the following version of the surface code, where vertex stabilizers on the top and bottom boundaries, and plaquette stabilizers on the left and right boundaries, are now supported on three qubits instead of four:

<div style="text-align: center; margin-bottom: 0px; color: gray; font-size: 15px">
    Click on edges to add
    <select id='select-planar-code-error-type'>
        <option value='x'>X errors</option>
        <option value='z'>Z errors</option>
    </select>
</div>

<div id="planar-code-lattice" style="margin: auto; display: block; max-width: 450px; height: 450px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-planar-code-random'>Insert random errors</button>
    <button id='button-planar-code-reset'>Remove all errors</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    let id = 'planar-code-lattice';

    const params = {
        dimension: 2,
        codeName: 'Planar 2D',
        Lx: 5,
        Ly: 5,
        Lz: 5,
        rotated: false
    };

    let gui = new Interface(params, {}, keycode, 'https://gui.quantumcodes.io', 'planar-code-lattice');

    let button1 = document.getElementById('button-planar-code-random');
    button1.onclick = () => gui.addRandomErrors();

    let button2 = document.getElementById('button-planar-code-reset');
    button2.onclick = () => gui.removeAllErrors();

    let selectErrorType = document.getElementById('select-planar-code-error-type');
    selectErrorType.onchange = function() {
        if (selectErrorType.value == 'x') {
            gui.keycode['x-error'] = 0;
            gui.keycode['z-error'] = -1;
            gui.params.errorModel = 'Pure X';
        }
        else {
            gui.keycode['x-error'] = -1;
            gui.keycode['z-error'] = 0;
            gui.params.errorModel = 'Pure Z';
        }
    }

    await gui.init()
</script>

We call the top and bottom boundaries **smooth boundaries**, and the left and right boundaries **rough boundaries**. Feel free to play with this lattice and try to figure out what the main differences are, compared to the toric code. In particular, can you identify the logical operators of this code? How many equivalent classes, or logical qubits, can you spot?

The first difference to notice is that excitations can now be created at the boundary!

<!-- <div id="planar-code-boundary-errors" style="margin: auto; display: block; width: 450px; height: 450px">
</div>

<script type="module">
    import { Interface } from 'http://127.0.0.1:5001/js/gui.js'

    const params = {
        dimension: 2,
        codeName: 'Planar 2D',
        Lx: 5,
        Ly: 5,
        Lz: 5,
        rotated: false
    };

    let id = 'planar-code-boundary-errors';

    let gui = new Interface(params, {}, {}, 'http://127.0.0.1:5001', id);

    await gui.init()
    gui.code.insertError([1, 2], 'X');
    gui.code.insertError([3, 2], 'X');
    gui.code.insertError([7, 8], 'Z');
    gui.code.insertError([7, 6], 'Z');

    drawImage(gui, id, true);
</script> -->

<p style="text-align:center;">
    <img src="/assets/img/blog/surface-code/planar-code-boundary-errors.png" />
</p>

In this example, a vertex excitation is created on the rough boundary, and a plaquette excitation is created on the smooth boundary. You can see that excitations don't have to come in pairs anymore! This poses a slight issue when decoding using minimum-weight perfect matching, but this can easily be overcome by adding some new boundary nodes to the matching graph.

More importantly, we can observe that vertex excitations can only be created or annihilated at rough boundaries, and plaquette excitations only at the smooth boundaries. This means that $$X$$ logicals have to join the rough boundaries, and $$Z$$ logicals have to join the smooth boundaries. This is illustrated in the following figure, where we can see that there is no "vertical" $$X$$ logical or "horizontal" $$Z$$ logical.

Therefore, there are only two equivalence classes of logicals for each error type: the strings that join opposite boundaries, and the trivial loops. As a consequence, this non-periodic version of the surface code, also called **planar code**, only encodes a single qubit. It is a $$[[2L^2 - 2L + 1, 1, L]]$$-code. While we have lost one qubit compared to the toric version, the fact that it can be laid out on a 2D surface makes it much more practical.

## A more compact version: the rotated surface code

If you have started looking at the surface code literature, you might have noticed that people often use a different representation, which looks roughly like the following:

<div style="text-align: center; margin-bottom: 0px; color: gray; font-size: 15px">
    Click on vertices to add
    <select id='select-rectified-code-error-type'>
        <option value='x'>X errors</option>
        <option value='z'>Z errors</option>
    </select>
</div>

<div id="surface-code-rectified-picture" style="margin: auto; display: block; width: 450px; height: 450px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-rectified-code-random'>Insert random errors</button>
    <button id='button-rectified-code-reset'>Remove all errors</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    let id = 'surface-code-rectified-picture';

    const params = {
        dimension: 2,
        codeName: 'Planar 2D',
        Lx: 5,
        Ly: 5,
        Lz: 5,
        rotated: true
    };

    let gui = new Interface(params, {}, keycode, 'https://gui.quantumcodes.io', id);

    let button1 = document.getElementById('button-rectified-code-random');
    button1.onclick = () => gui.addRandomErrors();

    let button2 = document.getElementById('button-rectified-code-reset');
    button2.onclick = () => gui.removeAllErrors();

    let selectErrorType = document.getElementById('select-rectified-code-error-type');
    selectErrorType.onchange = function() {
        if (selectErrorType.value == 'x') {
            gui.keycode['x-error'] = 0;
            gui.keycode['z-error'] = -1;
            gui.params.errorModel = 'Pure X';
        }
        else {
            gui.keycode['x-error'] = -1;
            gui.keycode['z-error'] = 0;
            gui.params.errorModel = 'Pure Z';
        }
    }

    await gui.init()
</script>

In this representation, qubits are on the vertices, and all the stabilizers are on the plaquette. Feel free to play with this lattice to understand what's going on.

It happens that this lattice represents exactly the planar code that we saw before! Here are the two lattices on top of each other:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-both-pictures.png" />
</p>

The idea is to turn every edge of the original representation into a vertex, each vertex into yellow face, and each face into a rose face. As a result, both vertices and plaquettes become rotated squares, and qubits become vertices. This representation is called the [**rectified lattice**](https://en.wikipedia.org/wiki/Rectification_(geometry)).

One advantage of this representation is that it allows to come up with a different, more compact, version of the surface code. The idea is to take to following central piece of the rectified lattice:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/surface-code-rotated-construction.png" />
</p>

We then rotated it and add a few boundary stabilizers. This gives the following code, called the **rotated surface code**:

<div style="text-align: center; margin-bottom: 0px; color: gray; font-size: 15px">
    Click on vertices to add
    <select id='select-rotated-code-error-type'>
        <option value='x'>X errors</option>
        <option value='z'>Z errors</option>
    </select>
</div>

<div id="rotated-surface-code" style="margin: auto; display: block; width: 450px; height: 450px">
</div>

<div style="text-align: center; margin-bottom: 20px; font-size: 15px">
    <button id='button-rotated-code-random'>Insert random errors</button>
    <button id='button-rotated-code-reset'>Remove all errors</button>
</div>

<script type="module">
    import { Interface } from 'https://gui.quantumcodes.io/js/gui.js'

    const params = {
        dimension: 2,
        codeName: 'Rotated Planar 2D',
        Lx: 5,
        Ly: 5,
        Lz: 5,
        rotated: true
    };

    let gui = new Interface(params, {}, keycode, 'https://gui.quantumcodes.io', 'rotated-surface-code');

    let button1 = document.getElementById('button-rotated-code-random');
    button1.onclick = () => gui.addRandomErrors();

    let button2 = document.getElementById('button-rotated-code-reset');
    button2.onclick = () => gui.removeAllErrors();

    let selectErrorType = document.getElementById('select-rotated-code-error-type');
    selectErrorType.onchange = function() {
        if (selectErrorType.value == 'x') {
            gui.keycode['x-error'] = 0;
            gui.keycode['z-error'] = -1;
            gui.params.errorModel = 'Pure X';
        }
        else {
            gui.keycode['x-error'] = -1;
            gui.keycode['z-error'] = 0;
            gui.params.errorModel = 'Pure Z';
        }
    }

    gui.init()
</script>

As always, feel free to familiarize yourself with this new code by playing with it on the visualization. You should be able to see that it also encodes a single logical qubit, and has a distance of $$L$$. However, this time, the number of physical qubits is exactly $$L^2$$. The rotated surface code is therefore a $$[[L^2, 1, L]]$$-code, which is a factor two improvement in the overhead compared to the original surface code. This version of the surface code is therefore the preferred one to realize experimentally. For instance, its two smallest instances, the $$[[9,1,3]]$$ code and the $$[[16,1,4]]$$ code are the ones recently realized by the Google lab.

## Thresholds of the surface code

One of the most important characteristic of a code family is its set of thresholds. The **threshold** of a code family for a given noise model and decoder is the maximal physical error rate $$p_{th}$$ such that for all $$p < p_{th}$$, increasing the code size decreases the logical error rate. The threshold is typically calculated numerically using plots that look like the following:

<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/threshold-example.png" height="400" />
</p>

To make this figure, codes with distance $$10,20,30$$ are simulated under noise channel with varying physical error rate. Errors are then decoded, and logical errors are enumerated. We can see that above $$p_{th} \approx 15.5\%$$, increasing the code distance increases the logical error rate, while below $$p_{th}$$, the logical error rate decreases with the code distance.

So what is the threshold of the surface code? First of all, very importantly, there isn't a single threshold for the surface code: it highly depends on which noise channel and which decoder we are using. Let's start by discussing noise models.

We often make the distinction between three types of noise models:
* The **code-capacity** model, in which errors can occur on all the physical qubits of the code, but measurements are assumed to be perfect.
* The **phenomenological** noise model, in which each stabilizer measurement can also fail with a fixed probability.
* The **circuit-level** noise model, in which the circuits to prepare the code and extract the syndrome are considered, and errors are assumed to occur with a certain probability after each physical gate.

The code-capacity threshold is the easiest to estimate, both in terms of implementation time and computational time, and allows to get a rough idea of the performance of a given code or decoder. The phenomenological threshold gets us closer to the true threshold value and can be useful when comparing decoders that deal with measurement errors in interesting ways (such as single-shot decoders). Finally, circuit-level thresholds are the most realistic ones and approximate the most accurately the actual noise level that experimentalists need to reach to make error correction work with a given code. While circuit-level thresholds have been considered very hard to estimate for a long time, mainly due to the lack of very fast noisy Clifford circuit simulators, recent tools such as Stim have made those simulations much less cumbersome.

For each of those three models, we also need to specify the distribution of $$X$$, $$Y$$ and $$Z$$ errors[^6]. There are two very common choices here. The first is the depolarizing noise model, in which those three Paulis are assumed to occur with the same probability. Since $$Y$$ is made of $$X$$ and $$Z$$, it means that $$P(Y)=P(X,Z) \neq P(X)P(Z)$$, or in other words, $$X$$ and $$Z$$ are correlated. Another noise model is the independent $$X/Z$$ model, in which $$X$$ and $$Z$$ are independent and occur with the same probability. The probability of getting $$Y$$ errors is fixed as $$P(Y)=P(X)P(Z)=P(X)^2$$ and is therefore lower than for depolarizing noise.

Regarding the decoders, we will consider two of them here for simplicity: the maximum-likelihood decoder, and the matching decoder. As it happens, the code-capacity threshold for the maximum likelihood decoder corresponds exactly to the phase transition of a certain statistical mechanics model. This *stat mech mapping* was established in [Dennis et al.](https://arxiv.org/abs/quant-ph/0110143) (probably the most cited paper of quantum error correction) in 2002. For the surface code subjected to independent $$X/Z$$ errors, the equivalent stat mech model is the random-bond Ising model, whose phase transition had just been calculated at that time. They were therefore able to give this first surface code threshold without doing any simulation themselves!

We are now ready to give the actual threshold values for the surface code! Here is a table with the code-capacity thresholds of the different noise models and decoder discussed previously:

<table style="margin: auto; width: 60%; text-align: center; margin-bottom: 1em">
<th>
    <td>Maximum-likelihood</td>
    <td>Matching</td>
</th>
<tr>
    <td>Depolarizing noise</td>
    <td><a href="https://arxiv.org/abs/1202.1852">18.9%</a></td>
    <td><a href="https://arxiv.org/abs/0905.0531">15.5%</a></td>
</tr>
<tr>
    <td>Independent noise</td>
    <td><a href="https://arxiv.org/abs/quant-ph/0110143">10.9%</a></td>
    <td><a href="https://arxiv.org/abs/quant-ph/0110143">10.5%</a></td>
</tr>
</table>
Table 1: Code-capacity thresholds of the surface code
{:.figure}


For phenomenological and circuit-level noise, I am only aware of some matching decoder thresholds under depolarizing noise. For phenomenological noise, we have a threshold of about [$$3\%$$](https://arxiv.org/abs/1907.02554). For circuit-level noise, the threshold goes down to about [$$1\%$$](https://arxiv.org/abs/0905.0531), which is often the cited value for "the threshold of the surface code".

## Conclusion

In this post, we have defined the surface code and its different variants (toric, planar, rotated) and tried to understand its most important properties visually. We have seen that it encodes one or two logical qubits depending on the boundary conditions, and has a distance scaling as $$\sqrt{N}$$. Stabilizers can be thought as trivial (or contractible) loops on the underlying manifold, while the logical $$X$$ and $$Z$$ operators are the non-trivial loops going around the torus or joining the boundaries, drawing a connection between topology and codes. We have also studied the decoding problem for the surface code and how minimum-weight perfect matching can be used for this purpose. Finally, I have introduced the notion of error-correction threshold and given its value for different decoders and noise models.

The surface code is by far one of the most studied codes of the quantum error-correction literature and there is a lot more to say about it! I haven't told you how to deal with measurement errors, how to prepare the code and measure the syndrome using quantum circuits, how to run logical gates on it, how to generalize it to different lattices and dimensions, how to make precise the connection with topology, etc. The surface code is also a stepping stone to understand more complicated codes, from the color code (the second most famous family of 2D codes) to hypergraph product codes and all the way to good LDPC codes. Now that you are equipped with the stabilizer formalism and have a good grasp of the surface code, the tree of possible learning trajectories has suddenly acquired many branches, and I hope to cover as many of those in subsequent blog posts!

In the meantime, one direct follow-up from this post is [Dominik Kufel's post](https://dom-kufel.github.io/blog/2023-04-15-toric_code-intro/) on the condensed matter aspects of the toric code, where you will learn about the connection between codes and Hamiltonians, why *excitations* are called excitations and can be thought of as quasi-particles called *anyons*, what the state of the surface code looks like and how it provides an example of topological phase of matter. This connection is crucial to learn for any practicing quantum error-correcter, as it is used extensively in the literature and allows to understand many computational aspects of the surface code (how to make gates by braiding anyons, why the circuit to prepare the surface code has polynomial size, etc.). So go read his post!

## Solution of the exercise

**Exercise 1**: What is the equivalence class of the following (purple) loop? [(Back to section)](#loops-on-a-smooth-manifold)
{:.message}
<p style="text-align:center; margin-top: 2em; margin-bottom: 2em">
    <img src="/assets/img/blog/surface-code/torus-exercise.png" height="200"/>
</p>
**Correction**: The loops goes once around the middle hole, and three times around the hole forming the inside of the donut. Therefore, it belongs to coset labelled by $$(1,3)$$.
{:.message}

**Exercise 2**: What are the $$[[n,k,d]]$$ parameters of a surface code with lattice size $$L$$? [(Back to section)](#loops-on-the-surface-code)
{:.message}

**Correction**: Since there are $$L^2$$ horizontal and $$L^2$$ vertical edges, we have $$n=2L^2$$. Then, we saw that there are exactly two non-equivalent types of logical operators, meaning that there are $$k=2$$ qubits. Finally, the distance is the minimum size of a logical operator, which in our case is $$d=L$$. Therefore, the surface code is a $$[[2L^2, 2, L]]$$-code.
{:.message}

[^1]: I tend to use the names *surface code* and *toric code* interchangeably. Some people use *surface code* to talk about the open-boundary version and *toric code* to talk about the periodic-boundary one, but this is not a universal convention, and the name *planar code* is also often used to talk about the open-boundary version.
[^2]: The final version of this embedding is just a few lines of Javascript, which use [gui.quantumcodes.io](gui.quantumcodes.io) as an API. So if **you** would like to embed some codes (both 2D and 3D) in your website, feel free to contact me and I'll give you the instructions to do it (I might add an official tutorial in the PanQEC documentation later).
[^3]: For the interested reader, the rigorous definition is that two loops are equivalent if there exists a **homotopy** between them. More precisely, we can define a loop as a continuous map $$\ell: S^1 \rightarrow \mathcal{M}$$ (an embedding of the circle onto the manifold). A homotopy between two loops $$\ell_1, \ell_2$$ is then a continuous function $$L:S^1 \times [0,1] \rightarrow \mathcal{M}$$ such that $$L(\cdot, 0)=\ell_1$$ and $$L(\cdot, 1)=\ell_2$$. In other words, each $$L(\cdot, t)$$ for $$t \in [0,1]$$ defines a different loop on the path from $$\ell_1$$ to $$\ell_2$$. The equivalence relation defined here is called **homotopy equivalence**, and is one of the most important notions of equivalence in topology (along with another one called **homological equivalence**).
[^4]: [Rotor codes](https://arxiv.org/abs/2303.13723) are example of such continuous-variable codes.
[^5]: The backend is using [PyMatching](https://github.com/oscarhiggott/PyMatching), Oscar Higgott's fast implementation of minimum-weight perfect matching.
[^6]: We assume here that we have a Pauli error model. The reasons for this choice are actually quite technical and I hope to write a post about it one day. In the meantime, you can have a look at Section 10.3 of Nielsen & Chuang to get an idea of the argument.
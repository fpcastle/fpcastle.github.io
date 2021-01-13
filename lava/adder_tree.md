---
layout: tech
title: Adder Tree
---
## An Adder Tree in Lava

Consider the task of adding eight numbers n1, n2, .. n8 use a binary tree of adders as shown below.

<p align="center"> <img src="adder_tree_n.jpg"></p>

In a language like VHDL one can write a recursive function that takes an unconstrained array of numbers as input and returns their sum computed with an adder tree. However, there is no standard way in VHDL of specifying how the adder tree should be laid out. Adder trees are typically pipelined and it takes just one adder to be badly placed to degrade the performance of the whole adder tree. Since Lava can specify how the adders are to be laid out the design engineer can make decisions and calculations about intermediate wire delays. One example layout scheme for a pipelined adder tree is shown below (the clock wires are now shown):

<p align="center"> <img src="adder_tree_middle.jpg"></p>

This layout scheme places the adders in a row with the final addition occurring in the middle. Both the left and right hand sides of the final adder also contain adder trees in which the final sum occurs in the middle. This recursive structure continues until the leaf additions are encountered (e.g. the adder for n1 and n2).

### The middle Combinator

<p align="center"> <img src="middle.jpg"></p>

Note that this combinator produces a two-sided tile which has two inputs on the left and one output on the right.

## Tree Circuits in Lava

Using the middle combinator we can define a tree circuit combinator for any kind of two input and one output circuit:

```haskell
tree circuit [a] = a 
tree circuit [a, b] = circuit (a, b) 
tree circuit xs 
  = middle (tree circuit) circuit (tree circuit)(halve xs) 
```

* The first line of this definition is used when a singleton list is given to the tree circuit. In this case the result is just the sole element of the list.
* The second line is used when a two element list is used with a tree circuit. In this case the result is obtain by processing the two inputs with the circuit that is the first parameter of the tree combinator.
* In the third line case the middle circuit will be the parameterized two input and one output circuit and the left and right circuits will be recursively defined trees of this circuit. The input is halved with the first half going to the left sub-tree and the second half going to the right sub-tree.

A combinational adder tree with bit-growth can be made with the flexibleAdder circuit. This circuit has the type `(Bit, Bit) -> (Bit)` so it is suitable for use as an element of an adder tree. To define a combinational adder tree one simply writes:

```haskell
adderTree = tree flexibleAdder
```

It is possible to rewrite a call to the tree combinator to see how it works for a particular circuit and input list:

```haskell
tree flexibleAdder [i1, i2, i3, i4]
  = (middle (tree flexibleAdder) circuit (tree flexibleAdder)) (halveList [i1, i2,i3, i4]) 
  = (middle (tree flexibleAdder) circuit (tree flexibleAdder)) [[i1, i2], [i3, i4]] 
  = flexibleAdder (tree flexibleAdder [i1, i1], tree flexibleAdder [i2, i3]) 
  = flexibleAdder (i1+i2, i3+i4) 
  = i1+i2+i3+i4
```

We can use the `flexibleAdderFD` circuit to make a pipelined adder tree:

```haskell
adderTreeFD clk = tree (flexibleAdderFD clk)
```

The adders in this tree will use FD register components. An example layout of four 96-input pipelined adder trees stacked upon each other is shown below on a XCV300 part. The bit-growth in the adder tree helps to identify the recursive nature of the layout.

<p align="center"> <img src="tree_stack96_4_small.jpg"></p>

The adder trees shown above sum 96 9-bit numbers each and with this layout the circuit operates at 173MHz on a XCV300 at speed grade 6. If the layout information is removed and the same netlist is placed and routed again the maximum speed is 129MHz i.e. 34% slower. The graph below shows that specifying layout had significant performance advantages. The blue bars represent the speed of circuits with layout information and the red bars represented the speed of the same netlist with the layout information (RLOCs) removed. Each pair of bars corresponds to a target timing specification. The leftmost pair of bars is for the case where the place and route tools were asked to meet a timing specification of 180MHz but delivered 173MHz for the RLOC-ed circuit and 129MHz without the RLOCs.

<p align="center"> <img src="speed_graph.jpg"></p>

Providing layout information also speeds out the place and route times as shown in the graph below. For the 180MHz timespec case the netlist with layout took less then 400 seconds to route (nothing needs to be placed) but took more than 1800 seconds to place and route without layout information.

<p align="center"> <img src="apr_graph.jpg"></p>

The FPGA Editor view of the four placed adder trees is shown below.

<p align="center"> <img src="adder_tree_fpgaeditor.jpg"></p>

Without the RLOCs the following implementation is produced:

<p align="center"> <img src="adder_tree_norloc_fpgaeditor.jpg"></p>

The adder tree layout was easy to express in Lava and has produced significant performance advantages. Many other tree layout schemes can be easily explored in Lava. It is also very easy to make trees of other kinds of circuits like multiplier etc. All one has to do is to supply such circuits to the first argument of the `tree` combinator.

Next section: [A Sorter Example in Lava](sorter)
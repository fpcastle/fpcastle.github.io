---
layout: tech
title: Netlists
---

# Describing Netlists in Lava

Structural netlists are described in Lava by composing functions that represent basic library elements or composite circuits. This is very similar to the way the structural netlists are described in VHDL or in Verilog which rely on netlist naming to compose circuit elements. Later we present combinators which provide a much more powerful way to combine circuits.
Most hardware description languages provide a library of basic gates that correspond to the Xilinx Unified Library components. Designers can build up netlists by creating instances of these abstract gates and writing them together. The implementation software maps these gates to specific resources on the FPGA. Most logic functionality is mapped into the slices of CLBs. The top half of a Virtex-II slice is shown below:

<p align="center"> <img src="half_slice.jpg"></p>

Lava provides a more direct way to specify the mapping into CLB, slices and LUTs. Just like the Xilinx Unified Library the Xilinx Lava library contains specific functions (circuits) that correspond to resources in a Virtex slice e.g. muxcy and xorcy. However, instead of trying to enumerate every possible one, two, three and four input function that can be realized in a LUT Lava instead provides a higher order function for creating a LUT configuration from a higher level specification of the requited function.
As an example consider the task of configuring a LUT to be a two input AND gate. This can be performed by using the lut2 combinator with an argument that is the Haskell function that performs logical conjunction written as `&`.

```haskell
and2 :: (Bit, Bit) -> Bit
and2 = lut2 (&)
```

The lut2 higher order combinator takes any function of two Boolean parameters and returns a circuit that corresponds to a LUT programmed to perform that function. A circuit in Lava has a type that operates over structures of the Bit type. The and2 circuit is defined to take a pair of bits as input and return a single bit as its output.
Note that `lut2` is a higher order combinator because it takes a function as an argument (the `&` function) and returns a function as a result (the circuit which ANDs its two inputs). Indeed the `lut2` combinator can take any function that has the type `Bool -> Bool -> Bool` and it returns a circuit of type `(Bit, Bit) -> Bit`. The type of the `lut2` function is similar to:

```haskell
lut2 :: (Bool -> Bool -> Bool) -> ((Bit, Bit) -> Bit)
```

When Lava generates a VHDL or EDIF netlist for this component it will instance a LUT with the appropriate programming information. Here is what the generated VHDL for the `and2` gate might look like:

```vhdl
 lut2_1 : lut2 generic map (init => "1000") 
          portmap (i0 => lava(5), i1 => lava (6), 
                   o => lava(4)) ;
```

When realized in the top part of a slice as shown in the picture above this gate would be mapped to the upper G function generator which will be configured as a LUT.
As another example consider the definition of a three input AND-gate. First, we define a function in Haskell that performs the AND3 operation:

```haskell
and3 = lut3 and3function
```

The general idea is that instead of trying to provide a library that tries to enumerate some of the one, two, three and four input logic functions Lava provides four combinators lut1, lut2, lut3 and lut4. These combinators take functions expressed in the embedding language (Haskell) and return LUTs that realize these functions. This makes it convenient to express exactly what gets packed into one LUT. Later we will see that these higher order combinators are actually overloaded which allows LUT contents to be specified in many different kinds of ways (including an integer or a bit-vector).
Lava does provide a module that contains definitions for commonly used gates mapped into LUTs. These include:

```haskell
inv = lut1 not
and2 = lut2 (&)
or2 = lut2 (||)
xor2 = lut2 exor
muxBit sel (d0, d1)= lut3 muxFn (sel, d0, d1)
```

assuming the following functions are available in addition to the Haskell prelude:

```haskell
exor :: Bool -> Bool -> Bool 
exor a b = a /= b

muxFn :: Bool -> Bool -> Bool -> Bool
muxFn sel d0 d1 
  = if sel then
      d1
    else 
      d0
```

To make a netlist that corresponds to a NAND gate one could simply perform direct instantiation and wire up the signals appropriate just like in VHDL or Verilog:

```haskell
nandGate (a, b) = d 
                  where 
                  d = inv c
                  c = and2 (a, b)
```

Lava generates a netlist containing two LUT instantiations for this circuit: a LUT2 to implement the AND gate and a LUT1 to implement the inverter. How many LUTs are used to realize this circuit on the FPGA? If no information is given about the location of these two circuits then the mapper is free to merge both the LUT2 for the AND gate and the LUT1 for the inverter into one LUT. However, if the inverter has been laid out in a different location from the AND gate then the two components will not be merged. By default each gate is at logical position (0.0). Since the description above does not translate the two sub-circuits to another location they are understood to occupy the same function generator location and will be merged into one LUT.
A partial list of some of the basic Virtex slice resources supported by Lava is shown below:

```haskell
gnd :: Bit
vcc :: Bit 
fd :: Bit -> Bit -> Bit
fde :: Bit -> Bit -> Bit -> Bit
muxcy :: (Bit, (Bit, Bit)) -> Bit
muxcy_l :: (Bit, (Bit, Bit)) -> Bit
xorcy :: (Bit, Bit) -> Bit
xorcy_l :: (Bit, Bit) -> Bit
muxf5 :: (Bit, (Bit, Bit)) -> Bit
muxf6 :: (Bit, (Bit, Bit)) -> Bit 
muxf7 :: (Bit, (Bit, Bit)) -> Bit
muxf8 :: (Bit, (Bit, Bit)) -> Bit
```

Next section: [Layout in Lava](layout)
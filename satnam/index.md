---
layout: personal
title: Satnam Singh
---
# Silver Oak
I work at [Google Research](https://research.google/) on the [Silver Oak](https://github.com/project-oak/oak-hardware) project that is investigating
new ways of designing high assurance hardware and software based
on a methodology that starts from specifications and implementations
in the [Coq](https://coq.inria.fr) theorem prover cast in its dependently typed language
[Gallina](https://coq.github.io/doc/v8.9/refman/language/gallina-specification-language.html). We follow an approach inspired by the vision set out by
[Adam Chlipala](http://adam.chlipala.net) at MIT in his book
[Certified Programming with Dependent Types](http://adam.chlipala.net/cpdt/).


# Bio (short)
Satnam Singh works at [Google Research](https://research.google/)
on the [Oak](https://github.com/project-oak/oak) project that is developing technology to promote secure and private computing.
Satnam Singh has worked on projects dealing with custom hardware acceleration and compiler technology
at Facebook, Microsoft and Xilinx and has held academic positions at the University of Glasgow
and the University of Birmingham.

# Bio (long)
Satnam Singh works at [Google Research](https://research.google/)
on the [Oak](https://github.com/project-oak/oak) project that is developing technology to promote secure and private computing.
Satnam has previously worked on topics including functional programming, hardware design, domain-specific languages including [Lava](http://lava.fpcastle.com), compilers, custom hardware acceleration with ASICs and FPGAs, accelerating machine learning algorithms, concurrency and parallelism, formal verification, configuration management, distributed systems, container orchestration, cloud computing and low level Android performance improvement. 
Satnam was an early team member of the [Kubernetes](https://kubernetes.io) project. Satnam has also
held academic positions at The University of Glasgow and The University of Birmingham as well as
visiting and adjuct positions elsewhere (Imperial College London and the University of Washington).
Satnam's professional duties include having been an elected member of the [ACM SIGPLAN](https://www.sigplan.org/) executive committee (until 2018), [IFIP WG2.8](http://www.cs.ox.ac.uk/ralf.hinze/WG2.8) on functional programming and
[IFIP WG2.11](https://wiki.hh.se/wg211/index.php/Main_Page) on program generation.

# Cava Talk Title
**Hardware Design and Verification with Cava**   
*Satnam Singh, Google Research. satnam@google.com*

This talk describes the specification, implementation and formal
verification inside the [Coq](https://coq.inria.fr) theorem prover of the
[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) crypto accelerator component of the
[OpenTitan](https://opentitan.org) silicon roof of trust. The approach
we take is inspired by [Adam Chlipala](http://adam.chlipala.net) in his book
[Certified Programming with Dependent Types](http://adam.chlipala.net/cpdt/).
We have developed a Lava-like domain specific hardware description language called Cava in Coq's
[Gallina](https://coq.github.io/doc/v8.9/refman/language/gallina-specification-language.html) language
which is designed to help describe low level data-path style circuits with a view
to having tight control over resource utilization and performance. This allows
us to specify, implement and formally verify high assurance systems in one model
where specifications are plain Gallina functions that specify the desired
behaviour of hardware components. Our system can extract circuit descriptions
from our Coq DSL to SystemVerilog for implementation on FPGA circuits.

We are also planning on building on this work to also tackle the co-design of
software and hardware where we extract both C code (or RISC-V machine code)
and SystemVerilog and a register or bus-interface so we can reason about
the end-to-end behaviour of a system comprising hardware *and* software.
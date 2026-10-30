___
# SR Latch
An **SR latch** (Set-Reset latch) is a basic bistable circuit that stores one bit of information. It has two inputs, **S** (set) and **R** (reset), and two complementary outputs, **Q** and **Q̅**. It is the simplest storage element.

It is usually described with two **NOR** gates.

![[sr-latch.png| center]]

It can be also described with two **NAND** gates.
![[sr-latch-nand.png| center]]

___
# Gated SR Latch
A **gated SR latch** is an SR latch with an extra **enable (clk)** input that controls when the **S** and **R** inputs are allowed to affect the stored bit.

![[gated-sr-latch.png|center]]

___
# D Latch
A **D latch** (data latch) is a gated SR latch modified so the forbidden input condition can never occur. It has a single data input **D** and an enable input **clk**, with outputs **Q** and **Q̅**.
![[d-latch-1.png| center]]

![[d-latch-2.png]]
___
# D Flip-Flop
A **D flip-flop** (DFF) is an edge-triggered storage element. It captures the value of **D** only at a clock edge (usually the rising edge) and holds it until the next edge, regardless of what D does in between.

![[d-flip-flop.png | center]]
___
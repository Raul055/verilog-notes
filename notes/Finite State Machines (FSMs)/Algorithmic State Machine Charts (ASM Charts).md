___
> [[Index]]

An ASM chart is a flowchart-style notation for FSMs. It looks like a software flowchart, but it is tied to the clock, where everything inside one ASM block happens in a single clock cycle.
___
# ASM Block
An **ASM block** is the unit of an ASM chart. Exactly one state box plus everything that happens in the same clock cycle while the machine is in that state.

![[asm-block.png | center]]

The following is an example of an **ASM Block**.

![[asm-block-example.png]]

There are multiple ways for representing an **ASM Block**. For example, the following represent the same **ASM Block**.

![[asm-block-example-2.png | center]]

Notice that for outputs $S_2$ and $S_3$, the trail is exactly the same. Because of this, they are equivalent. Likewise, an **ASM Block** never has to have *feedback* from the ASM Block itself.

![[asm-block-correct-incorrect.png]]

___

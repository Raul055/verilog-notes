___
> [[Index]]

An ASM chart is a flowchart-style notation for FSMs. It looks like a software flowchart, but it is tied to the clock, where everything inside one **ASM block** happens in a single clock cycle.

# ASM Block
An **ASM block** is the unit of an ASM chart: exactly one state box plus everything that happens in the same clock cycle while the machine is in that state. It starts at the state box and ends where the paths reach the next state boxes (or loop back to this one).
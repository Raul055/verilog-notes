___
> [[Index]]

A **counter** can be interpreted as a FSM where the state is the count. A `mod-N` counter has `N` states, and each clock edge moves it to the next one.

For example, the the following *state diagram* represent a counter from `0-7`.

![[counter-as-fsms-1.png | center]]

The following module represent that FSM.

```verilog
module fsm_counter
	(
		input clk,
		input reset,
		input enable,
		output [2:0] num
	);
	
	reg [2]
	
endmodule
```
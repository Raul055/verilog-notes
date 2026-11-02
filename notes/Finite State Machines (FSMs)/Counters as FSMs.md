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
	
	reg [2:0] state_reg, state_next;
	localparam s0 = 0, s1 = 1, s2 = 2, s3 = 3, s4 = 4, s5 = 5,
			   s6 = 6, s7 = 7;
	
	// State register
	always @(posedge clk, posedge reset)
	begin
		if (reset)
			state_reg <= s0;
		else
			state_reg <= state_next;
	end
	
	// Next state logic
	always @(*)
	begin
		
	end
	
endmodule
```
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
		if (en)
			case(state_reg)
				s0: state_next = s1;
				s1: state_next = s2;
				s2: state_next = s3;
				s3: state_next = s4;
				s4: state_next = s5;
				s5: state_next = s6;
				s6: state_next = s7;
				s7: state_next = s0;
				default: state_next = state_reg;
			endcase
		else
			state_next = state_reg;
	end
	
	// Output logic
	assign num = state_reg;
	
endmodule
```

The following testbench can be used for testing the module.

```verilog
module fsm_counter_tb;
	// I/O
	reg clk;
	reg reset;
	reg enable;
	wire [2:0] num;
	
	// Instantiate modules under test
	fsm_counter uut0 (
		.clk(clk),
		.reset(reset),
		.enable(enable),
		.num(num)
	);

	// Generate a clk signal
	localparam T = 10;
	always
	begin
		clk = 1'b0;
		#(T / 2);
		clk = 1'b1;
		#(T / 2);
	end

	initial
	begin
		reset = 
	end

endmodule
```
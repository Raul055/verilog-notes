___
# `N-bit` Simple Register
We can describe a simple `N-bit` register the following way:
```verilog
module simple_register
	#(parameter N=4)
	(
		input clk,
		input [N-1:0] I,
		output [N-1:0] Q
	);
	
	reg [N-1:0] Q_reg, Q_next;
	
	// Register
	always @(posedge clk)
	begin
		Q_reg <= Q_next;
	end
	
	// Next-state logic
	always @(I)
	begin
		Q_next = I;
	end
	
	// Output logic
	assign Q = Q_reg;
	
endmodule
```

This can be interpreted as the following circuit (`4-bit`).

![[4-bit-register.png | center]]

___
# `N-Bit` Register with Load

We can define a `N-Bit` register the following way.
```verilog
module simple_register_load
	#(parameter N=4)
	(
		input clk,
		input load,
		input [N-1:0] I,
		output [N-1:0] Q
	);
	
	reg [N-1:0] Q_reg, Q_next;
	
	// Register
	always @(posedge clk)
	begin
		Q_reg <= Q_next;
	end
	
	// Next-state logic
	always @(I, load)
	begin
		if(load)
			Q_next = I;
		else
			Q_next = Q_reg;
	end
	
	// Output logic
	assign Q = Q_reg;
	
endmodule
```
Essentially, what we are describing is the following (`4-bit`).
![[4-bit-register-load.png | center]]

___
# `N-Bit` Shift Register
The following code modules a `N-Bit` shift register.
```verilog
module shift_register
	#(parameter N=4)
	(
		input clk,
		input SI, // serial in
		//output [N-1:0], // If we care about the content
		output SO // serial out
	);
	
	reg [N-1:0] Q_reg, Q_next;
	
	// Register
	always @(posedge clk)
	begin
		Q_reg <= Q_next;
	end
	
	// Next-state logic
	always @(SI, Q_reg)
	begin
		// Right shift
		Q_next = {SI, Q_reg[N-1:1]};
		
		// Left shift
		// Q_next = {Q_reg[N-2:0], SI};
	end
	
	// Output logic
	assign SO = Q_reg[0];
	// assign Q = Q_reg; // If content is needed
	
endmodule
```
This can be seen as the following.

![[shift-register.png| center]]
___
# `N-Bit` Shift Register with Load
The following code modules a `N-Bit` shift register wth load.
```verilog
module shift_register_load
	#(parameter N=4)
	(
		input clk,
		input SI, // serial in
		input load,
		input reset,
		output [N-1:0], // If we care about the content
		output SO // serial out
	);
	
	reg [N-1:0] Q_reg, Q_next;
	
	// Register
	always @(posedge clk, posedge reset)
	begin
		// Asynchronous reset
		if(reset)
			Q_reg <= 1'b0;
		else
			Q_reg <= Q_next;
	end
	
	// Next-state logic
	always @(SI, Q_reg)
	begin
		if(load)
			Q_next = I;
		else
			// Right shift
			Q_next = {SI, Q_reg[N-1:1]};
	end
	
	// Output logic
	assign SO = Q_reg[0];
	assign Q = Q_reg; // If content is needed
	
endmodule
```
This can be seen as the following.

![[parallel-access-shift-register.png| center]]
**Note:** *parallel access is NOT described in the module.*
___
# `N-Bit` Universal Shift Register
The following module describes a `N-Bit` universal shift register.
```verilog
module universal_shift_register
	#(parameter N=4)
	(
		input clk, reset,
		input MSB_in, LSB_in,
		input [N-1:0] I,
		input [1:0] s,
		output [N-1:0] Q
	);
	
	reg [N-1:0] Q_reg, Q_next;
	
	// Register
	always @(posedge clk, posedge reset)
	begin
		// Asynchronous reset
		if(reset)
			Q_reg <= 1'b0;
		else
			Q_reg <= Q_next;
	end
	
	// Next-state logic
	always @(Q_reg, MSB_in, LSB_in, I)
	begin
		Q_next = Q_reg;
		case(s)
			2'b00: Q_next = Q_reg;
			2'b01: Q_next = {MSB_in, Q_reg[N-1:0]};
			2'b10: Q_next = {Q_reg[N-2:0], LSB_in};
			2'b11: Q_next = I;
			default: Q_next = Q_reg;
		endcase
	end
	
	// Output logic
	assign Q = Q_reg;
	
endmodule
```

Basically, we are describing the following circuit.

![[universal-shift-register.png | center|624]]
___
___
# Coding Guidelines
#### 1. Use *blocking* (immediate) assignments for combinational circuits.
___
#### 2. Use *nonblocking* (deferred) assignments for registers.
___
#### 3. Assign a variable only in a single `always` block.
Take the following module as an example.
```verilog
module race_conditions
	(
		input a, b,
		output reg x
	);
	
	always @(a, b)
	begin
		x = a & b;
	end
	
	always @(a, b)
	begin
		x = a & b;
	end

endmodule
```
Because two `always` blocks are assigning to the same variable, a *race condition* is created, because we don't really know which `always` block is evaluated first. Remember to avoid ***race conditions***.
___
#### 4. Separate memory components (registers) into individual code segments.

Follow this ***General Synchronous System***.

![[general_synchronous_system.png | center]]

##### Example
The following is an example following the general synchronous system guideline.

![[example_syn_sys_guideline.png | center]]

The module is described as follows:

```verilog
module coding_guidelines
	(
		input a, b, c,
		input clk,
		output f, g
	);
	
	// Output registers
	reg f_reg, f_next, g_reg, g_next;
	
	// Storage elements (flip-flops...)
	always @(posedge clk)
	begin
		f_reg <= f_next;
		g_reg <= g_next;
	end
	
	// Next state logic
	always @(a, b, c, g_reg)
	begin
		f_next = a & ~g_reg;
		g_next = b | c;
	end
	
	// Output logic
	assign f = f_reg;
	assign g = g_reg;
	
endmodule
```

___
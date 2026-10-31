___

Consider the following module.
```verilog
module some_circuit
	(
		// I/O
	);
	
	/* Continuous assignment */
	// ---LHS---RHS--------//
	assign x = (a & b) | c;
	
	always @(*)
	begin
		/* Procedural assignment */
		// ---LHS---RHS--------//
		y = (a & b) | c;
	end
	
endmodule
```

- **Continuous assignment**: the *LHS* (`x`) is updated automatically when a change in *RHS* is done. Usually the keyword `assign` is needed.
- **Procedural assignment**: a change in *RHS* does NOT yield a change in *LHS* (`y`). *LHS* updates ONLY when the `always` statement is triggered. Lives in the `always` block.
___
## Procedural assignments

Procedural assignments contain **blocking** and **nonblocking** assignments.

The following correspond to a **Blocking assignment**
```verilog
module some_circuit
	(
		// I/O
	);
	
	always @(*)
	begin
		// Blocking Assignment
		y = (a & b) | c;
	end
	
endmodule
```

The following correspond to a **Nonblocking assignment**
```verilog
module some_circuit
	(
		// I/O
	);
	
	always @(*)
	begin
		// Nonblocking Assignment
		y <= (a & b) | c;
	end
	
endmodule
```
___
### Blocking assignments

#### Combinational Circuits
Consider the following module for a combinational circuit.
```verilog
module combinational_blocking
	(
		input a, b, c,
		output reg x
	);
	
	always @(a, b, c)
	begin
		x = a;
		x = x ^ b;
		x = x | c;
	end
	
endmodule
```
In a blocking assignment, each line inside of the `always` block is *blocking* the next statement. So, in the following assignment, first the equivalence is done, then the XOR, and finally the OR.
```verilog
always @(a, b, c)
	begin
		x = a; // 1
		x = x ^ b; // 2
		x = x | c; // 3
	end
```
This can be translated as
```verilog
always @(a, b, c)
	begin
		/* 
			--- 1 ---
			x = a;
			--- 2 ---
			x = a ^ b;
			--- 3 ---
			x = a ^ b | c;
		*/
		x = a ^ b | c;
	end
```
This can be seen as:

![[continuous_blocking_assignment.png]]

#### Sequential Circuits
Consider the following flip flop module.

```verilog
module ff_blocking
	(
		input D, clk,
		output reg Q1, Q2
	);
	
	always @(posedge clk)
	begin
		Q1 = D;
		Q2 = Q1;
	end
	
endmodule
```

In this blocking assignment, due to being a blocking assignment, the `Q2` register updates AUTOMATICALLY. So `Q1` and `Q2` are describing the same. In other way, like this:

![[sequential_blocking_assignment.png | center]]
That is why *blocking assignments* **should NOT** be used in sequential circuits that use registers of flip-flops.

___
### Nonblocking assignments

#### Combinational Circuits
Consider the following module for a combinational circuit.
```verilog
module combinational_nonblocking
	(
		input a, b, c,
		output reg x
	);
	
	always @(a, b, c)
	begin
		x <= a;
		x <= x ^ b;
		x <= x | c;
	end
	
endmodule
```
In a nonblocking assignment, each line inside of the `always` block does NOT affect the next statement. So, in the following assignment, each statment does not interfer with one another.
```verilog
always @(a, b, c)
	begin
		x <= a; // 1
		x <= x ^ b; // 2
		x <= x | c; // 3
	end
```
This can be translated as
```verilog
always @(a, b, c)
	begin
		/* 
			--- 1 ---
			x <= a;
			--- 2 ---
			x <= x ^ b;
			--- 3 ---
			x = x | c;
		*/
		x <= x | c;
	end
```
This can be seen as:
![[continuous_nonblocking_assignment.png]]
As seen, the 'feedback loop' does not make sense. That is why, *nonblocking assignments* **should NOT** be used for combinational circuits (you CAN but you SHOULD NOT).

#### Sequential circuits
Consider the following flip flop module.

```verilog
module ff_nonblocking
	(
		input D, clk,
		output reg Q1, Q2
	);
	
	always @(posedge clk)
	begin
		Q1 <= D;
		Q2 <= Q1;
	end
	
endmodule
```

Here, `Q1` and `Q2` are not blocked, so the order in which `Q1` and `Q2` are described does not matter. Hence, because `Q2` is assigned the value of `Q1` but `Q1` is not assigned yet, the following circuit is inferred.

![[sequential_nonblocking_assignment.png | center]]

That is why, *nonblocking assignments* shall be preffered on sequential circuits.

___

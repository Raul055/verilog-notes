___
> [[Index]]

# Debouncing
Debouncing is the technique of ignoring the rapid, spurious on/off transitions a mechanical button produces when pressed or released, so that one physical press is registered as exactly one event.

![[debouncing-diagram.png | center]]

There are two main ways to debounce a button input:
- **Delay detection**: only consider the button input after a $t$ time. Ideally, the button shall reach the **steady state** after the $t$ time passed. As its name implies, it provides a *delayed* output.
- **Early detection**: it detects a change in the input (from *last state* to *current state*) and applies it right away, ignoring the subsequent changes after $t$ time. It provides a quicker response than the *delayed detection*, but, if *noise* is present, it can give a *false positive*.
___
# Debouncer FSM

We can implement the FSM as follows.

![[debouncer-fsm.png | center]]

The following module represent the `Debouncer FSM`.

```verilog
module debouncer_delayed_fsm
	(
		input clk, reset,
		input noisy, timer_done,
		output timer_reset, debounced
	);
	
	reg [1:0] state_reg, state_next;
	parameter s0 = 0, s1 = 1, s2 = 2, s3 = 3;
	
	// Sequential state register
	always @(posedge clk, posedge reset)
	begin
		if(reset)
			state_reg <= 0;
		else
			state_reg <= state_next;
	end
	
	// Next state logic
	always @(*)
	begin
		state_next = state_reg;
		case(state_reg)
			s0: if(~noisy)
					state_next = s0;
				else if (noisy)
					state_next = s1;
			s1: if(~noisy)
					state_next = s0;
				else if (noisy & ~timer_done)
					state_next = s1;
				else if (noisy & timer_done)
					state_next = s2;
			s2: if(~noisy)
					state_next = s3;
				else if (noisy)
					state_next = s2;
			s3: if(noisy)
					state_next = s2;
				else if (~noisy & ~timer_done)
					state_next = s3;
				else if (~noisy & timer_done)
					state_next = s0;
			default: state_next = s0;
		endcase
	end

	// Output logic
	assign timer_reset = (state_reg == s0) | (state_reg == s2);
	assign debounced = (state_reg == s2) | (state_reg == s3);

endmodule
```

The FSM expect a timer for determining the debounce time $t$. For that, we can implement it using the `timer_parameter` module from [[Counters as Timers]]. See the next module as an example.

```verilog
module debouncer_delayed
	(
		input clk, reset,
		input noisy,
		output debounced
	);
	
	wire timer_done, timer_reset;
	
	debouncer_delayed_fsm FSM0(
		.clk(clk),
		.reset(reset),
		.noisy(noisy),
		.timer_done(timer_done),
		.timer_reset(timer_reset),
		.debounced(debounced)
	);
	
	// 20 ms timer for 100 MHz clock
	timer_parameter #(.FINAL_VALUE(1_999_999)) T0(
		.clk(clk),
		.reset(~timer_reset),
		.enable(~timer_reset),
		.done(timer_done)
	);

endmodule
```
___
# Edge Detection FSM
We can implement the FSM as follows.

![[edge-detection-fsm.png | center]]

The following module represent the `Edge Detector`.
```verilog
module edge_detector
	(
		input clk,
		input reset,
		input level,
		output p_edge, n_edge, _edge
	);

	// Edge detector with Mealy outputs
	reg state_reg, state_next;
	parameter s0 = 0, s1 = 1;
	
	// Sequential state registers
	always @(posedge clk, posedge reset)
	begin
		if(reset)
			state_reg <= s0;
		else
			state_reg <= state_next;
	end
	
	// Next state logic
	always @(*)
	begin
		case(state_reg)
			s0: if(level)
					state_next = s1;
				else
					state_next = s0;
			s1: if(level)
					state_next = s1;
				else
					state_next = s0;
			default: state_next = s0;
		endcase
	end
	
	// Output logic
	assign p_edge = (state_reg == s0) & level;
	assign n_edge = (state_reg == s1) & ~level;
	assign _edge = p_edge | n_edge;
	
endmodule
```

We can implement a full button driver with bebounced button and edge detection.

```verilog
module button
	(
		input clk,
		input reset,
		input noisy,
		output debounced,
		output p_edge,
		output n_edge,
		output _edge
	);
	
	// For clean button
	debouncer_delayed DD0(
		.clk(clk),
		.reset(reset),
		.noisy(noisy),
		.debounced(debounced),
	);
	
	// For edge detection from clean button
	edge_detector ED0(
		.clk(clk),
		.reset(reset),
		.level(debounced),
		.p_edge(p_edge),
		.n_edge(n_edge),
		._edge(_edge)
	);

endmodule
```
  
___
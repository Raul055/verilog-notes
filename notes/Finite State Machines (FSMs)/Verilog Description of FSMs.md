___
> **[[Index]]** 

As seen in [[Design of FSMs]], the obtention of a circuit from the design of a FSM can be long. WIth that, CAD tools can help us simplify the process, more specifically:

1. *Draw the state diagram based on specifications & desired behavior.*
2. *Reduce the number of states if necessary.*
3. *Assign binary values to states.*
4. *Obtain the binary-coded state table.*
5. *Choose FF type.*
6. *Get simplified FF input equations & output equations.*
7. *Draw circuit diagram.*

As seen, these are **design steps** when designing a FSM. In reality, we only need to focus on step 1, which is the creation of the **state diagram** for the implementation of a FSM. The other steps can be inferred by vivado.

For example, taking the **Mealy machine** example from [[Design of FSMs]], we can build our FSM based on the **state diagram**.

![[description-fsm-state-diagram-1.png | center]]

The following module represent the FSM of the `101 Mealy Detector`.
```verilog
module mealy_101_detector
	(
		input clk,
		input reset,
		input x,
		input y
	);
	
	reg [1:0] state_reg, state_next;
	// States from FSM
	localparam s0 = 0; // reset state
	localparam s1 = 1;
	localparam s2 = 2;
	
	// State register
	always @(posedge clk, posedge reset)
	begin
		if(reset)
			state_reg <= s0; // go to reset state
		else
			state_reg <= state_next;
	end
	
	// Next state logic
	always @(*)
	begin
		case(state_reg)
			s0: 
			begin
				if(x)
					state_next = s1;
				else
					state_next = s0;
			end
			s1:
			begin
				if(x)
					state_next = s1;
				else
					state_next = s2;
			end
			s2:
			begin
				if(x)
					state_next = s1;
				else
					state_next = s0;
			end
			default:
		endcase
	end
	
	// Output logic

endmodule
```
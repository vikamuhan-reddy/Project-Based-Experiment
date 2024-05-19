### Project-Based-Experiment

**AIM:**

To design and simulate the traffic light controller.

**SOFTWATE USED:**

Quartus II

**THEORY:**
	
     	Consider a controller for traffic at the intersection of three main roads.  

  ![image](https://github.com/naavaneetha/Project-Based-Experiment/assets/154305477/e3af03dd-a4de-4b21-af0a-a5a332a3e4b6)


 The traffic signal for all the three main roads have equal priority and they remain red by default.

 In state 00,the traffic signals remains red for first five counts and yellow of road1 turns on for the next four counts.

 In state 01, the green of road1 turns on for first five counts and yellow of road1 and road2 turns on for next four4 counts of this state.
 
 In state 10, the traffic signal of road1 comes back to the red and that of road2 goes to green for tee first five counts.For the next four counts the traffic signal of road2 and road3 remains yellow.


 In state 11, the traffic signal of road2 comes back to the red and that of road3 goes to green for the first five counts.For the next four counts the traffic signal of road3 turns to yellow

 At the end of four states,the traffic signal of all the three roads come back to red.

**Task Assigned**

From the HDL code given formulate the correct code  to divert the traffic to path 1 direction and disable the control in other directions (Assume user is at MR3 spot)

**Procedure**

1.	Type the program in Quartus software.
2.	Compile and run the program.
3.	Generate the RTL schematic and save the logic diagram.
4.	Create nodes for inputs and outputs to generate the timing diagram.
5.	For different input combinations generate the timing diagram
   
**Program:**

/* Program to implement the given logic function and to verify its operations in quartus using Verilog programming. 
Developed by: Vikamuhan.N
RegisterNumber: 212223240181
*/
```
module project_1 (
    input wire clk,          // Clock signal
    input wire reset,        // Reset signal
    input wire userAtMR3,    // User presence at MR3
    output reg [1:0] light1, // Traffic light for MR1 (00 = Red, 01 = Green, 10 = Yellow)
    output reg [1:0] light2, // Traffic light for MR2 (00 = Red, 01 = Green, 10 = Yellow)
    output reg [1:0] light3  // Traffic light for MR3 (00 = Red, 01 = Green, 10 = Yellow)
);

    // State encoding using localparam
    localparam S0 = 2'b00;
    localparam S1 = 2'b01;
    localparam S2 = 2'b10;
    localparam S3 = 2'b11;

    reg [1:0] current_state, next_state;
    reg [3:0] counter;

    // Parameters for timing
    parameter RED_TIME = 5;
    parameter YELLOW_TIME = 4;
    parameter GREEN_TIME = 5;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= S0;
            counter <= 0;
        end else if (userAtMR3) begin
            // When user is at MR3, set MR1 to green and others to red
            current_state <= S1; // Directly transition to state S1 for MR1 green light
            counter <= GREEN_TIME;
        end else begin
            if (counter == 0) begin
                current_state <= next_state;
                counter <= (next_state == S0 || next_state == S2) ? RED_TIME : YELLOW_TIME;
                if (next_state == S1 || next_state == S3) begin
                    counter <= GREEN_TIME;
                end
            end else begin
                counter <= counter - 1;
            end
        end
    end

    // Next state logic
    always @(*) begin
        case (current_state)
            S0: next_state = (counter == 0) ? S1 : S0;
            S1: next_state = (counter == 0) ? S2 : S1;
            S2: next_state = (counter == 0) ? S3 : S2;
            S3: next_state = (counter == 0) ? S0 : S3;
            default: next_state = S0;
        endcase
    end

    // Output logic
    always @(*) begin
        // Default all lights to red
        light1 = 2'b00;
        light2 = 2'b00;
        light3 = 2'b00;

        case (current_state)
            S0: begin
                // State 0: All red, MR1 yellow for 4 counts
                if (counter < YELLOW_TIME) light1 = 2'b10; // MR1 Yellow
            end
            S1: begin
                // State 1: MR1 Green for 5 counts, MR1 yellow and MR2 yellow for 4 counts
                if (counter >= (RED_TIME - GREEN_TIME)) light1 = 2'b01; // MR1 Green
                else light1 = 2'b10; // MR1 Yellow
                if (counter < YELLOW_TIME) light2 = 2'b10; // MR2 Yellow
            end
            S2: begin
                // State 2: MR2 Green for 5 counts, MR2 and MR3 yellow for 4 counts
                if (counter >= (RED_TIME - GREEN_TIME)) light2 = 2'b01; // MR2 Green
                else light2 = 2'b10; // MR2 Yellow
                if (counter < YELLOW_TIME) light3 = 2'b10; // MR3 Yellow
            end
            S3: begin
                // State 3: MR3 Green for 5 counts, MR3 Yellow for 4 counts
                if (counter >= (RED_TIME - GREEN_TIME)) light3 = 2'b01; // MR3 Green
                else light3 = 2'b10; // MR3 Yellow
            end
            default: begin
                light1 = 2'b00; // Default to Red
                light2 = 2'b00; // Default to Red
                light3 = 2'b00; // Default to Red
            end
        endcase
    end

endmodule
```

**RTL Schematic**
![image](https://github.com/vikamuhan-reddy/Project-Based-Experiment/assets/144928933/f7c6f1fb-73c9-4c29-8f40-8dae2d6749b4)



**Output Timing Waveform**
![image](https://github.com/vikamuhan-reddy/Project-Based-Experiment/assets/144928933/c8636655-9fc4-4445-ae78-a2654fa6c099)


**Result:**
The Verilog traffic light controller efficiently manages congestion by prioritizing MR1 when user presence at MR3 is detected, ensuring smooth traffic flow through controlled transitions of lights based on predefined timing sequences, confirmed by simulation results.




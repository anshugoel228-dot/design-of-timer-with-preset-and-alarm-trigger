# design-of-timer-with-preset-and-alarm-trigger
`timescale 1ns/1ps
module timer (
    input clk,
    input reset,
    input enable,
    input [3:0] preset,
    output reg [3:0] count,
    output reg alarm
);

always @(posedge clk or posedge reset) begin
    if (reset) begin
        count <= 0;     // CHANGE 1
        alarm <= 0;
    end 
    else if (enable) begin
        if (count < preset) begin
            count <= count + 1;   // CHANGE 2
            alarm <= 0;
        end 
        else if(count==preset)begin
         alarm <= 1;
        end
    end
end

endmodule



#TESTBENCH

`timescale 1ns/1ps

module timer_tb;

reg clk;
reg reset;
reg enable;
reg [3:0] preset;
wire [3:0] count;
wire alarm;

// Instantiate DUT
timer uut (
    .clk(clk),
    .reset(reset),
    .enable(enable),
    .preset(preset),
    .count(count),
    .alarm(alarm)
);

// Clock generation
always #5 clk = ~clk;

initial begin
    // Initialize
    clk = 0;
    reset = 1;
    enable = 0;
    preset = 4'd5;   // target value

    // Apply reset
    #10 reset = 0;
    enable = 1;

    // Let it run
    #100;

    $stop;
end

// Monitor
initial begin
    $monitor("Time=%0t | Count=%d | Alarm=%b", $time, count, alarm);
end

endmodule

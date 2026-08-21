# Design_and_Simulate_the_Memory_Design_using_verilog_HDL
# EXP NO:5. Design-and-Simulate the-Memory-Design-using-Verilog-HDL
# Aim 
To design and simulate a RAM,ROM,FIFO using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 environment. 

# Apparatus Required 
Vivado 2023.1 

# Procedure
1. Launch Vivado 2023.1 Open Vivado and create a new project.
2. Design the Verilog Code Write the Verilog code for the RAM,ROM,FIFO
3. Create the Testbench Write a testbench to simulate the memory behavior. The testbench should apply various and monitor the corresponding output.
4. Create the Verilog Files Create both the design module and the testbench in the Vivado project.
5. Run Simulation Run the behavioral simulation to verify the output.
6. Observe the Waveforms Analyze the output waveforms in the simulation window, and verify that the correct read and write operation.
7. Save and Document Results Capture screenshots of the waveform and save the simulation logs. These will be included in the lab report.

# Code
# RAM
// Verilog code
```
module ram_8x8 (
    input  wire        clk,
    input  wire        we,        
    input  wire [2:0]  addr,      
    input  wire [7:0]  din,       
    output reg  [7:0]  dout       
);
    reg [7:0] memory [0:7];       
    always @(posedge clk) begin
        if (we)
            memory[addr] <= din;
    end
    always @(*) 
    begin
        dout = memory[addr];
    end
endmodule
```
// Test bench
```
module tb_ram_8x8;
reg clk;
reg we;
reg [2:0] addr;
reg [7:0] din;
wire [7:0] dout;

ram_8x8 uut (
    .clk(clk),
    .we(we),
    .addr(addr),
    .din(din),
    .dout(dout)
);
always #5 clk = ~clk;

initial 
begin
    clk = 0;
    we  = 0;
    addr = 0;
    din = 0;
    #10 we = 1; addr = 3'b000; din = 8'hA1;
    #10 addr = 3'b001; din = 8'hB2;
    #10 addr = 3'b010; din = 8'hC3;
    #10 we = 0;
    #10 addr = 3'b000;
    #10 addr = 3'b001;
    #10 addr = 3'b010;

    #20 $stop;
end

endmodule
```
// output Waveform
<img width="1920" height="1080" alt="638598809-a1c8e1be-acee-408a-9586-0094745676e3" src="https://github.com/user-attachments/assets/5ce16fff-f7fb-42c5-befc-9a0c05198529" />

# ROM
// write verilog code for ROM using $random
```
`timescale 1ns / 1ps
module rom_random (
    input  wire [2:0] addr,  
    output reg  [7:0] data    
);
    reg [7:0] memory [0:7];  

    initial
    begin
        for (i = 0; i < 8; i = i + 1) begin
            memory[i] = $random;
        end
    end
    always @(*)
    begin
        data = memory[addr];
    end
endmodule
```
// Test bench
```
`timescale 1ns / 1ps
module tb_rom_random;
reg  [2:0] addr;
wire [7:0] data;
rom_random uut (
    .addr(addr),
    .data(data)
);
initial
begin
    addr = 3'b000;
    #10 addr = 3'b001;
    #10 addr = 3'b010;
    #10 addr = 3'b011;
    #10 addr = 3'b100;
    #10 addr = 3'b101;
    #10 addr = 3'b110;
    #10 addr = 3'b111;
    #10 $stop;
end
endmodule
```
// output Waveform
<img width="1920" height="1080" alt="638600719-b2bb36e2-1450-4f82-9302-d02b0ea82ca4" src="https://github.com/user-attachments/assets/c4555a2e-6d69-4688-89b8-3e67da484a12" />

# FIFO
// write verilog code for FIFO
```
`timescale 1ns / 1ps

module fifo_8x8 (
    input  wire        clk,
    input  wire        rst,
    input  wire        wr_en,
    input  wire        rd_en,
    input  wire [7:0]  din,
    output reg  [7:0]  dout,
    output wire        full,
    output wire        empty
);
    reg [7:0] memory [0:7];   
    reg [2:0] wr_ptr = 0;
    reg [2:0] rd_ptr = 0;
    reg [3:0] count  = 0;    

    assign full  = (count == 8);
    assign empty = (count == 0);

    always @(posedge clk or posedge rst) 
    begin
        if (rst) 
        begin
            wr_ptr <= 0;
            rd_ptr <= 0;
            count  <= 0;
            dout   <= 0;
        end
        else begin
            if (wr_en && !full) 
            begin
                memory[wr_ptr] <= din;
                wr_ptr <= wr_ptr + 1;
                count  <= count + 1;
            end
            if (rd_en && !empty) 
            begin
                dout <= memory[rd_ptr];
                rd_ptr <= rd_ptr + 1;
                count  <= count - 1;
            end
        end
    end
endmodule
```
// Test bench
```
`timescale 1ns / 1ps
module tb_fifo_8x8;
reg clk;
reg rst;
reg wr_en;
reg rd_en;
reg [7:0] din;
wire [7:0] dout;
wire full;
wire empty;

fifo_8x8 uut (
    .clk(clk),
    .rst(rst),
    .wr_en(wr_en),
    .rd_en(rd_en),
    .din(din),
    .dout(dout),
    .full(full),
    .empty(empty)
);

always #5 clk = ~clk;

initial 
begin
    clk = 0;
    rst = 1;
    wr_en = 0;
    rd_en = 0;
    din = 0;
    #10 rst = 0;
    #10 wr_en = 1; din = 8'hA1;
    #10 din = 8'hB2;
    #10 din = 8'hC3;
    #10 wr_en = 0;
    #10 rd_en = 1;
    #30 rd_en = 0;

    #20 $stop;
end
endmodule
```
// output Waveform
<img width="1920" height="1080" alt="638602561-121c0890-40fc-4bc6-8435-ba22c49a99f9" src="https://github.com/user-attachments/assets/6b5f03bc-66bc-4258-9630-d0858b96b2b0" />

# Conclusion
The RAM, ROM, FIFO memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes.

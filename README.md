# SYNCHRONOUS-FIFO-

#  SYNCHRONOUS-FIFO USING VERILOG


In Synchronous FIFO, data read and write operations use the same clock frequency. Usually, they are used with high clock frequency to support high-speed systems.

SIGNALS :

wr_en: write enable

wr_data: write data

full: FIFO is full

empty: FIFO is empty

rd_en: read enable

rd_data: read data

w_ptr: write pointer

r_ptr: read pointer

FIFO WRITE OPERATION:

FIFO can store/write the wr_data at every posedge of the clock based on wr_en signal till it is full. The write pointer gets incremented on every data write in FIFO memory.


FIFO READ OPERATION :

The data can be taken out or read from FIFO at every posedge of the clock based on the rd_en signal till it is empty. The read pointer gets incremented on every data read from FIFO memory.


![image](https://github.com/Prasanna300/SYNCHRONOUS-FIFO/assets/167746764/af6b15bf-7fdf-425a-ba91-5aa1354210d8)



VERILOG CODE:

****
**  the entire synchronous fifo was designed using vivado EDA tool by behavioral modelling method.******

             

*      
      module fifo_sync
   
       #( parameter FIFO_DEPTH = 8,
	   parameter DATA_WIDTH = 32)
   
    	(input clk, 
       input rst,
      input cs,      
      input wr_en, 
      input rd_en, 
      input [DATA_WIDTH-1:0] data_in, 
      output reg [DATA_WIDTH-1:0] data_out, 
	  output empty,
	  output full); 

      localparam FIFO_DEPTH_LOG = $clog2(FIFO_DEPTH);
	
   
      reg [DATA_WIDTH-1:0] fifo [0:FIFO_DEPTH-1];
	
	
      reg [FIFO_DEPTH_LOG:0] write_pointer;
      reg [FIFO_DEPTH_LOG:0] read_pointer;


      always @(posedge clk or negedge rst) 
      begin
      if(!rst)
		    write_pointer <= 0;
      else if (cs && wr_en && !full) begin
          fifo[write_pointer[FIFO_DEPTH_LOG-1:0]] <= data_in;
	       write_pointer <= write_pointer + 1'b1;
      end
      end
  

    	always @(posedge clk or negedge rst) 
      begin
	    if(!rst)
		    read_pointer <= 0;
      else if (cs && rd_en && !empty) begin
          	data_out <= fifo[read_pointer[FIFO_DEPTH_LOG-1:0]];
	        read_pointer <= read_pointer + 1'b1;
      end
    	end
	
	
        assign empty = (read_pointer == write_pointer);
    	assign full  = (read_pointer == {~write_pointer[FIFO_DEPTH_LOG], write_pointer[FIFO_DEPTH_LOG-1:0]});

  
 
      endmodule


SCHEMATIC OF SYNCRONOUS FIFO:
<img width="1493" height="800" alt="Screenshot 2025-07-18 120200" src="https://github.com/user-attachments/assets/52fa4ffa-387c-4482-89c2-04f855723389" />


TEST BENCH FOR VERIFICATION :

*


     module tb_fifo_sync();

   
      parameter FIFO_DEPTH = 8;
     parameter DATA_WIDTH = 32;

  
     reg clk = 0;
     reg rst;
     reg cs;
     reg wr_en;
     reg rd_en;
     reg [DATA_WIDTH-1:0] data_in;
     wire [DATA_WIDTH-1:0] data_out;
     wire empty;
     wire full;

     integer i;

   
     fifo_sync #(
        .FIFO_DEPTH(FIFO_DEPTH),
        .DATA_WIDTH(DATA_WIDTH)
     ) dut (
        .clk(clk),
        .rst(rst),
        .cs(cs),
        .wr_en(wr_en),
        .rd_en(rd_en),
        .data_in(data_in),
        .data_out(data_out),
        .empty(empty),
        .full(full)
     );

  
     always #5 clk = ~clk;

    
     task write_data(input [DATA_WIDTH-1:0] value);
        begin
            @(posedge clk);
            cs = 1;
            wr_en = 1;
            data_in = value;
            $display("Time %0t: Write -> %0d", $time, value);
            @(posedge clk);
            wr_en = 0;
        end
     endtask

   
     task read_data();
        begin
            @(posedge clk);
            cs = 1;
            rd_en = 1;
            @(posedge clk);
            $display("Time %0t: Read  -> %0d", $time, data_out);
            rd_en = 0;
        end
     endtask

   
     initial begin
       
        rst = 0;
        cs = 0;
        wr_en = 0;
        rd_en = 0;
        data_in = 0;

      
        #10 rst = 1;
        #10 rst = 0;
        #10 rst = 1;

        $display("\n--- Scenario 1: Basic Write & Read ---");
        write_data(11);
        write_data(10);
        write_data(2003);
        read_data();
        read_data();
        read_data();

      
        $display("\n--- Scenario 2: Fill and Empty FIFO ---");
        for (i = 0; i < FIFO_DEPTH; i = i + 1) begin
            write_data(i * 11);
        end

        for (i = 0; i < FIFO_DEPTH; i = i + 1) begin
            read_data();
        end

      
        $display("\n--- Test Completed ---");
        #20 $finish;
     end

  
     initial begin
        $dumpfile("fifo_sync.vcd");
        $dumpvars(0, tb_fifo_sync);
     end
     endmodule
SIMULATION OUTPUT :

<img width="1572" height="839" alt="Screenshot 2025-07-18 115025" src="https://github.com/user-attachments/assets/cf31af7a-cb00-4e58-92de-f27c0f509300" />


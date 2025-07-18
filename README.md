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


             the entire synchronous fifo was designed using vivado EDA tool by behavioral modelling method.

             

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

![image](https://github.com/Prasanna300/SYNCHRONOUS-FIFO/assets/167746764/34d8dfb3-1839-4587-bb1e-71bc6597ec96)




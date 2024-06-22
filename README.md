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

             

*      `timescale 1ns / 1ps
       //////////////////////////////////////////////////////////////////////////////////
       // Company: 
       // Engineer: 
      // 
      // Create Date: 24.05.2024 08:26:14
      // Design Name: 
      // Module Name: synchronous fifo
      // Project Name: 
      // Target Devices: 
      // Tool Versions: 
       // Description: 
      // 
      // Dependencies: 
      // 
      // Revision:
      // Revision 0.01 - File Created
      // Additional Comments:
      // 
      //////////////////////////////////////////////////////////////////////////////////


        module synchronousfifo(
       input clk,
       input rst,
      input Wen,
      input Ren,
      input [7:0] data_in,
       output [7:0] data_out,
      output fifo_full,
      output fifo_empty,
      output [7:0] fifo_counter
       );
      reg [7:0] data_out;
      reg   fifo_full;
       reg   fifo_empty;
      reg [7:0]  fifo_counter;
      reg [3:0]r_ptr, w_ptr;
      reg [7:0] RAM[0:63];
    
      always @(fifo_counter)  
      begin
         fifo_full <= (fifo_counter == 63);
         fifo_empty <= (fifo_counter == 0);
       end

      always @(posedge clk or posedge rst)
      begin
      if (rst)
           fifo_counter <= 0;
      else if ((!fifo_full && Wen) && (!fifo_empty && Ren))
            fifo_counter <= fifo_counter ;
      else if (!fifo_full && Wen)
           fifo_counter <= fifo_counter + 1;
      else if (!fifo_empty && Ren)
           fifo_counter <= fifo_counter - 1;
      else
           fifo_counter <= fifo_counter;
      end

      always @(posedge clk or posedge rst)
      begin   
      if (rst)
            data_out <= 0; 
      else if (!fifo_empty && Ren)
            data_out <= RAM[r_ptr] ;
      else 
            data_out <= data_out;
      end  

       always @(posedge clk )
      begin
      if (!fifo_full && Wen)
         DPRAM[w_ptr] <= data_in;
       else
         DPRAM[w_ptr] <= RAM[w_ptr];
      end 

      always @(posedge clk or posedge rst)
      begin
      if (rst)
      begin
         w_ptr <= 0;
         r_ptr <= 0;
      end

   
      else 
      begin
      if (!fifo_full && Wen)
          w_ptr <= w_ptr + 1;
      else 
          w_ptr = w_ptr; 
       end

    
      begin
      if (!fifo_empty && Ren)
        r_ptr <= r_ptr + 1;
      else
        r_ptr <= r_ptr;
       end       
       end
      endmodule




SCHEMATIC OF SYNCRONOUS FIFO:

![image](https://github.com/Prasanna300/SYNCHRONOUS-FIFO/assets/167746764/34d8dfb3-1839-4587-bb1e-71bc6597ec96)




module sync_fifo(clk, rst,  din, we, dout, re,
full, empty);

parameter dw=8;
parameter aw=8;
parameter n=32;
parameter max_size = 1<<aw;

input clk, rst;
input [dw-1:0] din;
input we;
output [dw-1:0] dout;
input re;
output full;
output empty;


////////////////////////////////////////////////////////////////////
//
// Local Wires
//

reg [aw-1:0] wp;
wire [aw-1:0] wp_pl1;
wire [aw-1:0] wp_pl2;
reg [aw-1:0] rp;
wire [aw-1:0] rp_pl1;
reg gb;
reg gb2;
reg [aw:0] cnt;


////////////////////////////////////////////////////////////////////
//
// Memory Block
//

 generic_dpram u0(
.rclk( clk ),
.rrst( !rst ),
.rce( 1'b1 ),
.oe( 1'b1 ),
.raddr( rp ),
.do( dout ),
.wclk( clk ),
.wrst( !rst ),
.wce( 1'b1 ),
.wr_en( we ),
.rd_en( re ),
.waddr( wp ),
.di( din )
);

////////////////////////////////////////////////////////////////////
//
// Misc Logic
//

always @(posedge clk )
if(!rst) 
    wp <= {aw{1'b0}};

else if(we) 
    wp <=  wp_pl1;

assign wp_pl1 = wp + { {aw-1{1'b0}}, 1'b1};
assign wp_pl2 = wp + { {aw-2{1'b0}}, 2'b10};

always @(posedge clk )

if(!rst) 
    rp <=  {aw{1'b0}};

else if(re) 
    rp <=  rp_pl1;

assign rp_pl1 = rp + { {aw-1{1'b0}}, 1'b1};

////////////////////////////////////////////////////////////////////
//
// Combinatorial Full & Empty Flags
//

assign empty = ((wp == rp) & !gb);
assign full  = ((wp == rp) &  gb);

// Guard Bit ...
always @(posedge clk )
if(!rst) 
    gb <=  1'b0;

else if((wp_pl1 == rp) & we) 
    gb <=  1'b1;

else if(re) 
    gb <=  1'b0;

////////////////////////////////////////////////////////////////////
//
// Registered Full & Empty Flags
//

// Guard Bit ...
always @(posedge clk )
if(!rst) gb2 <=  1'b0;

else  if((wp_pl2 == rp) & we) gb2 <=  1'b1;

else if((wp != rp) & re) gb2 <=  1'b0;



////////////////////////////////////////////////////////////////////
//
// Combinatorial Full_n & Empty_n Flags
//



// N entries status
always @(posedge clk )

if(!rst) cnt <=  {aw + 1{1'b0}};

else if( re & !we) cnt <=  cnt + { {aw{1'b1}}, 1'b1};

else if(!re &  we) cnt <=  cnt + { {aw{1'b0}}, 1'b1};

////////////////////////////////////////////////////////////////////
//
// Registered Full_n & Empty_n Flags
//



////////////////////////////////////////////////////////////////////
//
// Sanity Check
//

// synopsys translate_off
always @(posedge clk)
if( full)
$display("%m WARNING: Writing while fifo is FULL (%t)",$time);

always @(posedge clk)
if( empty)
$display("%m WARNING: Reading while fifo is EMPTY (%t)",$time);
// synopsys translate_on

endmodule

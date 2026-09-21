`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 10.09.2026 15:56:06
// Design Name: 
// Module Name: magnitude_cdc
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


module magnitude_cdc(
    input  clkA, clkB, reset,
    input  [31:0] d_in,
    input  done0_in,
    input  wr_enable,
    output reg [31:0] d_out,
    output reg done0,
    output ready
    );
// Internal Storage
reg [31:0] magnitude;
reg done;

// Handshake signals 
reg req; // clkA generates
reg ack; // clkB generates

// Synchronizer Registers
reg req_meta, req_sync; // for clkB
reg ack_meta, ack_sync; // for clkA

// Finalization Handshake
assign ready = !req && !ack_sync;

// --------------------------
// clkA Domain - Recieving
// --------------------------
always@(posedge clkA) begin
    if (reset) begin
        magnitude <= 32'd0;
        done      <= 1'b0;
        req       <= 1'b0;
        ack_meta  <= 1'b0;
        ack_sync  <= 1'b0; end
    else begin 
        ack_meta <= ack;
        ack_sync <= ack_meta; // Alligning clkB
               
        if (wr_enable && ready) begin
            magnitude <= d_in;
            done      <= done0_in;
            req       <= 1'b1;
        end
        // Acknowledgement
        else if (req && ack_sync) begin
            req <= 1'b0;
        end 
end
end
// --------------------------
// clkB Domain - Transmitting
// --------------------------
always@(posedge clkB) begin
    if (reset) begin
        d_out     <= 32'd0;
        done0     <= 1'b0;
        ack       <= 1'b0;
        req_meta  <= 1'b0;
        req_sync  <= 1'b0; end
    else begin 
        req_meta <= req;
        req_sync <= req_meta; // Alligning clkB
               
        if (req_sync && !ack) begin
            d_out     <= magnitude;
            done0     <= done;
            ack       <= 1'b1;
        end
        // Acknowledgement
        else if (ack && !req_sync) begin
            ack <= 1'b0;
        end 
end
end
endmodule

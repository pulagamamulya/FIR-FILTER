# FIR-FILTER
odule fir #(

    
    parameter N = 4  // Number of taps
)(
    input wire clk,
    input wire reset,
    input wire signed [7:0] x_in,
    output reg signed [15:0] y_out
);

    // Coefficient ROM (can be updated for new filters)
    wire signed [7:0] coeffs [0:N-1];
    assign coeffs[0] = 8'd3;
    assign coeffs[1] = 8'd5;
    assign coeffs[2] = 8'd5;
    assign coeffs[3] = 8'd3;

    // Input delay line
    reg signed [7:0] x_reg [0:N-1];

    integer i;
    reg signed [15:0] acc;

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            for (i = 0; i < N; i = i + 1)
                x_reg[i] <= 0;
            y_out <= 0;
        end else begin
            // Shift register
            for (i = N-1; i > 0; i = i - 1)
                x_reg[i] <= x_reg[i-1];
            x_reg[0] <= x_in;

            // MAC operation
            acc = 0;
            for (i = 0; i < N; i = i + 1)
                acc = acc + x_reg[i] * coeffs[i];
            y_out <= acc;
        end
    end

endmodule

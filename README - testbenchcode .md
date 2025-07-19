```verilog
`timescale 1ps/1ps
module tbRCNB;
  parameter N = 4;
  reg [N-1:0] a, b;
  reg cin;
  wire [N-1:0] s;
  wire cout;
  reg [N:0] exp_s;
  reg exp_c;
  integer err = 0;
  integer j, k, l;

// setup RippleCarryNbit testbench
  RippleCarryNbit #(N) DUT(a, b, cin, s, cout);

  initial begin
    $monitor("time=%0t, a=%b, b=%b, cin=%b, s=%b, cout=%b | exp_s=%b, exp_c=%b",
              $time, a, b, cin, s, cout, exp_s[N-1:0], exp_s[N]);

    for (l = 0; l < 2**N; l = l + 1) begin
      for (j = 0; j < 2**N; j = j + 1) begin
        for (k = 0; k < 2; k = k + 1) begin
          a = l[N-1:0];
          b = j[N-1:0];
          cin = k;
           exp_s = a + b + cin;
             exp_c = exp_s[N];
          #1 check(s, cout, exp_s[N-1:0], exp_c);
        end
      end
    end

    if (err == 0) begin
      $display("---------------");
      $display("Test Pass");
      $display("---------------");
    end else begin
      $display("---------------");
      $display("Test Failed with %d error(s)", err);
      $display("---------------");
    end
    #2 $finish;
  end

//Checker to check all transit
  task check;
    begin
      if (s !== exp_s[N-1:0] || cout !== exp_c) begin
        $display("[CHECK]: ERROR");
        err = err + 1;
      end else begin
        $display("[CHECK]: MATCHING");
      end
    end
  endtask
endmodule

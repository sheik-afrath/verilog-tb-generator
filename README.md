# Verilog Testbench Generator

This is a simple Perl script that automatically generates a basic Verilog testbench from a given Verilog design file. It parses the module's ports to create a test harness.

## Features

* Parses a Verilog file to find the module name, inputs, and outputs.
* Creates a new `<module_name>_tb.v` file.
* Declares all inputs as `reg` and outputs as `wire` in the testbench.
* Correctly handles both single-bit ports and vector ports (e.g., `[7:0]`).
* Instantiates the design, connecting the testbench `reg`s and `wire`s.
* **Sequential Logic Detection:**
    * Auto-detects ports named `clk` or `clock` and generates a clock signal.
    * Auto-detects `rst`/`reset` (active-high) or `rst_n`/`reset_n` (active-low) and generates an initial reset sequence.
* **Stimulus:** Generates an `initial` block with 10 random test vectors applied at 10ns intervals.

## How to Use

1.  Make sure you have Perl installed.
2.  Run the script from your terminal:

    ```bash
    perl <your_script_name>.pl <your_design_file>.v
    ```

3.  **Example:**
    If your script is named `gen_tb.pl` and your design is `andgate.v`:

    ```bash
    perl gen_tb.pl andgate.v
    ```

    This will create a new file named `andgate_tb.v` in the same directory.

## Supported Port Syntax

* This script relies on regular expressions to parse the Verilog file. It will work correctly with any design that declares one port per line.
* This includes both non-ANSI and ANSI styles.

✅ **Supported Example 1 (Non-ANSI Style):**

```verilog
module my_design (
    clk,
    rst_n,
    data_in,
    result_out
);

    input clk;
    input rst_n;
    input [7:0] data_in;
    output reg [3:0] result_out;

    // ... design logic ...
endmodule
```

✅ **Supported Example 2 (ANSI Style)**

```verilog
module my_design (
    input clk,
    input rst_n,
    input [7:0] data_in,
    output reg [3:0] result_out
);
   // ... design logic ...
endmodule
```

## Limitations & Unsupported Syntax
* This script will FAIL or produce incorrect code if your design file uses:

   * ❌ Multiple Ports Per Line: (e.g., `input a, b;` or `input clk, input rst_n,`)

   * ❌ inout Ports: These are not recognized.

   * ❌ Non-Descending Vectors: It only supports `[MSB:0]` style (e.g., `[7:0])`. It will not correctly parse `[0:7]`.

   * ❌ Parameterized Vectors: (e.g., `input [WIDTH-1:0] data;`)

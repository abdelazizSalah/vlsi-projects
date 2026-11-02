# VLSI Arithmetic Design Projects

**Verilog HDL | RTL Design | Digital Arithmetic | Functional Simulation | ASIC Synthesis | Timing & Power Analysis**

A collection of two VLSI mini-projects covering the design and evaluation of arithmetic hardware in Verilog. The projects explore different architectures for 32-bit addition and multiplication, with an emphasis on how implementation choices affect circuit area, propagation delay, and power consumption.

The repository includes RTL modules, directed testbenches, synthesis outputs, timing constraints, and implementation reports.

## Projects

| Project | Description | Source code |
| --- | --- | --- |
| **Mini Project 1 — Adders** | Eight 32-bit adder architectures, floating-point addition, and an area/timing/power comparison. | [View project](mini_project_1/) |
| **Mini Project 2 — Multipliers** | Signed and unsigned integer multipliers, Booth and Wallace-tree architectures, and a 32-bit floating-point multiplier. | [View project](mini_project_2/) |

## Mini Project 1 — 32-bit Adders and Floating-Point Addition

The first project compares different ways of implementing 32-bit addition. Each architecture uses a different approach to carry generation or propagation, making it possible to evaluate the speed-versus-area trade-offs at the hardware level.

### Adder implementations

| Architecture | RTL | Approach |
| --- | --- | --- |
| Behavioral (`+`) | [`PlusAdder.v`](mini_project_1/rtl/PlusAdder.v) | Reference implementation using the Verilog addition operator. |
| Ripple Carry | [`CarryRippleAdder.v`](mini_project_1/rtl/CarryRippleAdder.v) | Propagates the carry through a chain of full adders. |
| Carry Look-Ahead | [`carry_lookahead_adder.v`](mini_project_1/rtl/carry_lookahead_adder.v) | Computes carry signals using generate/propagate logic. |
| Carry Select | [`select_adder.v`](mini_project_1/rtl/select_adder.v) | Evaluates alternative carry-in outcomes and selects the corresponding result. |
| Carry Skip | [`carry_skip_adder.v`](mini_project_1/rtl/carry_skip_adder.v) | Uses block propagation logic to bypass selected carry stages. |
| Carry Bypass | [`bypass_adder.v`](mini_project_1/rtl/bypass_adder.v) | Implements a carry-bypass datapath. |
| Carry Increment | [`carryIncrementAdder.v`](mini_project_1/rtl/carryIncrementAdder.v) | Combines partial sums with carry-dependent increment logic. |
| Carry Save (project implementation) | [`carrySaveAdder.v`](mini_project_1/rtl/carrySaveAdder.v) | Implements the sum/carry structure used in the course design. |

Additional building blocks are available in [`fullAdder.v`](mini_project_1/rtl/fullAdder.v), [`halfAdder.v`](mini_project_1/rtl/halfAdder.v), and the other files in [`mini_project_1/rtl/`](mini_project_1/rtl/).

### Simulation and implementation

The [`AdderTb.v`](mini_project_1/rtl/AdderTb.v) testbench applies **10 directed test cases to each of the eight adder implementations**. The vectors cover positive and negative operands, zero, arithmetic boundaries, and overflow-related conditions, with output comparisons against expected results.

The project also contains:

- **Synthesis and implementation results:** architecture-specific folders with synthesized netlists (`verilog/`), timing/power/area reports (`rpt/`), and OpenDB design files (`odb/`).
- **Timing constraints:** [`demo_adder_func.sdc`](mini_project_1/constraints/demo_adder_func.sdc).
- **Technology libraries:** Nangate standard-cell and supporting technology files in [`lib_data/`](mini_project_1/lib_data/).
- **Results comparison:** [`Report.xlsx`](mini_project_1/Report.xlsx).

### Area, delay, and power comparison

The table below contains the measurements recorded in [`Report.xlsx`](mini_project_1/Report.xlsx). Area and power are shown as recorded in the spreadsheet; the maximum-delay values are in picoseconds.

| Adder | Area (reported) | Max. delay (ps) | Total power (reported) |
| --- | ---: | ---: | ---: |
| Behavioral `+` | **135** | 2,655.5 | **20.279** |
| Ripple Carry | 774 | 3,257.4 | 40.788 |
| Carry Save | 843 | 3,333.6 | 48.695 |
| Carry Look-Ahead | 593 | 2,248.7 | 50.331 |
| Carry Increment | 993 | 2,588.6 | 44.737 |
| Carry Skip | 1,095 | 3,704.8 | 55.533 |
| Carry Bypass | 843 | 3,140.2 | 55.029 |
| Carry Select | 1,111 | **1,954.3** | 75.538 |

The **carry-select adder** has the shortest delay in this comparison, while the behavioral `+` implementation has the lowest recorded area and power. The results illustrate the trade-off between reducing carry propagation time and increasing logic complexity.

### Floating-point adder

The [`floatingadder/`](mini_project_1/rtl/floatingadder/) directory contains a component-based 32-bit floating-point adder. Its RTL separates exponent processing, mantissa operations, normalization, sign handling, and output selection. The directory includes [`floating_unit.v`](mini_project_1/rtl/floatingadder/floating_unit.v), [`adder_floating.v`](mini_project_1/rtl/floatingadder/adder_floating.v), and floating-point testbenches.

---

## Mini Project 2 — Integer and Floating-Point Multipliers

The second project covers several approaches to integer multiplication and compares the underlying architectural ideas: iterative multiplication, Booth recoding, partial-product generation, and tree-based reduction.

### Multiplier implementations

| Architecture | RTL | Approach |
| --- | --- | --- |
| Behavioral Multiplier | [`star.v`](mini_project_2/rtl/star.v) | Uses Verilog's `*` operator with registered inputs and output. |
| Sequential Multiplier | [`sequential.v`](mini_project_2/rtl/sequential.v) | Shift-and-accumulate multiplication controlled by sequential logic. |
| Booth Multiplier | [`boothMult.v`](mini_project_2/rtl/boothMult.v) | Uses Booth recoding for signed multiplication. |
| Radix-4 Booth Multiplier | [`radix4BoothMult.v`](mini_project_2/rtl/radix4BoothMult.v) | Reduces partial-product generation using radix-4 Booth recoding. |
| Unsigned Wallace Tree | [`unsigned_wallace.v`](mini_project_2/rtl/unsigned_wallace.v) | Reduces partial products using a combinational tree structure. |
| Signed Wallace Tree | [`signed_wallace.v`](mini_project_2/rtl/signed_wallace.v) | Signed multiplication using a tree-based reduction architecture. |
| Floating-Point Multiplier | [`floating.v`](mini_project_2/rtl/floating.v) | Processes sign, exponent, and significand for 32-bit floating-point multiplication. |

### Testbenches and results

- [`testbench.v`](mini_project_2/rtl/testbench.v) contains directed signed-integer multiplication tests. Its current active instance is the **behavioral multiplier**; alternative multiplier instances can be selected in the testbench.
- [`testbench_wallace.v`](mini_project_2/rtl/testbench_wallace.v) tests the signed Wallace-tree implementation.
- [`testbench_floating.v`](mini_project_2/rtl/testbench_floating.v) provides **27 directed floating-point test vectors**, including zero, normal and subnormal values, infinity, and NaN cases.
- [`tbScript.tcl`](mini_project_2/rtl/tbScript.tcl) and [`test_floating.tcl`](mini_project_2/rtl/test_floating.tcl) contain simulator commands and waveform setup.
- [`synthesis/`](mini_project_2/rtl/synthesis/) contains synthesized netlists, while [`route/sequential.route.v`](mini_project_2/rtl/route/sequential.route.v) is an implementation-stage Verilog output.

The original [project specification](mini_project_2/Mini%20Project%232.pdf) is included for reference.

---

## Repository Structure

```text
vlsi-projects/
├── mini_project_1/
│   ├── rtl/                 # Adder RTL, testbenches, floating-point design
│   ├── constraints/         # Timing constraints
│   ├── lib_data/            # Standard-cell / technology libraries
│   ├── Report.xlsx          # Adder PPA comparison
│   └── <adder_architecture>/
│       ├── rpt/             # Area, timing, and power reports
│       ├── verilog/         # Synthesized netlists
│       ├── odb/             # Physical design databases
│       └── logs/
├── mini_project_2/
│   ├── Mini Project#2.pdf   # Project specification
│   └── rtl/
│       ├── *.v              # Multiplier RTL and testbenches
│       ├── *.tcl            # Simulation commands
│       ├── synthesis/       # Gate-level netlists
│       └── route/           # Implementation output
├── README.md
└── LICENSE
```

## Running a Simulation

The Verilog designs can be explored with **ModelSim/Questa** or another simulator supporting the SystemVerilog constructs used in the testbenches.

For example, to run the behavioral multiplier testbench in ModelSim/Questa:

```bash
cd mini_project_2/rtl
vlib work
vlog -sv star.v testbench.v
vsim -c work.multTb -do "run -all; quit -f"
```

The multiplier testbench instantiates `star` by default. To test a different architecture, update the unit under test and compile its corresponding RTL files. For the adder designs, select the relevant modules and dependencies; some implementations share helper-module names.

## Tools and Topics

**Languages:** Verilog HDL, SystemVerilog testbench constructs  
**Simulation:** ModelSim/Questa-style testbenches and Tcl commands  
**Hardware design:** Adders, multipliers, carry logic, Booth recoding, Wallace trees, floating-point arithmetic  
**ASIC flow:** Synthesis, SDC constraints, standard-cell libraries, static timing, area/power reports, physical-design artifacts

## License

This repository is distributed under the [MIT License](LICENSE). Copyright information and attribution are retained in the license file.

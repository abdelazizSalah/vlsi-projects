# VLSI Design Projects — RTL Arithmetic & ASIC Implementation

**Verilog HDL · Digital arithmetic · RTL simulation · Logic synthesis · Static timing · Area & power analysis**

A collection of academic VLSI design projects focused on implementing arithmetic datapaths in RTL and exploring their hardware implementation trade-offs. The repository contains **two mini-projects**: a study of 32-bit adder architectures and floating-point addition, followed by signed-integer multiplier architectures and floating-point multiplication.

Beyond the RTL, the repository preserves simulation testbenches, synthesized gate-level netlists, implementation reports, timing constraints, physical-design databases, and standard-cell library data. Together, these files illustrate the progression from **architecture and RTL → simulation → synthesis → implementation and PPA analysis**.

> **Scope:** This repository documents RTL design and simulation-oriented verification. Formal property verification, SystemVerilog Assertions (SVA), equivalence checking, and formal sign-off are **not demonstrated** by the files currently included. The presence of archived results should not be interpreted as independently reproduced validation.

## Projects at a glance

| Project | Focus | Principal implementations | Evidence in repository |
| --- | --- | --- | --- |
| [Mini Project 1 — Adders & Floating-Point Addition](mini_project_1/) | Compare arithmetic architectures and their implementation costs | Eight 32-bit adder variants and a 32-bit floating-point adder | RTL, functional testbenches, spreadsheet comparison, constraints, synthesized netlists, timing/area/power reports, and OpenDB files |
| [Mini Project 2 — Multipliers](mini_project_2/) | Explore integer multiplication architectures and floating-point multiplication | Behavioral (`*`), sequential shift-and-accumulate, Booth, radix-4 Booth, signed/unsigned Wallace-tree, and floating-point implementations | RTL, testbenches, ModelSim/Questa Tcl helpers, synthesized netlists, a routed Verilog file, and the original assignment brief |

## 1. Mini Project 1 — 32-bit Adders & Floating-Point Addition

**[Browse project](mini_project_1/)** · **[RTL](mini_project_1/rtl/)** · **[Comparison spreadsheet](mini_project_1/Report.xlsx)**

This project explores the trade-offs between different approaches to integer addition. Each architecture is represented by RTL, with corresponding implementation output available for the primary variants.

### Adder architectures

| Implementation | RTL source | Design idea |
| --- | --- | --- |
| Behavioral adder | [`PlusAdder.v`](mini_project_1/rtl/PlusAdder.v) | Uses Verilog's `+` operator as a baseline. |
| Ripple-carry adder | [`CarryRippleAdder.v`](mini_project_1/rtl/CarryRippleAdder.v) | Chains full adders, propagating carry bit by bit. |
| Carry look-ahead-labelled adder | [`carry_lookahead_adder.v`](mini_project_1/rtl/carry_lookahead_adder.v) | Uses explicit generate/propagate carry equations. |
| Carry-select adder | [`select_adder.v`](mini_project_1/rtl/select_adder.v) | Computes alternate carry-in results and selects an output. |
| Carry-skip adder | [`carry_skip_adder.v`](mini_project_1/rtl/carry_skip_adder.v) | Uses 4-bit blocks and carry-propagation logic. |
| Carry-bypass adder | [`bypass_adder.v`](mini_project_1/rtl/bypass_adder.v) | Separate bypass-style implementation. |
| Carry-increment adder | [`carryIncrementAdder.v`](mini_project_1/rtl/carryIncrementAdder.v) | Combines partial addition with increment/carry propagation. |
| Carry-save-labelled adder | [`carrySaveAdder.v`](mini_project_1/rtl/carrySaveAdder.v) | Uses separate internal sum/carry signals; see RTL for its exact two-input structure. |

Shared building blocks include [`fullAdder.v`](mini_project_1/rtl/fullAdder.v), [`halfAdder.v`](mini_project_1/rtl/halfAdder.v), and a ripple-adder helper. Some module names are shared between files or pulled in through `` `include `` statements: compile the required files for a chosen design rather than indiscriminately compiling every `.v` file together.

### 32-bit floating-point addition

The [`rtl/floatingadder/`](mini_project_1/rtl/floatingadder/) directory contains a component-based floating-point adder, including sign handling, exponent processing, mantissa operations, normalization and supporting selection logic. Key source files include [`floating_unit.v`](mini_project_1/rtl/floatingadder/floating_unit.v), [`adder_floating.v`](mini_project_1/rtl/floatingadder/adder_floating.v), and related submodules.

The directory contains multiple versions and testbench artifacts, including [`floatingTb.v`](mini_project_1/rtl/floatingadder/floatingTb.v) and [`floatingTbCopy.v`](mini_project_1/rtl/floatingadder/floatingTbCopy.v). They should be treated as historical development artifacts; the repository does not include a single canonical build script for the floating-point adder.

### Verification and implementation artifacts

- [`AdderTb.v`](mini_project_1/rtl/AdderTb.v) contains **10 directed input cases**, exercised across the eight integer adders, including signed values, zero and boundary conditions. This is simulation-based checking, not a formal proof.
- [`Report.xlsx`](mini_project_1/Report.xlsx) records area, maximum delay, slack and power values for the adder implementations.
- [`constraints/demo_adder_func.sdc`](mini_project_1/constraints/demo_adder_func.sdc) describes timing and I/O constraints; the checked-in file sets a **20 ns clock period**.
- The architecture-specific directories (for example, [`CarryRippleAdder/`](mini_project_1/CarryRippleAdder/) and [`select_adder/`](mini_project_1/select_adder/)) contain `verilog/` synthesized netlists, `rpt/` reports, `odb/` OpenDB files and `logs/` output.
- [`lib_data/`](mini_project_1/lib_data/) includes Nangate standard-cell and FreePDK-related technology/library data.

### Recorded adder comparison

The following values are transcribed from [`Report.xlsx`](mini_project_1/Report.xlsx), which is part of the repository. Delay values are consistent with the picosecond timing reports; **area and total-power units are not explicitly specified in the spreadsheet**, so those columns are retained as *reported values* rather than assigned units.

| Adder | Total area (reported) | Maximum delay (ps) | Total power (reported) |
| --- | ---: | ---: | ---: |
| Behavioral `+` | 135 | 2655.5 | 20.279 |
| Ripple carry | 774 | 3257.4 | 40.788 |
| Carry save (labelled) | 843 | 3333.6 | 48.695 |
| Carry look-ahead (labelled) | 593 | 2248.7 | 50.331 |
| Carry increment | 993 | 2588.6 | 44.737 |
| Carry skip | 1095 | 3704.8 | 55.533 |
| Carry bypass | 843 | 3140.2 | 55.029 |
| Carry select | 1111 | 1954.3 | 75.538 |

**Observation from the archived spreadsheet:** the behavioral `+` design has the lowest reported area and power, while the carry-select implementation has the lowest reported maximum delay. These are **archived course results**, not new measurements or proof that the implementations are functionally equivalent across all inputs. For an authoritative comparison, confirm the flow stage, units, cell library, constraints and functional correctness of each design.

## 2. Mini Project 2 — Integer & Floating-Point Multipliers

**[Browse project](mini_project_2/)** · **[RTL](mini_project_2/rtl/)** · **[Project specification (PDF)](mini_project_2/Mini%20Project%232.pdf)**

The included *Mini Project #2 — Multipliers Mania* assignment describes a comparison of **32-bit signed integer multiplier** architectures, covering simulation, synthesis, physical implementation, timing/power/area reporting and an IEEE-style 32-bit floating-point multiplier extension. The files below provide source-code evidence for the implementations; not every deliverable requested in the assignment is visibly present as a standalone file.

### Architectures in the repository

| Implementation | RTL source | Design idea |
| --- | --- | --- |
| Behavioral multiplier | [`star.v`](mini_project_2/rtl/star.v) | Registered operands and result, using Verilog `*` for multiplication. |
| Sequential multiplier | [`sequential.v`](mini_project_2/rtl/sequential.v) | Shift-and-accumulate datapath with controller/state logic. |
| Booth multiplier | [`boothMult.v`](mini_project_2/rtl/boothMult.v) | Booth-style recoding and signed multiplication logic. |
| Radix-4 Booth multiplier | [`radix4BoothMult.v`](mini_project_2/rtl/radix4BoothMult.v) | Radix-4 recoding using groups of multiplier bits. |
| Unsigned Wallace tree | [`unsigned_wallace.v`](mini_project_2/rtl/unsigned_wallace.v) | Large combinational partial-product reduction structure. |
| Signed Wallace tree | [`signed_wallace.v`](mini_project_2/rtl/signed_wallace.v) | Signed multiplication tree with a registered wrapper. |
| Floating-point multiplier | [`floating.v`](mini_project_2/rtl/floating.v) | 32-bit floating-point multiplication with exponent, significand and special-case handling. |

### Testbenches and implementation outputs

- [`testbench.v`](mini_project_2/rtl/testbench.v): directed signed-integer multiplication cases. **`star` is the active unit under test** in the checked-in version; other multiplier instances are commented out.
- [`testbench_wallace.v`](mini_project_2/rtl/testbench_wallace.v): directed tests for the registered signed Wallace-tree implementation.
- [`testbench_floating.v`](mini_project_2/rtl/testbench_floating.v): **27 directed floating-point test vectors**, including normal values, subnormals, zeros, infinities, NaNs and boundary cases.
- [`tbScript.tcl`](mini_project_2/rtl/tbScript.tcl) and [`test_floating.tcl`](mini_project_2/rtl/test_floating.tcl): simulator command examples / waveform setup.
- [`synthesis/`](mini_project_2/rtl/synthesis/): archived synthesized netlists for several multipliers; [`route/sequential.route.v`](mini_project_2/rtl/route/sequential.route.v) is a routed Verilog artifact.
- [`deliverables/backup.rar`](mini_project_2/rtl/deliverables/backup.rar): additional archived deliverables. The archive was **not unpacked** for this README, so its contents have not been assessed.

**Note:** The assignment PDF specifies broader sign-off deliverables (including post-route simulation and GDS). This README does **not** claim that all those files or results were independently verified in the accessible source tree.

## Repository layout

```text
vlsi-projects/
├── README.md
├── LICENSE
├── mini_project_1/
│   ├── rtl/
│   │   ├── AdderTb.v
│   │   ├── PlusAdder.v
│   │   ├── CarryRippleAdder.v
│   │   ├── carry_lookahead_adder.v
│   │   ├── carry_skip_adder.v
│   │   ├── bypass_adder.v
│   │   ├── carryIncrementAdder.v
│   │   ├── carrySaveAdder.v
│   │   ├── select_adder.v
│   │   ├── floatingadder/
│   │   └── synthesis/
│   ├── constraints/
│   ├── lib_data/
│   ├── Report.xlsx
│   └── <architecture>/
│       ├── logs/
│       ├── odb/
│       ├── rpt/
│       └── verilog/
└── mini_project_2/
    ├── Mini Project#2.pdf
    └── rtl/
        ├── star.v
        ├── sequential.v
        ├── boothMult.v
        ├── radix4BoothMult.v
        ├── signed_wallace.v
        ├── unsigned_wallace.v
        ├── floating.v
        ├── testbench*.v
        ├── *.tcl
        ├── synthesis/
        ├── route/
        └── deliverables/
```

The tree is abbreviated to emphasize key source and result locations. It does not list every generated file, backup or simulation work database.

## How to explore / simulate

1. Choose a particular architecture and read its top-level module and dependencies.
2. Use a simulator capable of **SystemVerilog**: although many source files use `.v` extensions, the directed testbenches include SystemVerilog constructs such as array assignment patterns.
3. Compile only the files for that architecture into a fresh simulator library. Several alternatives define overlapping helper module names; attempting to compile every RTL file together can cause conflicts.
4. Run the corresponding testbench and inspect its pass/fail messages and waveforms. Do not assume tests pass without running them on your environment.
5. For the first mini-project, compare the archived spreadsheet with the architecture-specific `rpt/` and `odb/` outputs. For the second, see the archived `synthesis/` and `route/` artifacts.

**Example: behavioral multiplier in ModelSim/Questa (illustrative; not executed during this review)**

```bash
cd mini_project_2/rtl
vlib work
vlog -sv star.v testbench.v
vsim -c work.multTb -do "run -all; quit -f"
```

The active testbench design may need adjustment if you want to simulate a different multiplier implementation. Dedicated scripts for reproducing the entire synthesis/place-and-route flow are **not present as a complete turnkey pipeline** in the checked-in tree.

## Technical areas demonstrated

- **RTL design:** 32-bit arithmetic datapaths in Verilog, modular design, sequential control and register interfaces.
- **Digital architecture:** adder carry strategies; Booth recoding; iterative and tree-based multiplication; floating-point data handling.
- **Simulation-based checking:** directed test vectors and expected-result comparisons using ModelSim/Questa-style testbenches.
- **ASIC implementation analysis:** exposure to synthesized netlists, physical database artifacts, SDC constraints, cell libraries and archived power/area/timing reports.
- **Engineering trade-offs:** comparison of logic resources, delay and power across arithmetic implementations.

## Reproducibility and limitations

This is an archived educational repository rather than a packaged CI-tested IP library. The included testbenches and reports are useful evidence of the original work, but they were **not rerun or independently validated** as part of this documentation update. Some directories contain intermediate or duplicate versions of modules, generated netlists, backups and simulator output. For a production-ready verification portfolio, recommended follow-ups include a clean per-design build, reproducible simulation commands, assertion-based checks, coverage reporting and a consistent PPA flow.

## License and attribution

See [`LICENSE`](LICENSE) for the repository's MIT license and existing copyright notice (**Mohamed Kamal, 2022**). The repository includes an academic course specification from Cairo University's Computer Engineering Department. Individual authorship and team contributions are not specified by the source tree; retain existing attribution when sharing or reusing these files.

## Architecture & Top-Level Design

The vsdbabysoc top-level module binds together the digital processor core and analog macros:
1.rvmyth: RISC-V-based CPU core that executes instructions and outputs digital data.
2.avsdpll: Phase-Locked Loop macro generating the system clock (CLK) from reference clock inputs.
3.avsddac: 10-bit DAC macro that converts the digital register output (RV_TO_DAC[9:0]) to an analog output signal (OUT).

## Pre-Synthesis RTL Simulation

Functional verification was conducted using Icarus Verilog and visualized in GTKWave (vsdbabysoc_tb).
1.CLK: Continuous master system clock (0 to 85 µs).
2.RV_TO_DAC[9:0]: Demonstrates a continuous triangular stair-step digital pattern, confirming correct execution of instructions before synthesis.

## Logic Synthesis & Technology Mapping

Synthesis was performed using Yosys and mapped to the SkyWater 130nm (sky130_fd_sc_hd) library via ABC.
### Design Hierarchy & Primitive Cell Statistics
 1.Instances: vsdbabysoc (1), rvmyth (1), clk_gate (7), avsdpll (1), avsddac (1).
 2.Total Primitive Cells: 5,160 cells (including 1,412 $___ANDNOT___, 1,322 $___OR___, 962 $___SDFFE_PP0P___, and 513 $___MUX___).
 3.Wires & Ports: 3,992 wires (6,688 wire bits) and 45 ports.

### Gate-Level Standard Cell Mapping
 1.Mapped Netlist: Mapped to 4,774 internal signals, 1,225 input signals, and 1,173 output signals.
 2.Key Mapped Standard Cells: 1,272 nand2_1, 754 a21loi_1, 646 nor2_1, 435 a21lloi_1, and 47 clkinv_1.

### Post-Synthesis Gate-Level Verification
Post-synthesis gate-level simulation (post_synth_sim.vcd) verifies that the synthesized netlist retains the expected functional behavior.


##  Static Timing Analysis (STA)

Timing verification was executed with OpenSTA using run.tcl across multiple Process, Voltage, and Temperature (PVT) corners

## 5. Physical Design Flow (OpenROAD)
The physical design implementation follows an automated OpenROAD script flow:
###Floorplanning & Placement
  1. Floorplan (2_floorplan): Defines core boundaries, power ring/stripe configurations, and positions the analog dac macro block at the bottom left of the die.
  2.Placement (3_place): Distributes standard cells above the macro block with placement density heatmaps monitored for congestion.

## Clock Tree Synthesis & Routing


   1.CTS (4_cts): Synthesizes balanced clock trees across sequential logic with minimal setup skew (0.00 ns).
   2.Routing (5_route): Executes detailed global/interconnect routing across met1 through met5 metal layers and extracts parasitic parameters (estimate_parasitics -global_routing).

## Post-Route Analysis & Final Metrics

###Post-Route Timing & Slack Overview
  1.Worst Setup Slack (WNS): -1.762 ns
  2.Worst Hold Slack: +0.479 ns (MET)
  3.Total Negative Slack (TNS): -69.502 ns
###Detailed Path Analysis
   1.Core Clock Setup Path (core_clock): Critical path between datapath registers dpath.b_reg.out[0] and dpath.a_reg.out[9] exhibits arrival time of 2.55 ns vs required 1.10 ns (Slack: -1.45 ns).
   2.Virtual Clock Setup Path (vclk_core_clock): Output path from dpath.b_reg.out[0] to port resp_msg[3] exhibits arrival time of 2.64 ns vs required 0.88 ns (Slack: -1.76 ns).
   3.Core Clock Hold Path (core_clock): Internal state path ctrl.state.out[1] to ctrl.state.out[1] achieves positive slack of +0.48 ns (MET).
   4.Virtual Clock Hold Path (vclk_core_clock): Output boundary path ctrl.state.out[1] to resp_val achieves positive slack of +1.06 ns (MET).

## Power, Skew & Area Metrics
###Total Power Consumption: 7.34 mW

   1.Combinational Power: 4.87 mW (66.4%)
   2.Sequential Power: 1.75 mW (23.9%)
   3.Clock Network Power: 0.715 mW (9.8%)
   4.Power Type Distribution: Internal Power: 76.1%, Switching Power: 23.9%, Leakage Power: 0.0%

###Design Area & Utilization:
   1.Total Area: 5029 µm²
   2.Core Utilization: 84%
   3.Setup Clock Skew: 0.00 ns


### Install the following open-source EDA tools:
   1.Icarus Verilog (iverilog)
   2.GTKWave
   3.Yosys
   4.OpenSTA
   5.OpenROAD Flow Scripts


### Run Simulation:
# Clone the repository
git clone [https://github.com/your-username/VSDBabySoC.git](https://github.com/your-username/VSDBabySoC.git)
cd VSDBabySoC

# Compile and simulate RTL
iverilog -o sim/sim_output.out rtl/*.v sim/vsdbabysoc_tb.v
./sim/sim_output.out
gtkwave sim/wave.vcd


## Run STA:

cd OpenSTA
opensta run.tcl














   


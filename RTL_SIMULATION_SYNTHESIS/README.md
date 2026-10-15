##  Functional Verification & Waveform Analysis (Pre-Synthesis)

<img width="1902" height="877" alt="pre_synth" src="https://github.com/user-attachments/assets/fbf4952e-d675-4ba0-ba5f-846f48a8254f" />


### Pre-Synthesis Functional Simulation

This section shows the RTL pre-synthesis simulation waveform generated using GTKWave for the `vsdbabysoc_tb` testbench.

#### Key Details:
* **Testbench:** `vsdbabysoc_tb`
* **Clock & Signal Verification:**
  * **`CLK`**: Master system clock running continuously throughout the simulation duration (0 to 85 µs).
  * **`RV_TO_DAC[9:0]`**: A 10-bit digital bus input to the Digital-to-Analog Converter (DAC).
  * **Waveform Behavior:** The multi-bit signal `RV_TO_DAC[9:0]` exhibits a clear stair-step / triangular digital output pattern across the 10 bits (`RV_TO_DAC[9]` down to `RV_TO_DAC[0]`), confirming proper execution of the core logic before logic synthesis.
 



## Post-Synthesis Technology Mapping (sky130_fd_sc_hd)

<img width="416" height="780" alt="baby_soc_cells" src="https://github.com/user-attachments/assets/feda5e05-2cfa-4cb0-aa3a-fed36539ccda" />

### Logic Synthesis & Technology Mapping

Following RTL elaboration, the design is synthesized and mapped to the **SkyWater 130nm High-Density (`sky130_fd_sc_hd`)** standard cell library using ABC[cite: 2].

#### Synthesis Statistics (`10.2.2. Re-integrating ABC results`)[cite: 2]
* **Target Library:** `sky130_fd_sc_hd`[cite: 2]
* **Signal Statistics:**
  * **Internal Signals:** 4,774[cite: 2]
  * **Input Signals:** 1,225[cite: 2]
  * **Output Signals:** 1,173[cite: 2]
* **Notable Cell Instances Mapped:**
  * `sky130_fd_sc_hd__nand2_1`: 1,272 cells[cite: 2]
  * `sky130_fd_sc_hd__a21loi_1`: 754 cells[cite: 2]
  * `sky130_fd_sc_hd__nor2_1`: 646 cells[cite: 2]
  * Various complex AOI/OAI logic gates, multiplexers, and flip-flops mapped efficiently by the synthesis engine
#### Synthesis Report Snapshot
![BabySoC Cell Synthesis Results](./screenshots/baby_soc_cells.png)


## Yosys Synthesis Netlist Breakdown (vsdbabysoc)

<img width="356" height="577" alt="baby_soc_details" src="https://github.com/user-attachments/assets/80553e4a-1dfa-4fd1-8e46-3135d116edfb" />

### Design Hierarchy & Synthesis Statistics

The pre-mapping synthesis report generated via Yosys provides a comprehensive overview of the design hierarchy, wire metrics, and primitive cell utilization for the `vsdbabysoc` architecture[cite: 3].

#### 1. Design Hierarchy
* **`vsdbabysoc` (Top-level SoC):** 1 instance[cite: 3]
* **`rvmyth` (RISC-V Core):** 1 instance[cite: 3]
* **`clk_gate`:** 7 instances[cite: 3]

#### 2. Netlist and Port Metrics
* **Total Wires:** 3,992 wires (6,688 wire bits)[cite: 3]
* **Public Wires:** 313 public wires (2,994 public wire bits)[cite: 3]
* **Ports:** 45 ports (54 port bits)[cite: 3]
* **Memories & Processes:** 0 memories, 0 processes[cite: 3]

#### 3. Primitive Cell Breakdown (Total: 5,160 Cells)[cite: 3]
* **Mixed-Signal Macros:** 
  * `avsddac` (DAC): 1 instance[cite: 3]
  * `avsdpll` (PLL): 1 instance[cite: 3]
* **Logic & Sequential Elements:**
  * `$___ANDNOT___`: 1,412[cite: 3]
  * `$___OR___`: 1,322[cite: 3]
  * `$___SDFFE_PP0P___`: 962[cite: 3]
  * `$___MUX___`: 513[cite: 3]
  * `$___DFF_P___`: 239[cite: 3]
  * `$___AND___`: 174[cite: 3]
  * `$___XOR___`: 129[cite: 3]
  * Other primitive gates (`NOR`, `NAND`, `XNOR`, `NOT`, etc.) make up the remainder of the synthesized netlist[cite: 3].

#### Synthesis Hierarchy Snapshot
![BabySoC Design Hierarchy and Details](./screenshots/baby_soc_details.png)


## SoC Architecture & Top-Level Block Diagram (vsdbabysoc)

<img width="1912" height="376" alt="baby_soc_syn" src="https://github.com/user-attachments/assets/e63cf871-6623-486a-811c-56eabd4dbe6b" />

### SoC Architecture & Data Flow

The top-level schematic for `vsdbabysoc` illustrates the integration of mixed-signal analog macro blocks with the digital RISC-V core.

#### Architecture Overview
* **Clock Generation (`avsdpll`)**: 
  * Controlled by input pins **`ENb_CP`**, **`ENb_VCO`**, **`REF`**, and **`VCO_IN`**[cite: 4].
  * Generates the internal system **`CLK`** supplied to the digital logic[cite: 4].
* **Processing Core (`rvmyth`)**: 
  * Receives the generated **`CLK`** and **`reset`** signals[cite: 4].
  * Executes instructions and outputs digital data via its **`OUT`** port[cite: 4].
* **Digital-to-Analog Conversion (`avsddac`)**: 
  * Takes the multi-bit digital bus (`RV_TO_DAC`) from the core alongside a reference voltage (**`VREFH`**)[cite: 4].
  * Converts the digital stream into a continuous analog **`OUT`** signal[cite: 4].

#### Top-Level Block Diagram
![BabySoC Top-Level Architecture Diagram](./screenshots/baby_soc_syn.png)


## Post-Synthesis Waveform Analysis (post_synth_sim.vcd)

<img width="1912" height="922" alt="post_synth" src="https://github.com/user-attachments/assets/be22f5e3-a256-40f4-8777-bf45a23fadae" />

### Post-Synthesis Gate-Level Simulation

This section shows the gate-level post-synthesis simulation waveform viewed via GTKWave (`post_synth_sim.vcd`) for the `vsdbabysoc_tb` testbench[cite: 5].

#### Key Details:
* **Waveform File:** `post_synth_sim.vcd`[cite: 5]
* **Signal Verification:**
  * **`CLK`**: Master system clock running continuously across the 0 to 85 µs simulation span[cite: 5].
  * **`RV_TO_DAC[9:0]`**: 10-bit digital bus input driving the DAC[cite: 5].
  * **Waveform Behavior:** The post-synthesis gate-level netlist correctly reproduces the expected triangular stair-step digital patterns across `RV_TO_DAC[9:0]`[cite: 5], confirming that logic synthesis preserves the correct functional behavior of the design.

## Static Timing Analysis (STA) Setup Script (run.tcl)
<img width="1137" height="458" alt="pre_STA_run_tcl" src="https://github.com/user-attachments/assets/af9cabf3-a5ee-487c-a29b-f9100d828fbf" />

### Static Timing Analysis (STA) Setup

The timing analysis for `vsdbabysoc` is executed using **OpenSTA** via a custom TCL script (`run.tcl`)[cite: 6]. This script configures the libraries, netlist, and constraints before running timing verification.

#### TCL Script Breakdown (`run.tcl`)[cite: 6]
* **Environment Setup:**
  * Defines the base path (`/data/OpenSTA/vsdbabysoc/`)[cite: 6].
  * Sets the standard cell library to `sky130_fd_sc_hd__tt_025C_1v80.lib` (Typical corner: 25°C, 1.8V)[cite: 6].
* **Library Loading (`read_liberty`):**
  * Loads minimum and maximum liberty files for the Sky130 standard cells[cite: 6].
  * Loads PLL analog macro library (`avsdpll.lib`)[cite: 6].
  * Loads DAC analog macro library (`avsddac.lib`)[cite: 6].
* **Design Link & Constraints (`read_verilog`, `read_sdc`):**
  * Reads the synthesized netlist (`vsdbabysoc.synth.v`) and links the design (`link_design vsdbabysoc`)[cite: 6].
  * Applies design constraints via the SDC file (`vsdbabysoc_synthesis.sdc`)[cite: 6].
* **Reporting (`report_checks`):**
  * Generates timing reports and check summaries to evaluate setup/hold violations[cite: 6].

## Multi-Corner Static Timing Analysis (STA) Execution

<img width="441" height="630" alt="STA_diff_corners" src="https://github.com/user-attachments/assets/36f8482f-4554-4747-bc36-8815020301f2" />

### Multi-Corner Static Timing Analysis (STA)

To ensure the robustness of `vsdbabysoc` under varying environmental and manufacturing conditions, Static Timing Analysis is executed across multiple Process, Voltage, and Temperature (PVT) corners using the Sky130 standard cell library[cite: 7].

#### Evaluated Corners & Execution Status (`STA_diff_corners.jpeg`)[cite: 7]
* **Fast-Fast (FF) Corners:**
  * `sky130_fd_sc_hd__ff_100C_1v65` — **SUCCESS**[cite: 7]
  * `sky130_fd_sc_hd__ff_100C_1v95` — **SUCCESS**[cite: 7]
  * `sky130_fd_sc_hd__ff_n40C_1v56` to `1v95` (covering -40°C across various supply voltages) — **SUCCESS**[cite: 7]
* **Slow-Slow (SS) Corners:**
  * `sky130_fd_sc_hd__ss_100C_1v40` (and subsequent slow corners) — **SUCCESS**[cite: 7]

## STA Completion Summary & Slack Timing Results
<img width="972" height="767" alt="STA_1" src="https://github.com/user-attachments/assets/8ffdee14-4c41-439f-ad99-74513f4e82ac" />

### STA Run Completion & Slack Analysis

Upon completing the multi-corner timing sweeps, the individual report outputs are stored as text files, and a `grep` command is used to parse the slack status across all evaluated corners[cite: 8].

#### Key Results & Summary (`STA_1.jpeg`)[cite: 8]
* **STA Run Status:** All corner runs (including Slow-Slow, Fast-Fast, and Typical-Typical corners from -40°C to 100°C) completed successfully[cite: 8].
* **Generated Reports Directory:** Results are compiled into individual text files (e.g., `sky130_fd_sc_hd__ff_100C_1v65.txt`, `sky130_fd_sc_hd__tt_025C_1v80.txt`, etc.)[cite: 8].
* **Slack Verification (`grep` results):** 
  * Filtering for the term `"slack"` across all text files confirms that every evaluated corner successfully met timing requirements (`slack (MET)`)[cite: 8].
  * Positive slack values indicate that the design achieves timing closure comfortably across all tested operating ranges[cite: 8].

## Comprehensive STA Slack Analysis & Slow-Corner Violations

<img width="1036" height="761" alt="STA_2" src="https://github.com/user-attachments/assets/ba61dadd-1e18-4abd-ac19-89da417a07a3" />

### Comprehensive STA Slack Analysis & Corner Violations

While typical and fast-fast corners successfully meet timing, running the complete set of scans across extreme Process, Voltage, and Temperature (PVT) variations uncovers specific setup/hold timing violations under harsh Slow-Slow (SS) conditions[cite: 9].

#### Key Observations from the Grep Slack Report (`STA_2.jpeg`)[cite: 9]:
* **Fast & Typical Corners (`ff` / `tt`):** 
  * All evaluated Fast-Fast and Typical-Typical corners maintain positive slack and successfully pass (`slack (MET)`)[cite: 9].
* **Slow-Slow Corners (`ss`):** 
  * While lower-severity slow corners pass, extreme operating points exhibit significant negative slack (timing violations).
  * **Notable Violations Logged:**
    * `ss_100C_1v40.txt`: `-17.12` slack (`slack (VIOLATED)`)[cite: 9]
    * `ss_100C_1v60.txt`: `-7.87` slack (`slack (VIOLATED)`)[cite: 9]
    * `ss_n40C_1v28.txt`: `-62.56` slack (`slack (VIOLATED)`)[cite: 9]
    * Additional slow corners (`1v35`, `1v40`, `1v44`, `1v60`, `1v76` at `-40C`) show corresponding setup violations ranging from `-4.38` to `-39.70`[cite: 9].
* **Design Takeaway:** These results highlight the critical need for supply voltage regulation and temperature management to prevent performance degradation under worst-case manufacturing and environmental conditions.

## Archiving & Packaging Multi-Corner STA Results

<img width="667" height="765" alt="STA_3" src="https://github.com/user-attachments/assets/28272230-81ab-4a74-aa4f-06c3b21afacf" />

### Archiving STA Results

To back up and easily share the extensive multi-corner timing analysis output files, the generated text reports are compressed into an archive file (`STA_results.zip`)[cite: 10].

#### Archive Details (`STA_3.jpeg`)[cite: 10]
* **Archived File Size:** 29 KB (`STA_results.zip`)[cite: 10]
* **Contents:** Contains all 17 multi-corner text report files (covering Slow-Slow, Fast-Fast, and Typical-Typical corners) with individual file sizes averaging ~9.5 KB to 9.8 KB[cite: 10].
* **Verification:** Checked using `unzip -l STA_results.zip` to confirm all corner report files are correctly packaged[cite: 10].

## Multi-Corner STA Summary & Timing Results Table

<img width="800" height="520" alt="Pre_STA_analysis" src="https://github.com/user-attachments/assets/9f459872-fa2f-47fa-b22b-e708b8debed9" />

### Multi-Corner Static Timing Analysis (STA) Summary

The table below summarizes the Static Timing Analysis results across various Process, Voltage, and Temperature (PVT) corners for `vsdbabysoc`[cite: 11].

#### Summary Table Highlights (`Pre_STA_analysis.jpeg`)[cite: 11]
* **Fast-Fast (FF) & Typical-Typical (TT) Corners:** 
  * All FF corners (entries 1–6) and TT corners (entries 15–16) successfully **MET** timing requirements, showing positive minimum and maximum slack values[cite: 11].
* **Slow-Slow (SS) Corners:** 
  * Extreme SS corners (entries 7–14) experience negative maximum slack (setup violations), ranging from `-4.38 ns` up to `-62.56 ns`, marked as **VIOLATED**[cite: 11].















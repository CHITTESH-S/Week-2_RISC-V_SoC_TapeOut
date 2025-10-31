# 🌟 RISC-V SoC Tapeout – Week-2: Research Week on Baby Soc fundamentals and functional modelling using Icarus Verilog & GTKWave.

> A concise, professional, and repository-ready rewrite. Same technical content as your original — clarified, reorganized, and formatted for readability. Each line uses a small icon for quick scanning.

---

## 🔎 Executive Summary

🎯 **Objective:** Validate the VSDBabySoC (PLL → RVMYTH → DAC) at the functional level using Icarus Verilog and GTKWave prior to synthesis.

🛠 **Tools:** Icarus Verilog · GTKWave · SandPiper‑SaaS · RISC‑V

⏱ **Duration:** 1 week · **Difficulty:** Intermediate

---

## Part 1 — Theory: SoC Fundamentals & BabySoC (Complete)

> A focused theory module covering System-on-Chip fundamentals, component roles, VSDBabySoC overview (PLL, RVMYTH, DAC), and the rationale for functional modelling. Use this as the canonical “Part-1” theory text.

## 🔷 Executive Goal

🎯 Purpose: Explain what an SoC is, why it matters, and the theoretical foundations behind VSDBabySoC.

📌 Scope: SoC concepts, core components, interconnects, BabySoC component theory (PLL, RISC-V core, DAC), mixed-signal considerations, and the role of functional modelling.

---

## 🧠 What is a System-on-Chip (SoC)?

**🧩 Definition:** An SoC integrates the major building blocks of a computing system (processor(s), memory, peripherals, and interconnect) onto a single semiconductor die.

**⚙️ Goal:** Provide a compact, power-efficient, and high-performance platform tailored to an application domain (mobile, IoT, automotive, edge AI).

**↔️ Contrast to multi-chip systems:** Replaces multiple discrete ICs connected over PCB traces with on-die communication — lowering latency, saving area and energy.

---

## ⚖️ SoC Design Tradeoffs & Key Challenges

**🔋 Power vs Performance:** Higher integration enables aggressive power optimization (clock/power gating) but complicates power-domain management.

**🧭 Complexity:** On-die heterogeneity (CPUs, accelerators, analog blocks) increases verification and timing challenges.

**📏 Area & Cost:** Integration reduces BOM and assembly cost but increases upfront design/verification investment.

**⏱ Timing closure:** Multiple clock domains and long cross-chip paths require careful timing analysis and synchronization.

**🛡 Reliability & Testability:** On-chip faults need DFT (scan, BIST) and supply domain isolation.

---

## 🧩 Core SoC Components (Roles & Rationale)

🧠 CPU (Processor): Executes instructions; coordinates peripherals; may be multi-core or microcontroller-class depending on use case.

💾 Memory: Volatile (RAM) for runtime data; non-volatile (Flash/ROM) for firmware; cache(s) to hide memory latency.

🔀 Interconnect / Bus Fabric: Connects masters (CPU, DMA, accelerators) to slaves (memory, peripherals). Examples: AXI/AHB/APB, Wishbone, TileLink, NoC.

🔌 Peripherals / I/O: UART, SPI, I²C, GPIO, ADC/DAC, USB, Ethernet — provide interfaces to the external world.

⚡ Power Management Unit (PMU): Controls voltage regulation, domain switching, sleep/retention states.

🧩 Specialized IP / Accelerators: Hardware blocks for compute-heavy tasks (crypto, AI, video) to boost throughput and efficiency.

🛠 Verification & Test Infrastructure: Testbenches, assertions, DFT logic for manufacturing and functional verification.

---

## 🔁 SoC Interconnects (Why they matter)

**🔹 Function:** Provide coherent, high-bandwidth pathways for data and control between IPs.

**🔹 Types & considerations:**

- **AMBA (AXI/AHB/APB):** Widely used, scales from high-performance (AXI) to lightweight (APB).

- **Wishbone / TileLink:** Simpler open standards suited for academic and open-source stacks.

- **NoC (Network on Chip):** Used for many-core systems for scalable throughput.

**🔹 Design concerns:** arbitration, QoS, throughput, latency, and deadlock avoidance.

---

## 🍼 VSDBabySoC — Block Overview

**🎯 Purpose:** A compact, educational SoC demonstrating core SoC ideas and a mixed-signal path: clocking (PLL) → processing (RISC-V CPU) → analog output (DAC).

**🔹 Scope:** Small number of modules so learners can inspect end-to-end behavior (instruction flow → digital values → analog signal).

**🔹 Learning benefits:** rapid simulation cycles, clear dataflow, and hands-on exposure to mixed-signal interactions.

<div align="center">

<img width="1024" height="1024" alt="BabySoC_block" src="https://github.com/user-attachments/assets/2256db26-22ca-4c4d-b0d7-60abdc5c8fa2" />

</div>

---

## ⚡ PLL (Phase-Locked Loop) — Theory

**🔸 Role:** Produce a stable system clock from a reference clock; can multiply/divide frequency and provide phase alignment.

**🔸 Signals:** clk_out (system clock), pll_locked (indicates stable lock).

**🔸 Behaviour (abstracted in simulation):** After power/reset, PLL takes a finite lock time; pll_locked goes high once the output phase/frequency is stable.

**🔸 Why model it?** Clock stability and lock latency affect reset release, synchronization, and system start-up sequencing.

---

## 🧠 RVMYTH (RISC-V Educational CPU) — Theory

🔸 ISA: RISC-V (open, modular instruction set), often used in education due to simplicity & industry adoption.

🔸 Implementation: Educational core may implement a minimal subset (RV32I) with a simple fetch-decode-execute pipeline.

🔸 Key internal elements: program counter (PC), register file, ALU, instruction memory (ROM), data memory (RAM), control logic.

🔸 Design language: TL-Verilog can be used for transaction-level clarity, then converted to synthesizable Verilog for simulation/synthesis.

🔸 Outputs for BabySoC: CPU writes processed samples to a designated register (e.g., r17) which drives the DAC input.

---

## 🎛 DAC (Digital-to-Analog Converter) — Theory

🔸 Role: Convert a digital word (10-bit in BabySoC) into an analog voltage (staircase approximation).

🔸 Resolution & range: 10 bits → 1024 discrete levels; resolution sets minimum step size.

🔸 Conversion methods (conceptual): resistor ladder, weighted DAC, or PWM+filter (implementation varies); simulations usually show ideal stair steps.

🔸 Signals: dac_value[9:0] (digital input), dac_ready/dac_valid handshake (optional), OUT (analog output).

🔸 Mixed-signal note: In RTL simulation the DAC is commonly modeled behaviorally — a register representing analog level — and visualized as an analog step waveform in GTKWave.

---

## 🔄 System Operation (High-Level Flow)

**1️⃣ Power on / reset → 2️⃣ PLL starts and eventually locks (pll_locked) → 3️⃣ Release/reset synchronizer allows CPU to start → 4️⃣ CPU fetches/executes instructions → 5️⃣ CPU writes digital samples to r17 → 6️⃣ DAC reads digital samples and updates OUT → 7️⃣ Analog device receives output.** 

**🧭 Start-up sequencing is critical:** PLL lock must be considered before allowing certain synchronous domains to begin.

## 🔬 Functional Modelling — Why It Matters

✅ Early validation: Confirms functional correctness before synthesis and physical design.

✅ Faster iteration: Fix design and protocol bugs in RTL — orders of magnitude cheaper than post-tapeout fixes.

✅ Architectural exploration: Compare different microarchitectures or peripheral interfaces without costly synthesis runs.

✅ Testbench-driven design: Encourages writing testcases that specify expected behavior (unit tests for hardware).

✅ Waveform insight: Waveforms reveal dynamic behavior — timing relationships, data flow, bus usage, and protocol violations.

---

## 🧾 Verification Concepts (Theory)

**🔹 Testbench:** Stimulus generator + DUT instantiation + monitors + checkers.

**🔹 Assertions:** Formalize expected properties (e.g., if write then ack within N cycles).

**🔹 Coverage:** Measure exercised code paths and transactions to ensure test completeness.

**🔹 Regression:** Automate repeated runs to catch regressions when code changes.

**🔹 Golden vectors:** Known good input→output traces for automated comparison.

---

🔗 Mixed-Signal Considerations (Theory)

**🔸 Domain boundaries:** Digital synchronous logic vs analog continuous signals require models and careful interfacing.

**🔸 Clock domain crossing (CDC):** Use synchronizers/handshakes when data crosses clock domains (e.g., CPU clock vs peripheral clocks).

**🔸 Analog modeling:** Simulated DAC outputs can be rendered as ideal steps; full analog accuracy requires SPICE or co-simulation if necessary.

**🔸 Verification strategies:** Behavioral analog models for functional checks; full analog verification only for final mixed-signal sign-off.

## 🎯 Learning Objectives (Part-1 Theory)

By the end of Part-1 learners should be able to:

✅ Explain what an SoC is and how it differs from multi-chip systems.

✅ Identify and describe core SoC components and their functions.

✅ Describe the BabySoC architecture and the role of PLL, RISC-V CPU (RVMYTH), and the 10-bit DAC.

✅ Explain why functional modelling is essential and list verification primitives (testbench, assertions, coverage).

✅ Understand mixed-signal interfacing basics and the importance of startup sequencing (PLL lock, resets).

---

## Part-2: Step‑by‑Step Practical Simulation 

### 🧰 Environment Preparation

- 📁 `git clone https://github.com/manili/VSDBabySoC.git` — clone repository.

```bash
cd VLSI/
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/
```

- 🐍 `python3 -m venv sp_env && source sp_env/bin/activate` — optional Python environment.

```bash
sudo apt update
sudo apt install python3-venv python3-pip

python3 -m venv sp_env
source sp_env/bin/activate

pip install pyyaml click sandpiper-saas
```

<div align="center">

<img width="1024" height="1024" alt="cloning_git" src="https://github.com/user-attachments/assets/6bf438e3-b320-4549-8a09-1237467fded0" />

</div>

- 📦 `pip install sandpiper-saas` — install tool for TL‑Verilog conversion.

### 🔁 Convert TL‑Verilog → Verilog

- 🔁 `sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/`  
- 🔍 **Note:** TL‑Verilog provides abstractions; conversion produces synthesis‑ready/verifiable Verilog.

<div align="center">

<img width="1024" height="1024" alt="tlv_to_v" src="https://github.com/user-attachments/assets/0322c51f-88e1-47a7-904b-3bbe690b819a" />

</div>

### 🧪 Compile & Run Simulation

- 🛠 Create output directory: `mkdir -p output/pre_synth_sim`  
- ⚙️ Compile:  
```bash
iverilog -o /home/chittesh/VLSI/VSDBabySoC/output/pre_synth_sim/pre_synth_sim.out   -DPRE_SYNTH_SIM -I /home/chittesh/VLSI/VSDBabySoC/src/include -I /home/chittesh/VLSI/VSDBabySoC/src/module /home/chittesh/VLSI/VSDBabySoC/src/module/testbench.v
```
- ▶️ Run and log: `cd output/pre_synth_sim && ./pre_synth_sim.out | tee simulation_log.txt`
```bash
cd output/pre_synth_sim
./pre_synth_sim.out
```

### 👁️ Waveform Inspection

- 🖼 Open GTKWave: `gtkwave output/pre_synth_sim/pre_synth_sim.vcd`  
- 🔎 Recommended signals: `clk`, `reset`, `pll_locked`, `RV_TO_DAC[9:0]`, `OUT`, `pc`, `insn`.
- 📝 Use bus formatters (hex/dec) and annotate significant events in GTKWave.

### 🔍 Testbench Best Practices (snippets)

- 💾 **VCD dump:** `$dumpfile("pre_synth.vcd"); $dumpvars(0, tb);`  
- ⏱ **Clock driver:** `always #5 clk = ~clk;` (adjust period as needed)  
- 🔔 **Event logging:** `if (cpu_writes_to_dac) $display("%0t: DAC <= %h", $time, cpu_dac_wdata);`

### Viewing Configuration

**Digital View Setup:**
- Add signals: CLK, reset, RV_TO_DAC[9:0], OUT
- Observe digital transitions

<div align="center">

<img width="1024" height="1024" alt="pre_synth_sim_out" src="https://github.com/user-attachments/assets/e38ce0ee-c367-4d6c-b37c-a2d3fbaa7ebc" />

</div>

**Analog View Setup:**
- Select OUT signal
- Right-click → Data Format → Analog → Step
- Observe analog staircase pattern

<div align="center">

<img width="1024" height="1024" alt="pre_synth_sim_out" src="https://github.com/user-attachments/assets/e4f08201-5d52-446a-aef3-6b2a7cc6aeec" />

</div>

---

## 📊 Results & Signal Analysis

| Signal                | Description                 |
| --------------------- | --------------------------- |
| 🕰 **CLK**            | Stable clock post-PLL lock  |
| 🔁 **reset**          | System initialization       |
| 🧾 **RV_TO_DAC[9:0]** | CPU output register data    |
| 📶 **OUT**            | Analog DAC staircase output |

### ✅ Verification Checklist

- [ ] Clock is stable and periodic.  
- [ ] `pll_locked` asserted after expected lock delay.  
- [ ] Reset initializes CPU and peripherals correctly.  
- [ ] CPU program counter (`pc`) advances and instructions execute.  
- [ ] DAC receives writes and `OUT` updates accordingly.  
- [ ] No visible bus contention or unexpected stalls.

### 🧭 Typical Observations

✔ After PLL lock, CPU writes values to DAC.

✔ DAC output forms analog staircase.

⚠ If CPU stalls → Check reset/PLL gating.

---

## 🏁 Conclusions

### 🔧 Technical Skills Gained

🧩 SoC architecture understanding  
🔀 Mixed-signal design basics  
⚙️ RTL simulation workflow  
📈 GTKWave waveform debugging  
🧰 TL-Verilog → Verilog flow  

---

### 💡 Design Principles

⏱️ Clock domain management  
🧪 Importance of pre-synthesis verification  
🧾 Testbench-driven hardware design  
🚨 Early bug detection saves cost

---

### ⚙️ Why Functional Modelling Matters

| Phase           | Bug Fix Cost |
| --------------- | ------------ |
| 🧩 Simulation   | Hours        |
| 🏭 Post-Silicon | 💸 Millions  |

**Verification Benefits:**
- Validates design before synthesis
- Enables architectural exploration
- Provides system behavior insights
- Supports regression testing

---

🚀 Next Steps

1️⃣ RTL Synthesis → Gate-level netlist
2️⃣ Gate-Level Simulation
3️⃣ Physical Design (Floorplan, P&R)
4️⃣ STA (Timing Analysis)
5️⃣ DRC/LVS Physical Verification

---

✅ Week-2 Summary

| Task                  | Status |
| --------------------- | ------ |
| SoC Fundamentals      | ✔      |
| Tool Setup            | ✔      |
| TL-V to Verilog       | ✔      |
| Functional Simulation | ✔      |
| Waveform Analysis     | ✔      |

> 🏅 Achievement: Functional Modelling Expert unlocked.

---

## References & Resources

- 🔗 VSDBabySoC — https://github.com/manili/VSDBabySoC  
- 🔗 RISC‑V Specification — https://riscv.org/  
- 🔗 TL‑Verilog Guide — https://www.redwoodeda.com/  
- 🔗 Icarus Verilog — http://iverilog.icarus.com/  
- 🔗 GTKWave — http://gtkwave.sourceforge.net/

---

👉 **Week-0 Repository Link:** https://github.com/CHITTESH-S/Week-0_RISC-V_SoC_TapeOut

👉 **Week-1 Repository Link:** https://github.com/CHITTESH-S/Week-1_RISC-V_SoC_TapeOut

👉 **Week-3 Repository Link:** https://github.com/CHITTESH-S/Week-3_RISC-V_SoC_TapeOut

👉 **Main Repository Link:** https://github.com/CHITTESH-S/RISC-V_SoC_TapeOut_VSD

👨‍💻 **Contributor:** Chittesh S


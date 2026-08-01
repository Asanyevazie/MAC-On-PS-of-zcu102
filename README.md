<h1 align="center">🚀 Ethernet MAC Communication on Zynq Ultrascale+ PS</h1>

<p align="center">
  <b>High-Performance Baremetal DMA Networking on Xilinx ZCU102</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Board-ZCU102-blue.svg?style=for-the-badge" alt="Board">
  <img src="https://img.shields.io/badge/Tools-Vivado%20%7C%20Vitis%202021.2-orange.svg?style=for-the-badge" alt="Vivado">
  <img src="https://img.shields.io/badge/Language-C%20(Baremetal)-green.svg?style=for-the-badge" alt="Language">
  <img src="https://img.shields.io/badge/Architecture-ARM%20Cortex--A53-lightgrey.svg?style=for-the-badge" alt="Arch">
</p>

> A comprehensive, step-by-step guide and baremetal C application examples for utilizing the Gigabit Ethernet MAC (GEM) within the Processing System (PS) of Xilinx Zynq Ultrascale+ MPSoCs. The project focuses on high-performance DMA-based data transfer, demonstrating both single-direction bursts and continuous bidirectional throughput approaching line rate (~977 Mbps).

---

## 🌟 Project Overview

The examples provided here build upon the standard Xilinx `xemacps` driver, significantly extending it to showcase practical, high-throughput scenarios commonly encountered in **Data Acquisition** and **Hardware-in-the-Loop (HIL)** testing.

### ✨ Key Features
* 🎯 **Target Platform:** Xilinx ZCU102 Evaluation Kit (Zynq Ultrascale+ XCZU9EG).
* ⚙️ **Operation Mode:** Baremetal (No OS), leveraging Interrupt-Driven DMA.
* 🚀 **Capabilities Demonstrated:**
  * High-speed **Burst Transmission** from DDR memory to PC.
  * **Simultaneous Bidirectional Transfer** (TX & RX).
  * Continuous, infinite-loop **Ring Buffer Management** for real-time streaming.
  * Custom payload generation (e.g., 20% Duty Cycle signal injection).

---

## 📂 Repository Structure

The repository is logically separated into hardware design, software applications, and supporting evidence:

```text
📦 MAC-On-PS-of-Zynq
 ┣ 📂 Vivado_Design           # Hardware design files
 ┃ ┗ 📜 design_1.tcl          # Tcl script to recreate the Zynq PS & DDR4 design
 ┣ 📂 Vitis_Applications      # Baremetal C source codes
 ┃ ┣ 📂 App1_Burst_Transfer   # App 1: Transmits 1000 packets in a single burst
 ┃ ┣ 📂 App2_Sim_TxRx         # App 2: Simultaneous transmit and receive
 ┃ ┗ 📂 App3_Continuous       # App 3: Real-time continuous stream with Duty Cycle
 ┣ 📂 Images                  # Proof of concept & performance
 ┃ ┣ 🖼️ Putty.png             # Serial output showing successful link & transfer
 ┃ ┣ 🖼️ Wireshark.png         # Packet capture verification
 ┃ ┗ 🖼️ 1.png                 # ~977 Mbps throughput on Windows Task Manager
 ┗ 📜 README.md               # You are here!

💻 Application Details
1️⃣ App 1: Burst Transfer (App1_Burst_Transfer)
This foundational example demonstrates how to configure the GEM, set up the PHY (supporting Marvell and TI), allocate DMA Buffer Descriptors (BDs), and transmit a fixed number of packets (1000) stored in DDR memory as quickly as possible. It includes packet indexing within the payload to verify data integrity upon reception.

2️⃣ App 2: Simultaneous TX/RX (App2_Sim_TxRx)
Expanding on the first app, this example configures both the Transmit and Receive DMA channels. It sends 1000 packets while simultaneously receiving incoming traffic. It utilizes cache invalidation (Xil_DCacheInvalidateRange) to ensure the CPU reads fresh data from DDR and dynamically filters out unwanted broadcast traffic from the host PC.

3️⃣ App 3: Continuous Streaming & Signal Generation (App3_Continuous)
This is the most advanced example, transforming the Zynq into a continuous data streamer. It implements an infinite while(1) loop to dynamically manage the DMA Ring Buffers—reclaiming processed descriptors and assigning new ones on the fly. Furthermore, it injects a custom 20% duty cycle step signal into the payload, demonstrating real-time data synthesis.

🛠️ Hardware Setup (Vivado)
The hardware design is intentionally minimal, focusing solely on the Processing System (PS) and DDR memory to highlight GEM capabilities. To recreate the project:

Open Vivado 2021.2.

Create a new project targeting the ZCU102 board (xczu9eg-ffvb1156-2-e).

Run the provided Tcl script via the Tcl Console:

Tcl
source Vivado_Design/design_1.tcl
This script instantiates the zynq_ultra_ps_e IP, configures the GEM3 interface (MIO 64-75), sets up the MDIO, and connects a DDR4 memory controller via an AXI SmartConnect.

Generate the bitstream and export the hardware (.xsa) for Vitis.

🖥️ Software Setup (Vitis)
Open Vitis and create a new platform project using the exported .xsa file.

Create a new Empty Application project targeting the Cortex-A53 processor.

Copy the source files (.c and .h) from your desired Vitis_Applications subfolder into the src directory of your Vitis application.

Build the project.

Connect your ZCU102 to your PC via Ethernet and Serial (UART).

Run the application using the System Debugger.

📊 Results & Proof of Concept
The Images/ directory contains visual proof of the system's exceptional performance:

🔎 Wireshark Capture: Verifies that the custom MAC address and incrementing payloads are correctly formed and transmitted over the wire.

🚀 Throughput Monitor (1.png): Demonstrates the continuous streaming application sustaining a massive ~977 Mbps data rate into a Windows PC, proving the efficiency of the DMA baremetal implementation.

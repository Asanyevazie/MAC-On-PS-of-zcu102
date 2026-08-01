Ethernet MAC Communication on Zynq
Ultrascale+ PS (Baremetal)
This repository provides a comprehensive, step-by-step guide and baremetal application
examples for utilizing the Gigabit Ethernet MAC (GEM) within the Processing System (PS) of
Xilinx Zynq Ultrascale+ MPSoCs (specifically tested on the ZCU102 Evaluation Board). The
project focuses on high-performance DMA-based data transfer, demonstrating both
single-direction bursts and continuous bidirectional throughput approaching line rate.
Project Overview
The examples provided here build upon the standard Xilinx xemacps driver, extending it to
showcase practical, high-throughput scenarios commonly encountered in data acquisition and
hardware-in-the-loop (HIL) testing.
Key Features:
● Target Platform: Xilinx ZCU102 Evaluation Kit (Zynq Ultrascale+ XCZU9EG).
● Environment: Xilinx Vivado and Vitis (tested with version 2021.2).
● Operation Mode: Baremetal (No OS), leveraging Interrupt-Driven DMA.
● Capabilities Demonstrated:
○ High-speed Burst Transmission from DDR memory to PC.
○ Simultaneous Bidirectional Transfer (TX & RX).
○ Continuous, infinite-loop Ring Buffer management for real-time streaming.
○ Custom payload generation (e.g., 20% Duty Cycle signal injection).
Repository Structure
The repository is organized to separate the hardware design, software applications, and
supporting evidence:
MAC-On-PS-of-Zynq/
├── Vivado_Design/ # Contains the Vivado project script and
Block Design
│ └── design_1.tcl # Tcl script to recreate the Zynq PS and
DDR4 hardware design
├── Vitis_Applications/ # Baremetal C applications for the ARM
Cortex-A53
│ ├── App1_Burst_Transfer/ # App 1: Sends 1000 packets in a single
burst
│ ├── App2_Sim_TxRx/ # App 2: Simultaneous transmit and
receive
│ └── App3_Continuous/ # App 3: Real-time continuous stream with
Duty Cycle signal
├── Images/ # Screenshots proving operation and

throughput
│ ├── Putty.png # Serial output showing successful link
and transfer
│ ├── Wireshark.png # Wireshark capture of the transmitted
packets
│ └── 1.png # Task Manager showing ~977 Mbps
throughput!
└── README.md # This file

Application Details
App 1: Burst Transfer (App1_Burst_Transfer)
This foundational example demonstrates how to configure the GEM, set up the PHY (supporting
Marvell and TI), allocate DMA Buffer Descriptors (BDs), and transmit a fixed number of packets
(1000) stored in DDR memory as quickly as possible. It includes packet indexing within the
payload to verify data integrity upon reception.
App 2: Simultaneous TX/RX (App2_Sim_TxRx)
Expanding on the first app, this example configures both the Transmit and Receive DMA
channels. It sends 1000 packets while simultaneously receiving incoming traffic. It utilizes cache
invalidation (Xil_DCacheInvalidateRange) to ensure the CPU reads fresh data from DDR and
filters out unwanted broadcast traffic from the host PC.
App 3: Continuous Streaming & Signal Generation
(App3_Continuous)
This is the most advanced example, transforming the Zynq into a continuous data streamer. It
implements an infinite while(1) loop to dynamically manage the DMA Ring Buffers—reclaiming
processed descriptors and assigning new ones on the fly. Furthermore, it injects a custom 20%
duty cycle step signal into the payload, demonstrating real-time data synthesis. As shown in
Images/1.png, this application achieves a remarkable receive throughput of ~977 Mbps on the
host PC, nearly maxing out the Gigabit Ethernet link.
Hardware Setup (Vivado)
The hardware design is minimal, focusing solely on the Processing System (PS) and DDR
memory. To recreate the project:
1. Open Vivado 2021.2.
2. Create a new project targeting the ZCU102 board (xczu9eg-ffvb1156-2-e).
3. Run the provided Tcl script: source Vivado_Design/design_1.tcl.
4. This script will instantiate the zynq_ultra_ps_e IP, configure the GEM3 interface (MIO
64-75), set up the MDIO, and connect a DDR4 memory controller via an AXI
SmartConnect.
5. Generate the bitstream and export the hardware (XSA) for use in Vitis.

Software Setup (Vitis)
1. Open Vitis and create a new platform project using the exported XSA file.
2. Create a new Empty Application project targeting the Cortex-A53 processor.
3. Copy the source files (.c and .h) from one of the Vitis_Applications folders into your
application's src directory.
4. Build the project.
5. Connect your ZCU102 to your PC via Ethernet and Serial.
6. Run the application using the System Debugger.
Results and Proof of Concept
The Images/ directory contains visual proof of the system's performance:
● Wireshark Capture: Verifies that the custom MAC address and incrementing payloads
are correctly formed and transmitted over the wire.
● Throughput Monitor: Demonstrates the continuous streaming application sustaining a
massive ~977 Mbps data rate into a Windows PC.
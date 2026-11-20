---
title: RISC-V Instruction Extension for Post-Quantum Signatures
subtitle: A customized RISC-V processor with SIMD instructions that makes SLH-DSA signatures about 5× faster on an FPGA
date: 2026-06-01
category: research
detail: true
selected: true
tags: [Hardware, Security, FPGA]
venue: Undergraduate research project
team: "陳彥定, 黃子洵, <span class=\"me\">Li-Cheng Tseng</span>, 蔡丞昕 · Advisor: Prof. Pei-Yin Chen"
role: System integration and hardware verification on FPGA (25%)
summary: >
  SLH-DSA is a hash-based **post-quantum signature** scheme that is slow on small IoT devices. Reproducing the RVSLH paper,
  we added **SIMD instructions** to a RISC-V core and ran it on an FPGA — about **5× faster** signatures and up to
  **34× faster** hashing. I built the FPGA system that connects the processor to the board and runs the software on it.
img: /assets/img/projects/riscv/thumb.jpg
tech: [CV32E40P, Synopsys VCS, riscv32-unknown-elf-gcc, Vivado, Xilinx PYNQ-Z2]
links:
  - label: Demo video
    url: https://youtu.be/zEEjedi2-EU
  - label: Code
    url: https://github.com/ukp66482/SLH-DSA-on-cv32e40p
videos:
  - title: Demo on the PYNQ-Z2 board
    id: zEEjedi2-EU
---

## Overview

Post-quantum signatures are coming to IoT devices, but **SLH-DSA** (based on SPHINCS+) is slow on small processors because it spends most of its time hashing with Keccak and SHAKE256. Our project reproduces the hardware–software co-design from the paper *RVSLH: Acceleration of Post-quantum Standard SLH-DSA With Customized RISC-V Processor*, on the open-source **CV32E40P** RISC-V core.

## What we built

**Hardware — a SIMD engine inside the CPU**

- **320-bit SIMD registers and ALU** that hold five 64-bit Keccak lanes (one row of the state) at once, removing the data dependencies that make Keccak slow in software.
- **Custom instructions** such as `xor3v`, `xorrv`, `rxorv`, and `xornav` that turn long chains of software logic into single operations.
- **New load/store instructions** (`lv`, `sv`) that move whole blocks between memory and the SIMD registers.
- **Transpose and zero registers** that make the 5×5 Keccak state easy to transpose and access.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/riscv/architecture.png' | relative_url }}" alt="Modified CV32E40P pipeline with new SIMD register file and SLH-ALU" loading="lazy">
  <figcaption>The modified CV32E40P core: green blocks are new (SIMD register file, SLH-ALU), blue blocks are modified.</figcaption>
</figure>

**Software — removing data copies**

The reference C code builds every hash input with `memcpy`. We pre-allocated contiguous structures and updated addresses in place, removing those copies entirely.

## My part: running it on a real FPGA

I was responsible for **system integration and hardware verification** on the Xilinx PYNQ-Z2 board:

- **A shared-memory SoC.** The board's ARM processor acts as the host and our RISC-V core as a co-processor; the two communicate through a **dual-port BRAM**.
- **Bare-metal software.** With no operating system on the RISC-V side, I rewrote the startup code (`boot.S`) and linker script (`link.ld`), cross-compiled the SLH-DSA C code, and mapped it into the FPGA's separate instruction and data memories.

## Results

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/riscv/speedup.jpg' | relative_url }}" alt="Speedups: Keccak-f 34.76x, SHAKE256 23.95x, SLH-DSA about 5x" loading="lazy">
</figure>

| Parameter set | Original CPU | With SIMD instructions | Speedup |
| --- | --- | --- | --- |
| SLH-DSA-SHAKE-128f | 64 s | 12 s | ×5.33 |
| SLH-DSA-SHAKE-192f | 102 s | 20 s | ×5.1 |
| SLH-DSA-SHAKE-256f | 208 s | 40 s | ×5.2 |

| Kernel | Original (cycles) | Ours (cycles) | Speedup |
| --- | --- | --- | --- |
| Keccak-f[1600] | 42,793 | 1,231 | ×34.76 |
| SHAKE256 | 220,357 | 9,200 | ×23.95 |

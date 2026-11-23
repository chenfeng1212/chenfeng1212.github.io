---
title: RISC-V Instruction Extension for Post-Quantum Signatures
subtitle: A customized RISC-V processor with SIMD instructions that runs post-quantum SLH-DSA signatures about 13× faster, verified on real FPGA boards
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
  we added **SIMD instructions** to a RISC-V core and ran it on an FPGA — about **13× fewer cycles** for full signatures and up to
  **34×** for the core hash. I built the FPGA system that connects the processor to the board and runs the software on it.
img: /assets/img/projects/riscv/thumb.jpg
hero: /assets/img/projects/riscv/demo_boards.jpg
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

Today's digital signatures (RSA, ECC) could be broken by a large quantum computer. In 2024 NIST standardized **SLH-DSA** (FIPS 205, based on SPHINCS+), a post-quantum signature scheme whose security rests only on hash functions. The catch is speed: it hashes constantly, and on a small CPU a single signature can take **up to half an hour**. That makes it hard to use on IoT and edge devices.

Our project reproduces the hardware–software co-design from the paper *RVSLH: Acceleration of Post-quantum Standard SLH-DSA With Customized RISC-V Processor* on the open-source **CV32E40P** RISC-V core, and takes it all the way to working FPGA hardware.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/riscv/signature_flow.jpg' | relative_url }}" alt="Digital signature flow: signer hashes data and runs the signature algorithm; verifier runs the verification algorithm and compares hashes" loading="lazy">
  <figcaption>The signing and verification flow. The purple blocks — where SLH-DSA spends its time hashing — are what we accelerate in hardware.</figcaption>
</figure>

## What we built

### Hardware — a SIMD engine inside the CPU

- **320-bit SIMD registers and ALU** that hold five 64-bit Keccak lanes (one row of the state) at once, removing the data dependencies that make Keccak slow in software.
- **Custom instructions** that turn long chains of software logic into single operations:

| Instruction | What it does |
| --- | --- |
| `xor3v` | XOR three SIMD registers |
| `xorrv` | Rotate and XOR one SIMD register |
| `rxorv` | XOR two SIMD registers, then rotate and reorder |
| `xornav` | Compute `(~A & B) ^ C` on three SIMD registers |
| `lv` / `sv` | Load / store a full 320-bit vector between memory and a SIMD register |

- **Transpose and zero registers**: extra register names map onto the same physical registers in transposed order, so the 5×5 Keccak state can be transposed without moving any data.

<div class="pj-split">
  <figure class="pj-figure pj-code">
    <pre><code>;; Compute C
xor3v   v5,  v0,  v1,  v2
xor3v   v5,  v3,  v4,  v5
;; Compute D
xorrv   v5,  v5
;; Compute B
rxorv   v6,  v0,  v5,  x0
rxorv   v7,  v1,  v5,  x1
rxorv   v8,  v2,  v5,  x2
rxorv   v9,  v3,  v5,  x3
rxorv   v10, v4,  v5,  x4
;; Compute E
xornav  v11, v7,  v8,  v6
xornav  v12, v8,  v9,  v7
xornav  v13, v9,  v10, v8
xornav  v14, v10, v6,  v9
xornav  v15, v6,  v7,  v10
;; xor x[0] with RC
...</code></pre>
    <figcaption>One Keccak-f[1600] round written with our instructions.</figcaption>
  </figure>
  <figure class="pj-figure">
    <img src="{{ '/assets/img/projects/riscv/register_map.jpg' | relative_url }}" alt="Register mapping: rows x0 to x4 and columns x16 to x20 refer to the same 5x5 grid of 64-bit lanes" loading="lazy">
    <figcaption>Transposed register mapping: x0–x4 read the state by rows, x16–x20 by columns. Each cell is 64 bits.</figcaption>
  </figure>
</div>

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/riscv/architecture.png' | relative_url }}" alt="Modified CV32E40P pipeline with new SIMD register file and SLH-ALU" loading="lazy">
  <figcaption>The modified CV32E40P core: green blocks are new (SIMD register file, SLH-ALU), blue blocks are modified.</figcaption>
</figure>

### Software — removing data copies

The NIST reference code builds every hash input with `memcpy`, stitching together the public seed, address, and data. We pre-allocated contiguous structures and updated addresses **in place**, removing those copies entirely. Because the Keccak state now lives in the SIMD registers, memory accesses drop as well.

## My part: running it on real FPGA boards

I was responsible for **system integration and hardware verification** on the Xilinx PYNQ-Z2 board:

- **A shared-memory SoC.** The board's ARM Cortex-A9 acts as the host and our RISC-V core as a co-processor. The ARM loads the program and data into the RISC-V's instruction and data memories through DMA and AXI BRAM controllers, then starts it and waits for "done" through GPIO.
- **Bare-metal software.** With no operating system on the RISC-V side, I rewrote the startup code (`boot.S`) and linker script (`link.ld`), cross-compiled the SLH-DSA C code to a raw binary, and mapped it into the FPGA's separate instruction and data memories.
- **Automated verification** with Python scripts on the ARM side.

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/riscv/soc.jpg' | relative_url }}" alt="SoC block diagram: Zynq Cortex-A9, DMA, two AXI BRAM controllers, data and instruction memory, CV32E40P RISC-V CPU, GPIO start/done" loading="lazy">
  <figcaption>The SoC I built on the PYNQ-Z2: the ARM host talks to our RISC-V core through shared BRAM.</figcaption>
</figure>

For the demo, **one board signs a file and sends the signature over a socket to a second board, which verifies it** — a complete post-quantum signing exchange between two devices.

## Results

<figure class="pj-figure">
  <img src="{{ '/assets/img/projects/riscv/speedup.jpg' | relative_url }}" alt="Speedups: Keccak-f 34.76x, SHAKE256 23.95x, SLH-DSA about 13 to 14x" loading="lazy">
</figure>

| Parameter set | Original CPU | With our SIMD core | Speedup (cycles) |
| --- | --- | --- | --- |
| SLH-DSA-SHAKE-128f | 2.67 M kcycles · 56.1 s | 0.19 M kcycles · 6.0 s | ×13.95 |
| SLH-DSA-SHAKE-192f | 4.30 M kcycles · 88.2 s | 0.32 M kcycles · 10.0 s | ×13.56 |
| SLH-DSA-SHAKE-256f | 8.70 M kcycles · 176.3 s | 0.65 M kcycles · 16.1 s | ×13.32 |

| Kernel | Original (cycles) | Ours (cycles) | Speedup |
| --- | --- | --- | --- |
| Keccak-f[1600] | 42,793 | 1,231 | ×34.76 |
| SHAKE256 | 220,357 | 9,200 | ×23.95 |

Full-scheme numbers were compiled with `riscv64-unknown-elf-gcc` 15.1.0 at `-O3`.

## What's next

- Turn the board into a **post-quantum FIDO2 security key**, adding the CTAP2 protocol and a USB interface for passwordless WebAuthn login.
- Reuse the SIMD engine for other Keccak-heavy standards such as **ML-KEM**.

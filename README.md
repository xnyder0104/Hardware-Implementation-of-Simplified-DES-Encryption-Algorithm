
# Hardware Implementation of Simplified DES Encryption Algorithm

## Project Overview

This project was developed for the UDYAM'24 DIGISIM PS-1 competition by Team Kaizer. The objective was to design and simulate a digital logic circuit capable of encrypting (and decrypting) an 8-bit digital message using a customized, simplified version of the Data Encryption Standard (DES) algorithm.

To demonstrate hardware design versatility and trade-offs, the project features two distinct architectural implementations in Proteus: a **Combinational Circuit** approach optimized for minimal cost and latency, and a **Sequential Circuit** approach that reuses hardware components across multiple clock cycles to complete the encryption rounds.

---

## Key Features & Results

* **Dual Architectural Designs:** Successfully designed both combinational (unrolled loop) and sequential (state-machine based) circuits to solve the same cryptographic problem, highlighting the trade-offs between hardware footprint and clock-cycle timing.
* **Custom S-Box Logic:** Reduced complex substitution box (S-Box) lookups into raw digital logic gates using Karnaugh Map (K-Map) simplification, replacing expensive ROM or multiplexer arrays with basic logic gates.
* **Dynamic Key Generation:** Engineered a hardware key-schedule algorithm that accurately performs left circular shifts (1-bit for odd rounds, 2-bits for even rounds) and compresses an 8-bit initial key into four unique 6-bit round keys.
* **Cost Optimization:** * **Combinational Approach:** Achieved an ultra-low simulated component cost of **Rs. 8** by relying heavily on hardwired permutations and optimized logic gates.
* **Sequential Approach:** Designed a robust clocked system using counters, registers, and multiplexers with a total simulated cost of **Rs. 99.3**.


* **Bonus Implementation:** Integrated a mode-selection toggle to allow the same hardware architecture to perform both encryption (Mode = 0) and decryption (Mode = 1).

---

## Working Principle

The encryption process mimics a 4-round Feistel network operating on 8-bit plain text and an 8-bit initial key:

* **Initial Permutation:** The 8-bit input message undergoes a fixed permutation and is split into two 4-bit halves: Left ($L_0$) and Right ($R_0$).
* **Round Function ($f$):** In each round, the 4-bit Right half is expanded and permuted to 6 bits. It is then XORed with the 6-bit Round Key.
* **Substitution (S-Boxes):** The 6-bit XOR result is split into two 3-bit segments. Each segment acts as a coordinate (row and column) for two distinct S-Boxes, which output 2 bits each. These 4 bits are merged and permuted to complete the $f$ function.
* **Round Logic:** The output of the $f$ function is XORed with the Left half. The halves are then swapped for the next round:
* $L_i = R_{i-1}$
* $R_i = L_{i-1} \oplus f(RK_i, R_{i-1})$


* **Inverse Permutation:** After 4 rounds, $L_4$ and $R_4$ are merged and passed through a final inverse permutation block to produce the 8-bit encrypted ciphertext.

---

## Hardware Design & Architecture

### 1. Combinational Circuit Approach

* **Hardwired Routing:** Permutations (Initial, Expansion, and Inverse) and circular shifts for key generation were implemented purely through wire routing (crossing inputs to outputs) costing zero virtual rupees.
* **Parallel Execution:** All 4 rounds are physically instantiated sequentially in the schematic, meaning the data flows from input to output asynchronously without clock pulses.
* **Logic Gate F-Box:** The S-Boxes were synthesized into discrete NOT, AND, OR, and XOR gates based on Boolean expressions derived from K-Maps.

### 2. Sequential Circuit Approach

* **Hardware Reuse:** Instead of unrolling all 4 rounds, a single F-Box and key generation module were built and reused over 4 clock cycles.
* **Control Path:** Utilized a clock generator, 4-bit counters, and T-flip-flops to keep track of the current round (1 to 4) and control multiplexers for data routing.
* **Clock-Driven Key Generation:** Round keys were generated dynamically using shift registers. A combination of single and double frequency clock pulses were routed to the shift registers to achieve the alternating 1-bit and 2-bit circular shifts required by the algorithm.
* **State Storage:** 4-bit registers were used to hold the $L_i$ and $R_i$ values between clock cycles, with the final ciphertext captured and locked in an output register after the 4th cycle.

---

## Applications

* Educational demonstrations of Feistel networks and symmetric-key block ciphers at the hardware level.
* Ultra-lightweight cryptography for resource-constrained IoT devices or RFID tags.
* ASIC/FPGA logic design and state-machine optimization prototyping.

---

## Future Scope

* **Pipeline Architecture:** Implementing a fully pipelined sequential design with intermediate registers to encrypt a new 8-bit block on every clock cycle, maximizing throughput.
* **Algorithm Scaling:** Expanding the datapath and logic from this 8-bit simplified model to implement a full 64-bit DES or Advanced Encryption Standard (AES) accelerator.
* **FPGA Deployment:** Translating the Proteus schematic logic into Verilog/VHDL for physical deployment and timing analysis on an FPGA development board.

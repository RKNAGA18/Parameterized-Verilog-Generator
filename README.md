# Parameterized-Verilog-Generator
A Perl-based generator for creating parameterized structural Verilog code for hybrid adders, $N \times N$ multipliers, and N-tap FIR filters.
# Parameterized Verilog Hardware Generator

This project is a multi-level hardware generator written in Perl. The main script, `gen_struct.pl`, automatically produces parameterized, structural Verilog code for complex digital components, including a hybrid adder, an array multiplier, and an N-tap FIR filter.

## 🚀 Project Levels

The project is divided into three levels of increasing complexity, with each level building upon the previous one.

### Level I: Hybrid N-bit Adder

* **Goal:** Implement a Perl generator for a parameterized $N$-bit hybrid adder.
* **Architecture:** The $N$ bits are partitioned into:
    * **LSB block ($K$ bits):** Implemented as a **Carry-Select Adder (CSLA)**.
    * **MSB block ($N-K$ bits):** Implemented as a **Ripple Carry Adder (RCA)**, which takes the carry-out of the CSLA as its carry-in.
* **Parameters:** The generator accepts $N$ (total bits) and $K$ (LSB bits, where $k = \text{floor}(n/2)$ is a good default).
* **Output:** A module (e.g., `hybrid_adder_N_k`) and a self-checking testbench to verify $a + b + c_{\text{in}}$ for random vectors.

### Level II: $N \times N$ Multiplier Generator

* **Goal:** Extend the generator to produce an $N \times N$ structural multiplier (e.g., Array Multiplier).
* **Integration:** This module **instantiates the generated hybrid adder** from Level I to perform the partial-product accumulation/addition.
* **Output:** A parameterized $N \times N$ multiplier module and a testbench to verify its behavior.

### Level III: N-tap FIR Filter Generator

* **Goal:** Extend the generator to produce a parameterized $N$-tap Finite Impulse Response (FIR) filter RTL.
* **Integration:**
    * Instantiates the **generated array multiplier** from Level II for the multiplication operations (tap $\times$ coefficient).
    * Uses the **hybrid adder** from Level I for the adder tree reductions.
* **Features:**
    * Supports parameterized tap count ($N$).
    * Supports precision of **8-bit** and **16-bit**.
    * Accepts a coefficient list as input.
* **Output:** A parameterized FIR filter module and testbenches to validate its operation with random inputs.

---

## 🛠️ Usage

The core of the project is the `gen_struct.pl` Perl script. It is driven by command-line arguments to specify the desired module and its parameters.

### Examples

**Level I: Generate a 32-bit Hybrid Adder**
(with 12 bits for the LSB CSLA block)

```bash
perl gen_struct.pl --gen csla --n 32 --lsb 12 --out hybrid_32_12.v --name hybrid32_12
```

**Level II: Generate a 16x16 Array Multiplier**

```bash
perl gen_struct.pl --gen mul --n 16 --out array_mult_16x16.v --name array16
```

**Level III: Generate an 8-tap, 16-bit Precision FIR Filter**

```bash
perl gen_struct.pl --gen fir --taps 8 --precision 16 --out fir_8_16.v --name fir8_16
```

---

## 📂 Deliverables

This repository contains:

* `gen_struct.pl`: The main Perl generator script.
* **Generated Verilog:** All output `.v` files for the adders, multipliers, and FIR filters.
* **Testbenches:** Self-checking Verilog testbenches (`_tb.v`) for each generated module.
* `report.pdf`: A 1-2 page report detailing the implementation, design choices, and verification strategy.

# Grover's Algorithm — 10-Qubit Implementation

A full implementation of **Grover's quantum search algorithm** using [Qiskit](https://qiskit.org/), built as a Quantum Computing course project. The notebook searches a 2¹⁰ = 1,024-state space for two marked states using two independently built oracle approaches, achieving ~99.9% success probability at the optimal iteration count.

---

## Overview

Grover's algorithm provides a quadratic speedup over classical unstructured search. Where a classical approach requires up to **512 queries** to find 2 items in 1,024 states, this implementation finds them in just **17 oracle evaluations**.

| Property | Value |
|---|---|
| Qubits | 10 (Part A) / 11 with ancilla (Part B) |
| Search space | 2¹⁰ = 1,024 states |
| Marked states | `0110011010`, `1101010001` |
| Optimal iterations | k = 17 |
| Shots per simulation | 8,192 |
| Success probability at k=17 | ~99.94% |

---

## Project Structure

```
Grovers-Algorithm-Project.ipynb   ← Main notebook (run this)
README.md
```

---

## Requirements

- Python **3.8+** (developed on 3.13.3)
- Jupyter Notebook or JupyterLab

Install the required Python packages:

```bash
pip install qiskit qiskit-aer matplotlib pylatexenc
```

---

## How to Run

### Option 1 — Run Locally

**1. Clone the repository**

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

**2. Install dependencies**

```bash
pip install qiskit qiskit-aer matplotlib pylatexenc
```

**3. Launch Jupyter**

```bash
jupyter notebook
```

**4. Open and run the notebook**

Open `Grovers-Algorithm-Project.ipynb` and run all cells in order from top to bottom via **Cell → Run All**, or step through them individually with `Shift+Enter`.

> ⚠️ Cells must be run **in order** — later cells depend on functions and variables defined earlier.

---

### Option 2 — Run on Google Colab

1. Go to [https://colab.research.google.com](https://colab.research.google.com)
2. Click **File → Open notebook → GitHub**
3. Paste the repository URL and select `Grovers-Algorithm-Project.ipynb`
4. Run **Runtime → Run all** (`Ctrl+F9`)

No local setup needed for this option.

---

## Notebook Sections

| Section | Description |
|---|---|
| **Setup & Imports** | Loads Qiskit, Aer simulator, and matplotlib |
| **Problem Setup** | Defines the 10-qubit search space, target states, and computes optimal iteration count |
| **Section 1 — Oracle Construction** | Introduces both oracle designs |
| **Part A — IBM-Style Oracle** | Uses `MCMTGate(ZGate(), ...)` with the open-control trick, adapted from IBM Quantum documentation |
| **Part B — Phase Kickback Oracle** | Custom oracle built from scratch using `mcx` and an ancilla qubit prepared in \|−⟩ |
| **Diffusion Operator** | Manual implementation of the Grover diffusion (H·X·MCZ·X·H) — no `grover_operator()` used |
| **Grover Iterations** | Full Grover loop assembled from oracle + diffuser for both Part A and Part B |
| **Simulation & Results** | Runs circuits at k = 1, 3, 5, 10, 17 iterations with 8,192 shots each |
| **Visualizations** | Measurement histograms showing probability amplification across iterations |
| **Section 9 — Full Summary** | Side-by-side comparison table of Part A vs Part B results vs theoretical values |
| **Conclusion** | Discussion of diffusion operator intuition and classical vs quantum efficiency |

---

## Results

Both implementations closely match theoretical predictions:

| k (iterations) | Theory | Part A (sim) | Part B (sim) |
|---|---|---|---|
| 1 | 0.0175 | 0.0165 | 0.0182 |
| 3 | 0.0927 | 0.0898 | 0.0905 |
| 5 | 0.2184 | 0.2219 | 0.2147 |
| 10 | 0.6410 | 0.6334 | 0.6382 |
| **17 (optimal)** | **0.9994** | **0.9994** | **0.9990** |

---

## Implementation Notes

- **No high-level abstractions used** — `qiskit.algorithms.Grover` and `grover_operator()` are explicitly avoided; everything is built gate-by-gate.
- **Part A** uses 10 qubits. The oracle applies X gates on `'0'` positions of the target bitstring (open-control trick) then a multi-controlled-Z.
- **Part B** uses 11 qubits (10 data + 1 ancilla). The ancilla is initialized to |−⟩ so that an MCX gate kicks back a −1 phase onto the marked state; the ancilla is uncomputed back to |0⟩ afterward.
- Qiskit uses **little-endian** qubit ordering, so bitstrings are reversed internally before gate application.

---

## Reference

- IBM Quantum — Grover's Algorithm Tutorial: https://quantum.cloud.ibm.com/docs/en/tutorials/grovers-algorithm

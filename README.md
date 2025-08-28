# Circuit Fidelity vs. Circuit Depth (Mirror Quantum Circuit) Experiment

Measure how circuit fidelity decays with depth using **mirror circuits** in Qiskit.

## Overview

For each depth, the script builds a random “forward” circuit (1-qubit gates: `sx/x/h/rx/ry/rz` + entangling `cx`), inserts a **barrier** to prevent compiler cancellation, appends the exact **inverse**, and measures the probability of all zeros `P(0…0)`. A simple depolarizing model maps this success probability to an estimated **fidelity**. Results are saved to CSV and summarized by depth.

## Features

* Noisy **Aer** simulator (default) with configurable depolarizing + readout errors
* **IBM hardware** support via Runtime **SamplerV2**
* Records raw depth (pre-transpile) and ISA depth (post-transpile)
* Reproducible trials, CSV export, console summary

## Requirements

```bash
python -m venv .venv && source .venv/bin/activate
pip install "qiskit[visualization]" qiskit-ibm-runtime qiskit-aer
```

## Quick Start (Noisy Simulator)

```bash
python fidelity_vs_depth_mirror.py
```

Outputs `fidelity_vs_depth.csv` and a depth summary in the terminal.

## Run on IBM Hardware

1. Save your IBM token once in Python:

```python
from qiskit_ibm_runtime import QiskitRuntimeService
QiskitRuntimeService.save_account(token="YOUR_TOKEN", channel="ibm_quantum_platform", set_as_default=True)
```

2. In the script: set `USE_HARDWARE=True` (optionally set `BACKEND_NAME`).
3. Run the script; results include real-device ISA depth.

## Configuration (edit in script)

* `N_QUBITS`, `DEPTHS`, `REPS_PER_DEPTH`, `SHOTS`
* Aer noise: `P1Q`, `P2Q`, `PREAD` (and `OPT_LEVEL_AER=0`)
* Hardware: `BACKEND_NAME`, `OPT_LEVEL_HW`

## Output

CSV columns: `backend, shots, depth, rep, raw_depth, isa_depth, p_success, fidelity_est, p1q, p2q, pread`.

## Plotting (example)

```python
import pandas as pd, matplotlib.pyplot as plt
df = pd.read_csv("fidelity_vs_depth.csv")
ax = df.groupby("isa_depth")["fidelity_est"].mean().plot(marker="o")
ax.set(xlabel="ISA depth", ylabel="Estimated fidelity")
plt.show()
```

## Notes

* The barrier between forward and inverse is crucial to avoid transpiler folding.
* On ideal simulators you’ll see \~1.0 at all depths; use noisy Aer or hardware to see decay.

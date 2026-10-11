# Qiskit Fall Fest 2026 — Project Submission Template

IIIT Kottayam Quantum Hackathon (24-Hour Sprint)

---

# Project Title

## Project Metadata

- **Project Title:** QAOA Channel Assignment for ESP32 Sensor Nodes
- **Team Name:** Joshua
- **Chosen Track:** Track 2: Open-Domain Optimization & Real-World Solvers
- **Video Presentation Link:** PASTE_YOUR_YOUTUBE_UNLISTED_LINK_HERE
- **Live Demo Timestamp in Video:** MM:SS

## Team Members (Max 4 members)

| Name | Email | GitHub Username |
|------|-------|-----------------|
| Joshua Praghajeeth A.P | apj.praghajeeth@gmail.com | @JOSHUAxJEETH |

---

## 1. Project Overview & Motivation

- **Summary (3–4 sentences):**
  We model a network of 6 ESP32 IoT sensor nodes as a weighted graph, where edge weights represent interference between nearby nodes. Splitting the nodes across two Wi-Fi channels so that interference between nodes on the same channel is minimised is the Max-Cut problem. We solve it with QAOA in Qiskit and compare it with brute force, a greedy heuristic and random assignment. We also study how QAOA depth and hardware noise affect solution quality.

- **Why this problem / game?**
  Channel assignment is a real wireless-network problem, and it maps directly onto Max-Cut, which is a standard QAOA benchmark. It lets us test how quantum optimisation behaves on a practical IoT scenario, and it is easy to explain: each node picks one of two channels, and we want nearby nodes on different channels.

---

## 2. Quantum Architecture & Mechanics

- **Core Quantum Principles Used:**
  Superposition (H gates on all qubits), entanglement through RZZ gates, interference through the QAOA cost and mixer layers, and measurement of the final state. The cost Hamiltonian is a sum of weighted ZZ terms, one per interference link.

- **How the Quantum Circuit is Integrated:**
  Each qubit is a node, and its measured bit is that node's channel. The cost layer applies an RZZ rotation scaled by each edge's weight. The mixer layer applies RX rotations. A classical COBYLA optimiser tunes the angles (gamma, beta) to minimise the energy, which is equivalent to maximising the expected cut. The most likely measured bitstring is taken as the channel assignment.

- **Circuit Details:**
  - **Number of Qubits:** 6
  - **Key Gates Used:** H, RZZ, RX, Measurement
  - **Primitives Used:** Qiskit `Statevector` for exact expectation values and `AerSimulator` (with a depolarizing noise model) for noisy sampling.
  - **Other Technologies Used:** NetworkX for the graph, SciPy (COBYLA) for optimisation, Matplotlib for plots.

```python
qc = QuantumCircuit(n); qc.h(range(n))
for k in range(p):
    for i, j, w in edges:
        qc.rzz(gamma[k] * w, i, j)   # cost layer
    qc.rx(2 * beta[k], range(n))     # mixer layer
```

---

## 3. Repository Structure & How to Run

### Project Structure

```
├── README.md               # Project documentation (this file)
├── requirements.txt        # Python dependencies
├── src/                    # Source code scripts (all code is in the notebook)
├── notebooks/              # Jupyter notebooks containing experiments
│   └── solution.ipynb      # Main runnable notebook with outputs
└── references/             # Hackathon reference guides (Track 1 & Track 2)
```

### Installation & Execution Instructions

> 💡 **Environment Recommendation:** To avoid package conflicts, we strongly suggest using a fresh Python virtual environment or Google Colab.

```bash
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

1. **Clone the repository:**
   ```bash
   git clone https://github.com/JOSHUAxJEETH/qiskit-esp32-channel-qaoa.git
   cd qiskit-esp32-channel-qaoa
   ```

2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Run the project / demo:**
   ```bash
   jupyter notebook notebooks/solution.ipynb
   ```
   (Or upload `notebooks/solution.ipynb` to Google Colab and choose Runtime → Run all.)

---

## 4. Implementation Walkthrough & Live Demo

- **Walkthrough of Gameplay / Optimization Pipeline:**
  1. Build a weighted interference graph of 6 ESP32 nodes with NetworkX.
  2. Compute the exact optimum by brute force (2^6 = 64 assignments), plus a greedy heuristic and a random baseline.
  3. Build the QAOA circuit for depth p and optimise gamma and beta with COBYLA over several random restarts.
  4. Take the most likely bitstring as the channel assignment and compare its cut value with the baselines.
  5. Sweep p = 1, 2, 3, then repeat with a depolarizing noise model to see how hardware noise affects solution quality.

- **Key Screenshots / Terminal Output:**

<img width="1120" height="483" alt="graph" src="https://github.com/user-attachments/assets/75e10e83-5bb4-4831-8eaf-ed1da0e52c96" /> 

<img width="1201" height="481" alt="graph 2" src="https://github.com/user-attachments/assets/806dd40a-38f8-46ff-9640-b1bcb9a53a10" /> 

<img width="709" height="491" alt="graph3" src="https://github.com/user-attachments/assets/01f45f4f-2224-4708-a468-a59400e16abb" />

---

## 5. Classical vs. Quantum Comparison

- **Classical Baseline / Counterpart:**
  Brute force (optimal cut = 17), greedy heuristic (cut = 17), and random assignment (average cut = 10.6).

- **Comparative Analysis:**
  The greedy heuristic matched the optimum on this small graph, so QAOA offers no advantage here, and we state that plainly. QAOA reached expected-cut approximation ratios of 0.81 to 0.83 (random assignment is 0.62), so it clearly beats random guessing. The optimal assignments were sampled with probability about 0.1 for p=1 and p=3, compared with about 0.03 for uniform random sampling (2 optimal assignments out of 64).

| Method | Cut value | Ratio (C / C_optimal) |
|--------|-----------|-----------------------|
| Brute force (optimal) | 17 | 1.00 |
| Greedy heuristic | 17 | 1.00 |
| Random assignment (avg) | 10.58 | 0.62 |
| QAOA p=1 (expected) | 14.08 | 0.828 |
| QAOA p=2 (expected) | 13.76 | 0.810 |
| QAOA p=3 (expected) | 14.10 | 0.829 |

---

## 6. Performance, Results & Limitations

- **Key Results & Observations:**
  QAOA approximation ratios were 0.828 (p=1), 0.810 (p=2) and 0.829 (p=3). In the noise experiment (re-optimised, so slightly different from the table above), p=3 fell from 0.889 with no noise to 0.757 at 2% two-qubit gate error, while p=1 moved only from 0.765 to 0.743. The advantage of the deeper circuit nearly disappears as noise grows, but both remain above the random baseline (0.62).

| Two-qubit gate error | 0 | 0.002 | 0.005 | 0.01 | 0.02 |
|----------------------|---|-------|-------|------|------|
| p=1 ratio | 0.765 | 0.764 | 0.763 | 0.757 | 0.743 |
| p=3 ratio | 0.889 | 0.873 | 0.849 | 0.816 | 0.757 |

- **Critical Reflection on Limitations:**
  The graph has only 6 nodes, and the weights are illustrative, not measured RSSI values from real ESP32 devices. The p=2 dip shows that COBYLA can get stuck in local minima, and results vary between optimisation runs. Deeper circuits are more sensitive to noise, so on real NISQ hardware the benefit of larger p would be limited. Larger graphs would need many more qubits and gates, and any real quantum advantage would have to be demonstrated at a scale where classical methods struggle, which this project does not claim.

---

## 7. References & Acknowledgments

- Open-source libraries: Qiskit, Qiskit Aer, NetworkX, SciPy, Matplotlib, NumPy.
- Farhi, Goldstone and Gutmann (2014), "A Quantum Approximate Optimization Algorithm".
- Qiskit Fall Fest 2026, IIIT Kottayam, for the hackathon template and guides.
  

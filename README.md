# Qiskit Fall Fest 2026 — Project Submission Template

IIIT Kottayam Quantum Hackathon (24-Hour Sprint)

---

# Project Title

## Project Metadata

- **Project Title:** QAOA Wi-Fi Channel Assignment from Real Raspberry Pi Scan Data
- **Team Name:** Joshua
- **Chosen Track:** Track 2: Open-Domain Optimization & Real-World Solvers
- **Video Presentation Link:** PASTE_YOUR_YOUTUBE_UNLISTED_LINK_HERE
- **Live Demo Timestamp in Video:** MM:SS

## Team Members 

| Name | Email | GitHub Username |
|------|-------|-----------------|
| Joshua Praghajeeth A.P | apj.praghajeeth@gmail.com | @JOSHUAxJEETH |

---

## 1. Project Overview & Motivation

- **Summary (3–4 sentences):**
  We scanned the real Wi-Fi environment of a hostel building with a Raspberry Pi 5 and turned the 6 strongest 2.4 GHz access points into a weighted interference graph. Splitting these access points across two channels so that same-channel interference is minimised is the Max-Cut problem, which we solve with QAOA in Qiskit. We add a load-balancing constraint (3 access points per channel) through a penalty term, and compare QAOA against brute force, a greedy heuristic and random assignment. We also study how QAOA depth, penalty strength and hardware noise affect solution quality.

- **Why this problem / game?**
  Wi-Fi channel assignment is a real, everyday resource-allocation problem: neighbouring access points on the same channel slow each other down. It maps directly onto a graph problem, and using measured scan data instead of invented numbers keeps the scenario grounded. The same formulation applies to IoT sensor nodes (such as ESP32 devices) that share a wireless band.

---

## 2. Quantum Architecture & Mechanics

- **Core Quantum Principles Used:**
  Superposition (H gates on all qubits), entanglement through RZZ gates, interference through the QAOA cost and mixer layers, and measurement of the final state. The cost Hamiltonian is a sum of weighted ZZ terms, one per pair of access points. For the balanced version, a penalty term lambda times (sum of x minus n/2) squared adds an extra ZZ interaction between every pair of qubits.

- **How the Quantum Circuit is Integrated:**
  Each qubit is an access point, and its measured bit is the channel group it is assigned to. The cost layer applies an RZZ rotation scaled by each pair's interference weight. The mixer layer applies RX rotations. A classical COBYLA optimiser tunes the angles (gamma, beta) to minimise the energy, which is equivalent to maximising the interference avoided. The most likely measured bitstring is the proposed channel assignment, and its cut value is checked against brute force.

- **Circuit Details:**
  - **Number of Qubits:** 6
  - **Key Gates Used:** H, RZZ, RX, Measurement
  - **Primitives Used:** Qiskit `Statevector` for exact expectation values and probabilities, and `AerSimulator` with a depolarizing noise model for noisy sampling.
  - **Other Technologies Used:** Raspberry Pi 5 and NetworkManager (`nmcli`) for Wi-Fi scanning, NetworkX for the graph, SciPy (COBYLA) for optimisation, Matplotlib for plots.

```python
qc = QuantumCircuit(n); qc.h(range(n))
for k in range(p):
    for i, j, w in edges:
        qc.rzz(gamma[k] * w, i, j)   # cost layer
    qc.rx(2 * beta[k], range(n))     # mixer layer
```

**Where the data comes from.** On a Raspberry Pi 5 we ran `nmcli -f BSSID,SSID,CHAN,SIGNAL dev wifi list --rescan yes` at five different positions in the building. Scans were merged by BSSID (each router's unique ID), keeping the strongest signal seen for each one. Ten distinct routers were found. Routers on the 5 GHz band cannot interfere with 2.4 GHz routers, so we kept the 6 routers on the 2.4 GHz band, labelled AP1 to AP6 (network names and addresses are not published). Personal phone hotspots were excluded. The interference weight between two access points is a proxy: 5 times the product of their two signal strengths (each as a fraction of 1).

| Access point | Current channel | Strongest signal seen (%) |
|--------------|-----------------|---------------------------|
| AP1 | 1 | 100 |
| AP2 | 11 | 100 |
| AP3 | 11 | 75 |
| AP4 | 6 | 52 |
| AP5 | 6 | 42 |
| AP6 | 6 | 39 |

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
   

---

## 4. Implementation Walkthrough & Live Demo

- **Walkthrough of Gameplay / Optimization Pipeline:**
  1. Scan the Wi-Fi environment with the Raspberry Pi 5 and keep the 6 strongest 2.4 GHz access points.
  2. Build a weighted interference graph with NetworkX (15 pairs, weights from signal strengths).
  3. Compute the exact optimum by brute force (2^6 = 64 assignments), plus a greedy heuristic and a random baseline.
  4. Build the QAOA circuit for depth p and optimise gamma and beta with COBYLA over several random restarts.
  5. Take the most likely bitstring as the channel assignment and compare its cut value with the baselines. Sweep p = 1, 2, 3.
  6. Repeat with a depolarizing noise model to see how hardware noise affects solution quality.
  7. Add a balance constraint (3 access points per channel) with a penalty term and measure the feasibility yield for several penalty strengths.

- **Key Screenshots / Terminal Output:**

<img width="1106" height="484" alt="Graph J 1" src="https://github.com/user-attachments/assets/ca59fe6a-b7a6-4144-bb92-87de035e84f9" />
<img width="1173" height="490" alt="Graph J 2" src="https://github.com/user-attachments/assets/bbf8dcb0-3613-498b-9a66-503d11a3ec1d" />
<img width="711" height="484" alt="Graph J 3" src="https://github.com/user-attachments/assets/b2e4354f-1367-4094-9546-fdc1eaa1e27b" />
<img width="761" height="565" alt="Graph J 4" src="https://github.com/user-attachments/assets/564885e9-fecb-4616-b392-f9a577807ac0" />
<img width="754" height="537" alt="Graph J 5" src="https://github.com/user-attachments/assets/895addfd-5ea2-44f1-a170-b414184b8e8d" />

---

## 5. Classical vs. Quantum Comparison

- **Classical Baseline / Counterpart:**
  Brute force (optimal cut = 20.8), greedy heuristic (cut = 20.75), and random assignment (average cut = 17.0).

- **Comparative Analysis:**
  The greedy heuristic is essentially optimal on this small graph (ratio 0.998), so QAOA offers no advantage here, and we state that plainly. Random assignment already reaches a ratio of 0.82 because the graph is dense, so the room for improvement is small. QAOA expected-cut ratios were 0.843 (p=1), 0.924 (p=2) and 0.929 (p=3). The probability of sampling an optimal assignment rose with depth: 0.025 (p=1), 0.063 (p=2) and 0.134 (p=3), compared with 0.031 for uniform random sampling (2 optimal assignments out of 64). So p=1 is no better than random at finding the optimum, while p=3 is about 4 times better. In terms of the application, the optimal two-channel plan leaves 12.9 units of same-channel interference out of 33.7 in total, versus 16.7 for a random split on average, a reduction of about 23%.

| Method | Cut value | Ratio (C / C_optimal) | P(sampling an optimal assignment) |
|--------|-----------|-----------------------|-----------------------------------|
| Brute force (optimal) | 20.8 | 1.000 | not applicable |
| Greedy heuristic | 20.75 | 0.998 | not applicable |
| Random assignment (avg) | 17.0 | 0.82 | 0.031 |
| QAOA p=1 (expected) | 17.53 | 0.843 | 0.025 |
| QAOA p=2 (expected) | 19.21 | 0.924 | 0.063 |
| QAOA p=3 (expected) | 19.32 | 0.929 | 0.134 |

---

## 6. Performance, Results & Limitations

- **Key Results & Observations:**
  **Depth:** deeper QAOA circuits gave better solutions, with ratios of 0.843, 0.924 and 0.929 for p = 1, 2, 3.

  **Noise:** with a depolarizing noise model (circuits re-optimised without noise, then sampled with 8000 shots), p=3 fell from 0.971 with no noise to 0.865 at 2% two-qubit gate error, while p=1 stayed almost flat (0.860 to 0.852). At 2% error the deeper circuit has lost most of its advantage and sits close to the random baseline (0.82). The noise-free p=3 value differs from the depth-sweep value because this run used different random restarts of the optimiser.

  **Balance constraint (3 vs 3):** the best balanced assignment has a cut of 20.75, almost the same as the unconstrained optimum of 20.8, so balancing costs very little interference. Averaged over 4 runs per penalty strength, the feasibility yield (share of QAOA samples that are balanced) was between 0.41 and 0.59, against 0.31 for random sampling. The probability of finding an optimal balanced assignment was 0.07 to 0.12, against 0.0625 for random sampling (4 optimal assignments out of 64), so QAOA is at best about 2 times better than chance. A larger penalty did not help: lambda=8 had the lowest yield (0.41).

| Penalty lambda | Feasibility yield (mean ± std) | Best-cut ratio | P(optimal balanced) |
|----------------|--------------------------------|----------------|---------------------|
| 0 | 0.52 ± 0.18 | 0.94 | 0.123 |
| 1 | 0.51 ± 0.16 | 0.98 | 0.067 |
| 2 | 0.59 ± 0.06 | 0.93 | 0.089 |
| 4 | 0.51 ± 0.04 | 0.98 | 0.100 |
| 8 | 0.41 ± 0.07 | 0.93 | 0.081 |

| Two-qubit gate error | 0 | 0.002 | 0.005 | 0.01 | 0.02 |
|----------------------|---|-------|-------|------|------|
| p=1 ratio | 0.860 | 0.859 | 0.860 | 0.857 | 0.852 |
| p=3 ratio | 0.971 | 0.955 | 0.932 | 0.910 | 0.865 |

- **Critical Reflection on Limitations:**
  The graph has only 6 nodes, and a classical greedy heuristic already solves it, so this project shows a working QAOA pipeline, not a quantum advantage. The pairwise weights are a proxy built from signal strengths seen from one location, not measured interference between access points, and the scan reports signal quality as a percentage, not in dBm. We use two channel groups, while real 2.4 GHz networks use three non-overlapping channels (1, 6 and 11), so a three-way version would be the natural next step. Results vary between optimiser runs because COBYLA can get stuck in local minima. Deeper circuits are more sensitive to noise, so on real NISQ hardware the benefit of larger p would be limited, and larger graphs would need many more qubits and gates. All results come from simulation, not from real quantum hardware.

---

## 7. References & Acknowledgments

- Open-source libraries: Qiskit, Qiskit Aer, NetworkX, SciPy, Matplotlib, NumPy.
- Farhi, Goldstone and Gutmann (2014), "A Quantum Approximate Optimization Algorithm".
- Qiskit Fall Fest 2026, IIIT Kottayam, for the hackathon template and guides.
- Starter structure adapted from the provided track notebook (B. Tirado, BasQ Qiskit Fall Fest 2026, CC BY 4.0 / Apache-2.0). The real-data graph, baselines, depth sweep, noise study and balance-constraint experiment are our own.

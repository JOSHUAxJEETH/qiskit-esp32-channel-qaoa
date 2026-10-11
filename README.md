# Qiskit Fall Fest 2026 — Project Submission Template
**IIIT Kottayam Quantum Hackathon (24-Hour Sprint)**

# Project Title

## Project Metadata

* **Project Title:** `[QAOA Channel Assignment for ESP32 Sensor Nodes]`
* **Team Name:** `[Joshua]`
* **Chosen Track:** `[Track 2: Open-Domain Optimization & Real-World Solvers]`
* **Video Presentation Link:** `[Insert YouTube Unlisted / Google Drive / Loom URL here]`
  *(⚠️ Crucial: Ensure link access is set to "Anyone with the link can view" on Google Drive or "Unlisted / Public" on YouTube. Locked/inaccessible links cannot be scored!)*
* **Live Demo Timestamp in Video:** `[MM:SS]` *(e.g., "Demo starts at 03:45")*

### Team Members (Max 4 members)
| Name | Email | GitHub Username |
| :--- | :--- | :--- |
| `[Joshua Praghajeeth A.P]` | `[apj.praghajeeth@gmail.com]` | `[JOSHUAxJEETH]` |


---

## 1. Project Overview & Motivation

* **Summary:**  
  `[We model a network of 6 ESP32 IoT sensor nodes as a weighted graph, where edge weights represent interference between nearby nodes. Splitting the nodes across two Wi-Fi channels so that interference between nodes on the same channel is minimised is the Max-Cut problem. We solve it with QAOA in Qiskit and compare it with brute force, a greedy heuristic and random assignment.]`

* **Why this problem / game?**  
  `[Channel assignment is a real wireless-network problem, and it maps directly onto Max-Cut, which is a standard QAOA benchmark. It lets us test how quantum optimisation behaves on a practical IoT scenario.]`

---

## 2. Quantum Architecture & Mechanics

* **Core Quantum Principles Used:**  
  `[Superposition (H gates on all qubits), entanglement through RZZ gates, interference through the QAOA cost and mixer layers, and measurement of the final state. The cost Hamiltonian is a sum of weighted ZZ terms.]`

* **How the Quantum Circuit is Integrated:**  
  `[Each qubit is a node, and its measured bit is that node’s channel. The cost layer applies an RZZ rotation scaled by each edge’s weight. The mixer layer applies RX rotations. A classical COBYLA optimiser tunes the angles to minimise the energy, which is the expected cut.]`

* **Circuit Details:**  
  * **Number of Qubits:** `[6]`
  * **Key Gates Used:** `[H, RZZ, RX, Measurement]`
  * **Primitives Used:** `[Qiskit Statevector for exact expectation values and AerSimulator for noisy sampling.]`
  * **Other Technologies Used:** `[NetworkX for the graph, SciPy for optimisation, Matplotlib for plots.]`

```python
# Optional: Paste a representative snippet of your core Qiskit circuit code here
```

---

## 3. Repository Structure & How to Run

### Project Structure
```text
├── README.md               # Project documentation (this file)
├── requirements.txt        # Python dependencies
├── src/                    # Source code scripts or game application
│   └── main.py             # Main entry point (or streamlit app)
├── notebooks/              # Jupyter notebooks containing experiments
│   └── solution.ipynb      # Main runnable notebook with outputs
└── references/             # Hackathon reference guides (Track 1 & Track 2)
```

## 4. Implementation Walkthrough & Live Demo

* **Walkthrough of Gameplay / Optimization Pipeline:**  
  `[Build the graph, then run QAOA with depth p, optimise the parameters over several random restarts, take the most likely bitstring as the channel assignment, and compare its cut value with the classical baselines.]`
  
* **Key Screenshots / Terminal Output:**  
  `[<img width="1120" height="483" alt="graph" src="https://github.com/user-attachments/assets/75e10e83-5bb4-4831-8eaf-ed1da0e52c96" />
  <img width="1201" height="481" alt="graph 2" src="https://github.com/user-attachments/assets/806dd40a-38f8-46ff-9640-b1bcb9a53a10" />
   <img width="709" height="491" alt="graph3" src="https://github.com/user-attachments/assets/01f45f4f-2224-4708-a468-a59400e16abb" />]`

---

## 5. Classical vs. Quantum Comparison

* **Classical Baseline / Counterpart:**  
  `[Brute force (optimal, 17), greedy heuristic (17), random assignment (average 10.6).]`

* **Comparative Analysis:**  
  `[The greedy heuristic matched the optimum on this small graph, so QAOA offers no advantage here. QAOA reached expected cut ratios of 0.81 to 0.83 (random is 0.62). The optimal assignments were sampled with probability about 0.1 for p=1 and p=3, compared with about 0.03 for random sampling.]`
  
---

## 6. Performance, Results & Limitations

* **Key Results & Observations:**  
  `[p=1 ratio 0.828, p=2 0.810, p=3 0.829. Noise-free p=3 beats p=1 in the noise run, but the gap closes as gate error approaches 2%.]`

* **Critical Reflection on Limitations:**  
  `[The graph has only 6 nodes, and the weights are illustrative, not measured RSSI values. The p=2 dip shows COBYLA can get stuck in local minima. Deeper circuits are more sensitive to noise, so on real NISQ hardware the benefit of larger p would be limited. Larger graphs would need many more qubits and gates, and any real advantage would have to be shown at scale.]`

---

## 7. References & Acknowledgments
Qiskit, NetworkX, SciPy, Matplotlib, Qiskit Fall Fest 2026 (IIIT Kottayam), Farhi et al. (2014) QAOA paper.

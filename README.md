🛡️ BGP Security with RPKI Validator: Mitigating Internet Routing Hijacks
This project implements and demonstrates Route Origin Validation (ROV), a security mechanism that uses cryptography to defend the internet's core routing protocol (BGP) against threats like BGP Hijacking.

1. Project Overview and Significance (Paragraphs)
The global network protocol, BGP (Border Gateway Protocol), is built on trust and is fundamentally vulnerable to BGP Hijacking. Our project introduces cryptographic proof (RPKI) into the routing process.

We built a hybrid system that detects when an incoming route is INVALID (lacks authorization) via a Python UI and then enforces a policy on simulated FRR routers to reject that specific malicious route, ensuring our network only forwards traffic based on verifiable, cryptographically secure information.

2. Project Components
The solution is split into two primary, interconnected environments:

A. I. Detection Core (GUI)
Role: Cryptographic Validation and Visualization (confirming VALID / INVALID status).

Technology Used: Python 3, Tkinter (GUI), requests (Simulated API Logic).

B. II. Enforcement Core (FRR)
Role: Network Security Policy Enforcement (blocking the rejected route).

Technology Used: Linux (VMs), FRRouting (FRR), BGP Prefix-List Filtering (Workaround for RPKI rejection).

3. Demonstration & Mitigation Proof
We prove the mitigation by simulating a hijacking of the globally known 8.8.8.0/24 prefix.

Test Case: Attempting to announce 8.8.8.0/24 with an unauthorized AS (65535).

Expected Result: The system returns ❌ INVALID, and the router must REJECT the route.

4. Setup and Run Instructions (Minimal Commands)
A. Python GUI (Detection Core)
Environment Setup:

Bash

python3 -m venv venv
source venv/bin/activate  # or .\venv\Scripts\activate on Windows
pip install requests
Run Application:

Bash

python ui_validator.py
B. Network Enforcement (FRR Simulation)
To reproduce the mitigation proof, you must apply the full configuration files located in the FRR_Configuration folder to the respective VMs.

Configuration Files:

VM-A (Policy Enforcer): Uses frr_config_vm_a.txt (Contains the prefix list filter).

VM-B (Malicious Peer): Uses frr_config_vm_b.txt (Configures AS 65535 to announce the hijack route).

Verification: On Router A's vtysh shell:

Check Rejection: Run show ip bgp 8.8.8.0/24.

The output must show: % Network not in table (Proof of successful security filtering).

5. Repository Structure and Author
Repository Structure:

RPKI_Validator_GUI/ui_validator.py: Python script containing the GUI and simulated RPKI logic.

FRR_Configuration/: Contains router configuration files.

Demonstration_Proofs/: Final snapshots proving RPKI detection and network rejection.

Project_Report.pdf: The final academic submission document.

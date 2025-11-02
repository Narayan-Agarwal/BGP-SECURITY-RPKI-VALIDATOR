🛡️ BGP Security with RPKI Validator: Mitigating Internet Routing Hijacks
1. Project Overview and Significance
This project implements and demonstrates Route Origin Validation (ROV), a critical security mechanism designed to defend the internet's core routing protocol, BGP, against threats like BGP Hijacking. BGP is fundamentally vulnerable because it operates on trust, allowing unauthorized parties to announce routes and redirect global traffic. Our project solves this by introducing cryptographic proof (RPKI) into the routing process. We built a hybrid system: a Python UI that detects when a route is cryptographically INVALID, and simulated FRR routers that enforce a policy to reject that specific malicious route, ensuring our network only forwards traffic based on verifiable, cryptographically secure information.

2. Project Components and Technology
The solution is split into two primary, interconnected environments:

A. I. Detection Core (GUI)
This component handles Cryptographic Validation and Visualization. It simulates checking a route's Origin AS against the global RPKI cache, confirming VALID / INVALID status. It utilizes Python 3, the Tkinter (GUI) library, and requests for the simulated API logic.

B. II. Enforcement Core (FRR)
This component handles Network Security Policy Enforcement, blocking the rejected route. It utilizes Linux (VMs) running FRRouting (FRR), where BGP is configured with a Prefix-List Filtering workaround to simulate the action of an RPKI rejection.

3. Demonstration & Mitigation Proof
We prove the mitigation by simulating a hijacking of the globally known 8.8.8.0/24 prefix. Specifically, we test the scenario where an unauthorized AS (65535) attempts to announce this prefix. Our system detects this as ❌ INVALID, and the router successfully REJECTS the route. This action prevents the attack.

4. Setup and Run Instructions
A. Python GUI (Detection Core)
To set up and run the detection core, execute the following commands:

Bash

python3 -m venv venv
source venv/bin/activate  # Use .\venv\Scripts\activate on Windows
pip install requests
python ui_validator.py
B. Network Enforcement (FRR Simulation)
To reproduce the mitigation proof, apply the full configuration files located in the FRR_Configuration directory to the respective Linux VMs. On Router A's vtysh shell, verification is confirmed by running show ip bgp 8.8.8.0/24. The output must show: % Network not in table (Proof of successful security filtering).

5. Repository Structure and Author
The repository includes the necessary files for full reproduction: RPKI_Validator_GUI/ui_validator.py (Python logic), the configuration files within the FRR_Configuration/ directory, the Demonstration_Proofs/ (final snapshots), and the final Project_Report.pdf.

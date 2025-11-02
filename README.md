BGP Security with RPKI Validator: Mitigating Internet Routing Hijacks
This project implements and demonstrates Route Origin Validation (ROV), a security mechanism that uses cryptography to defend the internet's core routing protocol (BGP) against threats like BGP Hijacking.

1. Project Overview and Significance
The global network protocol, BGP (Border Gateway Protocol), is built on trust and is fundamentally vulnerable to BGP Hijacking, where unauthorized parties announce routes to steal or drop traffic. Our project solves this by introducing cryptographic proof (RPKI) into the routing process.

We built a hybrid system that checks every incoming route announcement (Prefix + Origin AS) against a trusted, digitally signed database (the RPKI cache). Our Python UI detects when a route is INVALID because it lacks cryptographic authorization, and our simulated FRR router then immediately enforces a policy to reject that specific malicious route, ensuring our network only forwards traffic based on verifiable, cryptographically secure information.

2. Project Components
The solution is split into two primary, interconnected environments:

A. I. Detection Core (GUI)
Role: Cryptographic Validation and Visualization. Simulates checking a route's Origin AS against the global RPKI cache, confirming VALID / INVALID status.

Technology Used: Python 3, Tkinter (GUI), requests (Simulated API Logic).

B. II. Enforcement Core (FRR)
Role: Network Security Policy Enforcement. Simulates BGP peering, receives updates, and applies a filtering policy to block the rejected route.

Technology Used: Linux (VMs), FRRouting (FRR), BGP Prefix-List Filtering (Workaround for RPKI rejection).

3. Demonstration & Mitigation Proof
We prove the mitigation by simulating a hijacking of the globally known 8.8.8.0/24 prefix.

Legitimate Route Test:

Input: Prefix: 8.8.8.0/24, Origin AS: 15169 (Authorized)

Expected Status: ✅ VALID

Router Action: Accepted and installed.

Hijack Attempt Test:

Input: Prefix: 8.8.8.0/24, Origin AS: 65535 (Unauthorized)

Expected Status: ❌ INVALID

Router Action: Rejected by the inbound BGP filter, preventing the attack.

4. Setup and Run Instructions
A. Python GUI (Detection Core)
Environment Setup:

Bash

python3 -m venv venv
source venv/bin/activate  # Use .\venv\Scripts\activate on Windows
pip install requests
Run Application:

Bash

python ui_validator.py
B. Network Enforcement (FRR Simulation)
This component requires two Linux Virtual Machines (VM-A: Enforcer, VM-B: Attacker) with FRR installed.

Configure Router A (Policy Enforcer - AS64512):

Configuration: Apply the following commands into the vtysh shell of VM-A. This sets up the Prefix List filter that blocks the attack prefix.

Bash

configure terminal
no router bgp 65535
ip prefix-list BLOCK_HIJACK seq 10 deny 8.8.8.0/24
ip prefix-list BLOCK_HIJACK seq 20 permit 0.0.0.0/0 le 32
router bgp 64512
 bgp router-id 10.0.0.2
 neighbor 10.0.0.1 remote-as 65535
 address-family ipv4 unicast
  neighbor 10.0.0.1 prefix-list BLOCK_HIJACK in
 exit-address-family
end
write memory
Configure Router B (Malicious Peer - AS65535):

Configuration: Apply the following commands into the vtysh shell of VM-B. This announces the malicious route.

Bash

configure terminal
no router bgp 15169
router bgp 65535
 bgp router-id 10.0.0.1
 neighbor 10.0.0.2 remote-as 64512 
 address-family ipv4 unicast
  network 8.8.8.0/24
 exit-address-family
end
write memory
Verification: On Router A's vtysh shell:

Check Rejection: Run show ip bgp 8.8.8.0/24. The successful rejection is confirmed by the output: % Network not in table.

5. Repository Structure and Author
Repository Structure:

RPKI_Validator_GUI/ui_validator.py: Python script containing the GUI and simulated RPKI logic.

FRR_Configuration/: Contains router configuration files (for reference).

Demonstration_Proofs/: Final snapshots proving RPKI detection and network rejection.

Project_Report.pdf: The final academic submission document.

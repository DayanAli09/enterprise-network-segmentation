# Secure Enterprise Network (VLAN Segmentation + ACL + Port Security)

## Overview
This project simulates a segmented enterprise network built in Cisco Packet Tracer. It demonstrates practical implementation of VLAN segmentation, inter-VLAN routing (Router-on-a-Stick), least-privilege access control using ACLs, and Layer 2 port security to prevent unauthorized device access.

The goal was to design a network the way a real organization would — isolating departments, restricting sensitive traffic, and hardening access points — combining CCNA-level networking fundamentals with a security-first mindset.

## Architecture

<img width="919" height="601" alt="image" src="https://github.com/user-attachments/assets/2f242f07-aec7-4076-b370-9ef26664c620" />


**Devices:**
- 1 Router (Router-on-a-Stick configuration)
- 1 Layer 2 Switch
- 4 PCs (HR, Finance, IT, Guest)
- 1 Server (IT/Admin segment)

**VLAN Design:**

| Department | VLAN ID | Subnet | Gateway |
|---|---|---|---|
| HR | 10 | 192.168.10.0/24 | 192.168.10.1 |
| Finance | 20 | 192.168.20.0/24 | 192.168.20.1 |
| IT/Admin | 30 | 192.168.30.0/24 | 192.168.30.1 |
| Guest | 40 | 192.168.40.0/24 | 192.168.40.1 |

## Design Decisions

### VLAN Segmentation
Each department sits in its own broadcast domain. This limits lateral movement — if one device or segment is compromised, the blast radius stays contained instead of spreading across the whole network.

### Router-on-a-Stick
A single trunk link between the router and switch carries all VLAN traffic, routed through sub-interfaces (Gig0/0.10, .20, .30, .40). This keeps the design cost-efficient while still scalable.

### ACL-Based Access Control (Least Privilege)
- **Guest VLAN** — fully isolated from HR, Finance, and IT. Guests should never reach internal resources.
- **HR VLAN** — blocked from Finance, since HR has no legitimate need to access financial data.
- **IT/Admin VLAN** — full access across segments, required for network management and troubleshooting.

This mirrors the least-privilege principle: every segment only has the access it actually needs, nothing more.

### Port Security
Access ports are locked to a single MAC address (sticky learning) with a shutdown violation policy. This mitigates basic Layer 2 attacks such as MAC flooding or a rogue device being plugged into an access port.

## Configuration Reference

Full device configurations are available in [`configs/`](configs/):
- [`switch0-running-config.txt`](configs/switch0-running-config.txt)
- [`router0-running-config.txt`](configs/router0-running-config.txt)

Key verification commands used:
- `show vlan brief`
- `show interfaces trunk`
- `show ip interface brief`
- `show access-lists`
- `show port-security interface <port>`

## Testing & Verification

Connectivity was tested from each VLAN to confirm ACL enforcement matched the intended design:

| From | To | Expected | Result | Screenshot |
|---|---|---|---|---|
| HR | IT | Allowed | ✅ Success | <img width="615" height="304" alt="image" src="https://github.com/user-attachments/assets/45f591f6-f989-4cdb-b24d-f80c87c308fd" />
 |
| HR | Finance | Blocked | ❌ Timeout | <img width="618" height="264" alt="image" src="https://github.com/user-attachments/assets/1bcdaf71-8a4d-4814-bba9-ed9fdb89a8a3" />
 |
| Guest | HR | Blocked | ❌ Timeout | <img width="636" height="292" alt="image" src="https://github.com/user-attachments/assets/67ac15dd-865d-4312-b633-32fb8d4685b4" />
 |
| Guest | Finance | Blocked | ❌ Timeout | <img width="629" height="273" alt="image" src="https://github.com/user-attachments/assets/1f0a6e82-c41d-4f43-abe5-417b3e23609c" />
 |
| IT | Finance | Allowed | ✅ Success | <img width="608" height="316" alt="image" src="https://github.com/user-attachments/assets/c198a572-9b49-4ce7-866a-dfb9d72227da" />
 |

Port security was also verified by attempting to connect an unauthorized device to a locked access port, triggering an err-disabled state:

<img width="697" height="176" alt="image" src="https://github.com/user-attachments/assets/98b4f6fa-b106-44b7-b489-b835a8788b38" />


## Skills Demonstrated
- VLAN design and configuration
- Inter-VLAN routing (Router-on-a-Stick)
- Access Control Lists (standard & extended) applying least-privilege
- Layer 2 port security / MAC-based access control
- Network documentation and security rationale

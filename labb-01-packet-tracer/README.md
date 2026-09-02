# GNI102 – Lab 1: Introduction to Cisco Packet Tracer

This laboratory assignment introduces fundamental network simulation in Cisco Packet Tracer. You will assemble a switched and routed topology, configure host addressing, establish IP routing parameters, and apply basic Cisco IOS hardening techniques.

---

## Prerequisites

Before starting the lab, complete the following initial steps:

1. **Workstation Access:**
   - Log in to the host machine using the following local credentials:
     - **Username:** `cisco`
     - **Password:** `cisco`

2. **Canvas Profile Verification:**
   - Confirm that your personal photo is uploaded to [hv.instructure.com](https://hv.instructure.com/).
   - *Note: This will be checked by the instructor during final sign-off.*

3. **Packet Tracer Authentication:**
   - Launch **Cisco Packet Tracer**.
   - Authenticate with your **Cisco Networking Academy** credentials.

---

## 1. Network Topology

Build and cable the network topology according to the diagram and specifications below.

![Network Topology](images/topology.png)

### Equipment Selection
Locate and place the following hardware components from the bottom-left device tray:

* **Router:**
  - `1×` **Cisco 2811 Integrated Services Router** (place centered at the top)
* **Switches:**
  - `2×` **Cisco Catalyst 2960-24TT** (`Switch-Left` and `Switch-Right`)
* **End Devices:**
  - `2×` **PC-PT** (`PC-Left` and `PC-Right`)
  - `2×` **Server-PT** (`Server-Left` and `Server-Right`)

### Cabling Specifications
Connect the devices using standard **Copper Straight-Through** patch cables:

| Source Device | Source Interface | Destination Device | Destination Interface |
| :--- | :--- | :--- | :--- |
| `PC-Left` | `FastEthernet0` | `Switch-Left` | `FastEthernet0/1` |
| `Server-Left` | `FastEthernet0` | `Switch-Left` | `FastEthernet0/2` |
| `PC-Right` | `FastEthernet0` | `Switch-Right` | `FastEthernet0/1` |
| `Server-Right` | `FastEthernet0` | `Switch-Right` | `FastEthernet0/2` |
| `Switch-Left` | `FastEthernet0/24` | `MainRouter` | `FastEthernet0/0` |
| `Switch-Right` | `FastEthernet0/24` | `MainRouter` | `FastEthernet0/1` |

---

## 2. Basic Device Configuration (GUI)

Apply the following network parameters using the **Config** tab on each respective device.

### Addressing & Naming Table

| Device | Display Name | Hostname | Interface | IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Router** | `MainRouter` | `MainRouter` | `Fa0/0` | `192.168.1.1` | `255.255.255.0` | *N/A* |
| | | | `Fa0/1` | `192.168.2.1` | `255.255.255.0` | *N/A* |
| **Switch Left** | `Switch-Left` | `Switch-Left` | *N/A* | *N/A* | *N/A* | *N/A* |
| **Switch Right** | `Switch-Right` | `Switch-Right` | *N/A* | *N/A* | *N/A* | *N/A* |
| **PC Left** | `PC-Left` | — | `Fa0` | `192.168.1.10` | `255.255.255.0` | `192.168.1.1` |
| **Server Left** | `Server-Left` | — | `Fa0` | `192.168.1.100` | `255.255.255.0` | `192.168.1.1` |
| **PC Right** | `PC-Right` | — | `Fa0` | `192.168.2.10` | `255.255.255.0` | `192.168.2.1` |
| **Server Right**| `Server-Right`| — | `Fa0` | `192.168.2.100` | `255.255.255.0` | `192.168.2.1` |

### Step-by-Step GUI Instructions

#### Router Configuration:
1. Click `MainRouter` and open the **Config** tab.
2. Under **Global Settings**, set both **Display Name** and **Hostname** to:
   ```text
   MainRouter
   Under INTERFACE, select FastEthernet0/0:

IP Configuration: 192.168.1.1

Subnet Mask: 255.255.255.0

Port Status: Check the On box.

Under INTERFACE, select FastEthernet0/1:

IP Configuration: 192.168.2.1

Subnet Mask: 255.255.255.0

Port Status: Check the On box.

Switches Configuration:
Click Switch-Left → Config tab → set Display Name and Hostname to:

Plaintext


Switch-Left
Click Switch-Right → Config tab → set Display Name and Hostname to:

Plaintext


Switch-Right
Hosts (PC & Server) Configuration:
Click the device and select the Config tab.

Under Global Settings, configure:

Display Name: Enter host name (e.g. PC-Left, Server-Right)

Gateway IPv4: Enter the corresponding router interface IP (192.168.1.1 or 192.168.2.1).

Under INTERFACE, select FastEthernet0:

IP Configuration: Enter the host IP address from the table above.

Subnet Mask: 255.255.255.0

Port Status: Verify that On is checked.

3. Connectivity Verification
Verify end-to-end IP communication across the router:

A. ICMP Verification (Ping)
Click PC-Left → select the Desktop tab → open Command Prompt.

Ping the server situated on the remote subnet:

DOS


ping 192.168.2.100
Note: The first ICMP echo request may time out during initial ARP resolution. Run the command a second time to ensure four successful replies (0% packet loss).

B. HTTP Application Verification
On PC-Left, close the Command Prompt and open Web Browser from the Desktop tab.

In the URL field, navigate to:

Plaintext


[http://192.168.2.100/](http://192.168.2.100/)
Confirm that the default Cisco Packet Tracer web page loads successfully.

4. Security & Device Hardening (CLI)
Perform device security configurations directly in the Cisco IOS Command Line Interface (CLI tab).

A. Console & Privileged EXEC Passwords (Router)
Open the CLI tab on MainRouter and enter Privileged EXEC mode:

Kodavsnitt


enable
configure terminal
Secure physical console line access:

Kodavsnitt


line console 0
 password cisco
 login
 exit
Secure privileged EXEC access:

Kodavsnitt


enable password secure
exit
Test the credentials:

Execute exit until returned to the initial login prompt (MainRouter con0 is now available).

Press Enter, authenticate using console password: cisco.

Issue enable, authenticate using privileged password: secure.

B. Switch Hardening
Repeat the password configuration on both Switch-Left and Switch-Right:

Kodavsnitt


enable
configure terminal
line console 0
 password cisco
 login
 exit
enable password secure
exit
C. System Clock Configuration
Manually set the hardware clock in Privileged EXEC mode (#) on all three devices. Use the context-sensitive help (?) to verify parameter formatting:

Kodavsnitt


clock set hh:mm:ss DD Month YYYY
Example command:

Kodavsnitt


clock set 10:15:00 2 September 2026
Verify that the updated timestamp is registered:

Kodavsnitt


show clock
D. Password Encryption Service
By default, Cisco IOS stores simple passwords in cleartext within the active configuration. Enable service encryption on all devices:

Kodavsnitt


configure terminal
service password-encryption
exit
Verify that plain-text passwords have been replaced with type-7 hashes:

Kodavsnitt


show running-config
E. Message-of-the-Day (MOTD) Banner
Configure a legal warning banner displayed prior to user authentication:

Kodavsnitt


configure terminal
banner motd # Authorized Access Only! Violators will be prosecuted. #
exit
F. Interface Documentation (Descriptions)
Assign descriptive labels identifying connected endpoints on all active interfaces.

MainRouter:
Kodavsnitt


configure terminal
interface FastEthernet 0/0
 description Link to Switch-Left
interface FastEthernet 0/1
 description Link to Switch-Right
exit
Switch-Left:
Kodavsnitt


configure terminal
interface FastEthernet 0/1
 description Link to PC-Left
interface FastEthernet 0/2
 description Link to Server-Left
interface FastEthernet 0/24
 description Uplink to MainRouter Fa0/0
exit
Switch-Right:
Kodavsnitt


configure terminal
interface FastEthernet 0/1
 description Link to PC-Right
interface FastEthernet 0/2
 description Link to Server-Right
interface FastEthernet 0/24
 description Uplink to MainRouter Fa0/1
exit
G. Save Active Configuration
Commit the running configuration from volatile RAM to non-volatile memory (NVRAM) across all three network devices:

Kodavsnitt


copy running-config startup-config
(Press Enter to confirm the default destination filename [startup-config]).

Verify that the configuration is written to storage:

Kodavsnitt


show startup-config
5. Lab Questions
Document the answers to the following questions as part of your lab review:

IOS Image Filename:

What is the exact image file name of the operating system running on MainRouter?

Command clue:

Kodavsnitt


show version
Hardware MAC Address:

What MAC address is assigned to the router's FastEthernet0/0 interface?

Command clue:

Kodavsnitt


show interfaces FastEthernet 0/0
Flash Memory Size:

How large is the router's IOS binary image file, specified in bytes?

Command clue:

Kodavsnitt


show flash:
6. Verification & Sign-off Checklist
Before requesting evaluation, ensure that all criteria are met:

[ ] Canvas profile picture is verified (hv.instructure.com).

[ ] End-to-end HTTP access from PC-Left to http://192.168.2.100/ succeeds.

[ ] Console login is protected by password cisco.

[ ] Privileged EXEC mode (enable) is protected by password secure.

[ ] System clock is set and verified via show clock on all network devices.

[ ] service password-encryption is active (no cleartext passwords in show run).

[ ] All 8 operational interfaces contain descriptive description tags.

[ ] Configurations are permanently saved using copy running-config startup-config.

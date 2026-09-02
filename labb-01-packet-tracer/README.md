# GNI102 – Lab 1: Introduction to Cisco Packet Tracer

This laboratory assignment introduces fundamental network simulation in Cisco Packet Tracer. You will assemble a switched and routed topology, configure host addressing, establish IP routing parameters, and apply basic Cisco IOS hardening techniques.

---

## Prerequisites

Before starting the lab, complete the following initial steps:

* **Workstation Access:**
  * Log in to the host machine using the following local credentials:
    * **Username:** `cisco`
    * **Password:** `cisco`
* **Canvas Profile Verification:**
  * Confirm that your personal photo is uploaded to [hv.instructure.com](https://hv.instructure.com/).
  * *Note: This will be checked by the instructor during final sign-off.*
* **Packet Tracer Authentication:**
  * Launch **Cisco Packet Tracer**.
  * Authenticate with your **Cisco Networking Academy** credentials.

---

## 1. Network Topology

Build and cable the network topology according to the diagram and specifications below.

![Network Topology](images/topology.png)

### Equipment Selection

Locate and place the following hardware components from the bottom-left device tray:

* **Router:**
  * `1x` Cisco 2811 Integrated Services Router (place centered at the top)
* **Switches:**
  * `2x` Cisco Catalyst 2960-24TT (`Switch-Left` and `Switch-Right`)
* **End Devices:**
  * `2x` PC-PT (`PC-Left` and `PC-Right`)
  * `2x` Server-PT (`Server-Left` and `Server-Right`)

### Cabling Specifications

Connect the devices using standard Copper Straight-Through patch cables:

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
| **Server Right** | `Server-Right` | — | `Fa0` | `192.168.2.100` | `255.255.255.0` | `192.168.2.1` |

### Step-by-Step GUI Instructions

#### Router Configuration
1. Click **MainRouter** and select the **Config** tab.
2. Under **Global Settings**, configure:
   * **Display Name:** `MainRouter`
   * **Hostname:** `MainRouter`
3. Under **INTERFACE**, select `FastEthernet0/0`:
   * **IP Configuration:** `192.168.1.1`
   * **Subnet Mask:** `255.255.255.0`
   * **Port Status:** Check the **On** box.
4. Under **INTERFACE**, select `FastEthernet0/1`:
   * **IP Configuration:** `192.168.2.1`
   * **Subnet Mask:** `255.255.255.0`
   * **Port Status:** Check the **On** box.

#### Switches Configuration
* Click **Switch-Left** → **Config** tab → set **Display Name** and **Hostname** to: `Switch-Left`
* Click **Switch-Right** → **Config** tab → set **Display Name** and **Hostname** to: `Switch-Right`

#### Hosts (PC & Server) Configuration
1. Click the device and select the **Config** tab.
2. Under **Global Settings**, configure:
   * **Display Name:** Enter host name (e.g., `PC-Left`, `Server-Right`).
   * **Gateway IPv4:** Enter the corresponding router interface IP (`192.168.1.1` or `192.168.2.1`).
3. Under **INTERFACE**, select `FastEthernet0`:
   * **IP Configuration:** Enter the host IP address from the addressing table.
   * **Subnet Mask:** `255.255.255.0`
   * **Port Status:** Verify that **On** is checked.

---

## 3. Connectivity Verification

Verify end-to-end IP communication across the router:

### A. ICMP Verification (Ping)
1. Click **PC-Left** → select the **Desktop** tab → open **Command Prompt**.
2. Ping the server situated on the remote subnet:
   ```cmd
   ping 192.168.2.100
   ```
   > **Note:** The first ICMP echo request may time out during initial ARP resolution. Run the command a second time to ensure four successful replies (0% packet loss).

### B. HTTP Application Verification
1. On **PC-Left**, close the Command Prompt and open **Web Browser** from the **Desktop** tab.
2. In the URL field, navigate to: `http://192.168.2.100/`
3. Confirm that the default Cisco Packet Tracer web page loads successfully.

---

## 4. Security & Device Hardening (CLI)

Perform device security configurations directly in the Cisco IOS Command Line Interface (**CLI** tab).

### A. Console & Privileged EXEC Passwords (Router)

1. Open the **CLI** tab on **MainRouter** and enter privileged configuration mode:
   ```ios
   enable
   configure terminal
   ```
2. Secure physical console line access:
   ```ios
   line console 0
    password cisco
    login
    exit
   ```
3. Secure privileged EXEC access:
   ```ios
   enable password secure
   exit
   ```
4. **Test the credentials:**
   * Execute `exit` repeatedly until returned to the initial login prompt (`MainRouter con0 is now available`).
   * Press `Enter` and authenticate using console password: `cisco`.
   * Issue `enable` and authenticate using privileged password: `secure`.

---

### B. Switch Hardening

Repeat the password configuration on both **Switch-Left** and **Switch-Right**:

```ios
enable
configure terminal
line console 0
 password cisco
 login
 exit
enable password secure
exit
```

---

### C. System Clock Configuration

Manually set the hardware clock in Privileged EXEC mode (`#`) on all three devices. Use the context-sensitive help (`?`) to verify parameter formatting:

```ios
clock set hh:mm:ss DD Month YYYY
```

*Example command:*
```ios
clock set 10:15:00 2 September 2026
```

Verify that the updated timestamp is registered:
```ios
show clock
```

---

### D. Password Encryption Service

By default, Cisco IOS stores simple passwords in cleartext within the active configuration. Enable service encryption on all devices:

```ios
configure terminal
service password-encryption
exit
```

Verify that plain-text passwords have been replaced with type-7 hashes:
```ios
show running-config
```

---

### E. Message-of-the-Day (MOTD) Banner

Configure a legal warning banner displayed prior to user authentication:

```ios
configure terminal
banner motd # Authorized Access Only! Violators will be prosecuted. #
exit
```

---

### F. Interface Documentation (Descriptions)

Assign descriptive labels identifying connected endpoints on all active interfaces.

#### MainRouter:
```ios
configure terminal
interface FastEthernet 0/0
 description Link to Switch-Left
interface FastEthernet 0/1
 description Link to Switch-Right
exit
```

#### Switch-Left:
```ios
configure terminal
interface FastEthernet 0/1
 description Link to PC-Left
interface FastEthernet 0/2
 description Link to Server-Left
interface FastEthernet 0/24
 description Uplink to MainRouter Fa0/0
exit
```

#### Switch-Right:
```ios
configure terminal
interface FastEthernet 0/1
 description Link to PC-Right
interface FastEthernet 0/2
 description Link to Server-Right
interface FastEthernet 0/24
 description Uplink to MainRouter Fa0/1
exit
```

---

### G. Save Active Configuration

Commit the running configuration from volatile RAM to non-volatile memory (NVRAM) across all three network devices:

```ios
copy running-config startup-config
```
*(Press `Enter` to confirm the default destination filename `[startup-config]`)*.

Verify that the configuration is written to storage:
```ios
show startup-config
```

---

## 5. Lab Questions

Document the answers to the following questions as part of your lab review[cite: 1]:

1. **IOS Image Filename:**  
   What is the exact image file name of the operating system running on `MainRouter`?[cite: 1]  
   *Command clue:*
   ```ios
   show version
   ```
   *Answer:*  
   ______________________________________________________________________  
   ______________________________________________________________________

<br>

2. **Hardware MAC Address:**  
   What MAC address is assigned to the router's `FastEthernet0/0` interface?[cite: 1]  
   *Command clue:*
   ```ios
   show interfaces FastEthernet 0/0
   ```
   *Answer:*  
   ______________________________________________________________________  
   ______________________________________________________________________

<br>

3. **Flash Memory Size:**  
   How large is the router's IOS binary image file, specified in bytes?[cite: 1]  
   *Command clue:*
   ```ios
   show flash:
   ```
   *Answer:*  
   ______________________________________________________________________  
   ______________________________________________________________________

---

## 6. Verification & Sign-off Checklist

Before requesting evaluation, ensure that all criteria are met:

- [ ] Canvas profile picture is verified ([hv.instructure.com](https://hv.instructure.com/)).
- [ ] End-to-end HTTP access from `PC-Left` to `http://192.168.2.100/` succeeds.
- [ ] Console login is protected by password `cisco`.
- [ ] Privileged EXEC mode (`enable`) is protected by password `secure`.
- [ ] System clock is set and verified via `show clock` on all network devices.
- [ ] `service password-encryption` is active (no cleartext passwords in `show run`).
- [ ] All 8 operational interfaces contain descriptive `description` tags.
- [ ] Configurations are permanently saved using `copy running-config startup-config`.

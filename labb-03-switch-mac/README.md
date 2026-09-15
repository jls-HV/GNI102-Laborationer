# GNI102 – Lab 3: Switch Configuration, MAC Addresses, and NIC Operations

This lab series covers the fundamentals of Cisco Layer 2 Catalyst switches, the formation and analysis of the switch MAC address table, address resolution, and physical host NIC verification.

---

# Lab 3.1: Basic Switch and End Device Configuration

## Topology

<p align="center">
  <img src="images/topology-3.1.png" alt="Lab 3.1 Topology" width="40%">
</p>

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **S1** | `VLAN 1` | `192.168.1.1` | `255.255.255.0` | *N/A* |
| **S2** | `VLAN 1` | `192.168.1.2` | `255.255.255.0` | *N/A* |
| **PC-A** | `NIC` | `192.168.1.10` | `255.255.255.0` | *N/A* |
| **PC-B** | `NIC` | `192.168.1.11` | `255.255.255.0` | *N/A* |

---

## Objectives

* Set Up the Network Topology
* Configure PC Hosts
* Configure and Verify Basic Switch Settings

---

## Background / Scenario

In this lab, you will build a simple network with two hosts and two switches. You will configure basic settings including hostnames, passwords, login banners, and Switch Virtual Interfaces (SVI). You will also verify end-to-end communication using the `ping` utility.

> **Note:** Ensure both switches have been erased and hold no startup configuration prior to beginning.

---

## Required Resources

* `2x` Switches (Cisco Catalyst 2960 with Cisco IOS 15.0(2) or comparable)
* `2x` Host PCs (Windows with terminal emulation, such as PuTTY)
* Console and Ethernet patch cables

---

## Step-by-Step Instructions

### Step 1: Cable the Network Topology
1. Interconnect switch port `Gi1/0/10` on **S1** to `Gi1/0/10` on **S2**.
2. Connect **PC-A** to port `Gi1/0/5` on **S1**.
3. Connect **PC-B** to port `Gi1/0/15` on **S2**.
4. Power on all hardware devices.

### Step 2: Configure Host Addressing
Assign static IPv4 addresses and subnet masks to **PC-A** and **PC-B** according to the Addressing Table.

### Step 3: Configure and Verify Basic Switch Settings

**Note:** Do the following steps <u>without</u> checking the appendix.

 Console into the switch. Do the following configuration **on both switches**:
   1) Set the **correct time** in privileged EXEC mode.
   2) Set correct hostname according to the Addressing Table in global configuration mode.
   3) Disable unwanted DNS lookups.
   4) Use **class** as privileged EXEC password.
   5) Enter a login MOTD banner to warn unauthorized access.
   6) Encrypt plain text passwords.
   7) Use **cisco** as line console password.
   8) Configure and enable the SVI according to the Addressing Table.
   9) Set descriptions on appropriate interfaces.
   10) Save the configuration.

### Step 4: Verification and Connectivity Tests

 Confirm your configuration on both switches by doing the following steps:
   1) Display current time.
   2) Display the current configuration.
   3) Display IOS version.
   4) Display status of the connected interfaces on the switch.

Execute the verification commands:
```ios
show clock
show running-config
show version
show ip interface brief
```
*(Repeat configuration on **S2** using hostname `S2`, IP address `192.168.1.2`, and port description for **PC-B** on `Gi1/0/15`)*.


* **PC-A to PC-B Verification:** From **PC-A**, ping **PC-B** (`ping 192.168.1.11`).  
  *Is the ping successful? (Yes/No):*  
  ______________________________________________________________________

* **S1 to S2 Verification:** From **S1**, ping **S2** (`ping 192.168.1.2`).  
  *Is the ping successful? (Yes/No):*  
  ______________________________________________________________________

## Reflection Questions

Check the appendix and compare your configuration. Did you do anything differently?

*Answer:*  
______________________________________________________________________  
______________________________________________________________________

---

## Appendix A

```ios
--- System Configuration Dialog ---

Would you like to enter the initial configuration dialog? [yes/no]: no


Press RETURN to get started!


Switch>enable
Switch#clock set 09:15:00 21 SEP 2024
Switch#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#hostname SX (X =number of your Switch)
SX(config)#no ip domain lookup
SX(config)#enable password class
SX(config)#banner motd # UNAUTHORIZED ACCESS FORBIDDEN! #
SX(config)#service password-encryption
SX(config)#line console 0
SX(config-line)#password cisco
SX(config-line)#login
SX(config-line)#interface vlan 1
SX(config-if)#ip address 192.168.1.X 255.255.255.0
SX(config-if)#no shutdown
SX(config-if)#description Link to MANAGEMENT
SX(config-if)#interface G1/0/10
SX(config-if)#description Link to SX
S1(config-if)#interface G1/0/05
S1(config-if)#description Link to PC-A
S2(config-if)#interface G1/0/15
S2(config-if)#description Link to PC-B
S1#copy running-config startup-config
filename [startup-config]?
Building configuration... [OK]
SX#show clock
SX#show running-config
SX#show version
SX#show ip interface brief
```
---

## Clean-up (Lab 3.1)
Erase configuration and reload both switches before continuing:
```ios
erase startup-config
reload
```

---
---

# Lab 3.2: View the Switch MAC Address Table

## Topology

<p align="center">
  <img src="images/topology-3.2.png" alt="Lab 3.2 Topology" width="60%">
</p>

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **S1** | `VLAN 1` | `192.168.1.11` | `255.255.255.0` | *N/A* |
| **S2** | `VLAN 1` | `192.168.1.12` | `255.255.255.0` | *N/A* |
| **PC-A** | `NIC` | `192.168.1.1` | `255.255.255.0` | *N/A* |
| **PC-B** | `NIC` | `192.168.1.2` | `255.255.255.0` | *N/A* |

---

## Objectives

* **Part 1:** Build and Configure the Network
* **Part 2:** Examine the Switch MAC Address Table

---

## Instructions

### Part 1: Build and Configure the Network
1. Cable devices according to the topology (`F0/1` between switches, `F0/6` to **PC-A**, `F0/18` to **PC-B**).
2. Configure IP parameters on PCs and Switch SVIs.
3. Apply passwords: `cisco` (console/vty) and `class` (privileged EXEC).

### Part 2: Examine the Switch MAC Address Table

#### Step 1: Record Network Device MAC Addresses
Open a command prompt on both PCs and record their physical addresses via `ipconfig /all`:

* **PC-A MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **PC-B MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

Console into **S1** and **S2** and record the Burned-in Address (bia) via `show interface gi1/0/1` (or relevant trunk port):

* **S1 Gi1/0/1 (or F0/1) MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **S2 Gi1/0/1 (or F0/1) MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

#### Step 2: Display the MAC Address Table
From privileged EXEC mode on **S2**, execute:
```ios
show mac address-table
```

* **Question:** What MAC addresses are recorded in the table, to which ports are they mapped, and to which devices do they belong?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

#### Step 3: Clear and Re-evaluate Dynamic Entries
Clear dynamic entries and view the table:
```ios
clear mac address-table dynamic
show mac address-table
```

* **Question:** Does the table contain any addresses immediately after clearing? What happens after 10–15 seconds?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

#### Step 4: Traffic Generation & ARP Verification
1. On **PC-B**, check the ARP table:
   ```cmd
   arp -a
   ```
2. Ping **PC-A**, **S1**, and **S2** from **PC-B**.
3. Check `show mac address-table` on **S2** again.
4. Rerun `arp -a` on **PC-B**.

* **Question:** Has the switch added additional dynamic MAC addresses after traffic generation? List the new additions:  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

---

## Clean-up (Lab 3.2)
Reset devices before proceeding:
```ios
erase startup-config
reload
```

---
---

# Lab 3.3: View Network Device MAC Addresses

## Topology

<p align="center">
  <img src="images/topology-3.3.png" alt="Lab 3.3 Topology" width="55%">
</p>

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **S1** | `VLAN 1` | `192.168.1.2` | `255.255.255.0` | *N/A* |
| **PC-A** | `NIC` | `192.168.1.3` | `255.255.255.0` | `192.168.1.1` |

---

## Step-by-Step Instructions

### Step 1: Device Configuration & Verification
1. Connect **PC-A** to port `Gi1/0/6` on **S1**.
2. Configure **PC-A** with IP `192.168.1.3`, mask `255.255.255.0`, and gateway `192.168.1.1`.
3. On **S1**, configure:
   ```ios
   configure terminal
   hostname S1
   no ip domain-lookup
   interface vlan 1
    ip address 192.168.1.2 255.255.255.0
    no shutdown
    end
   ```
4. Ping `192.168.1.2` from **PC-A**.

### Step 2: Analyze the MAC Address for the PC-A NIC
Run `ipconfig /all` on **PC-A** and evaluate the 48-bit physical address:

* **Question 1:** What is the OUI portion (first 3 bytes / 6 hex digits) of your NIC's MAC address?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question 2:** What is the vendor serial number portion (last 3 bytes / 6 hex digits)?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question 3:** Using an IEEE OUI lookup tool, what vendor manufactured the NIC?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

### Step 3: Analyze the MAC Address on Switch S1
Console into **S1** and run:
```ios
show interfaces vlan 1
show arp
show mac address-table
```

* **Question 1:** What is the MAC address for interface `VLAN 1` on **S1**?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question 2:** What does the abbreviation `bia` stand for in the interface output?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question 3:** Did `show mac address-table` display the MAC address of **PC-A**? What port was it mapped to?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

---
---

# Lab 3.4: View Wired and Wireless NIC Information

## Objectives

* **Part 1:** Identify and Work with PC NICs
* **Part 2:** Identify and Use System Tray Network Icons

---

## Step-by-Step Instructions

### Part 1: Work with PC NICs

#### Step 1: Network Connections
1. Open **Control Panel** $\rightarrow$ **Network and Internet** $\rightarrow$ **Network and Sharing Center**.
2. Click **Change adapter settings** in the left panel.

#### Step 2: Wireless NIC Verification
1. Right-click the **Wi-Fi** adapter and select **Status**.
2. Answer the following from the **Status** and **Details** windows:

* **SSID of your connection:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Speed of your connection:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **MAC Address of the Wireless NIC:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Why would multiple IPv4 DNS servers be listed?**  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

#### Step 3: Wired Ethernet NIC Verification
1. Connect an Ethernet cable to your local LAN adapter.
2. Right-click **Ethernet** $\rightarrow$ **Status** $\rightarrow$ **Details...**
3. Run `ipconfig /all` in the Command Prompt to compare output with the GUI.

---

### Part 2: System Tray Network Indicators & Reflection

1. Disable Wi-Fi and Ethernet in **Network Connections** and observe the system tray icon.
2. Run the Windows **Troubleshoot** feature or re-enable adapters manually.

* **Reflection Question:** Why would an administrator or user activate more than one NIC simultaneously on a single computer system?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

---

## Sign-off Checklist (Lab 3 Series)

Before leaving the lab session, have an instructor verify your work:

- [ ] Baseline configurations, SVIs, and host addressing active on S1, S2, and PCs.
- [ ] End-to-end ICMP reachability (`ping`) verified across all devices.
- [ ] Dynamic MAC address table behavior examined on switch console.
- [ ] Hardware OUI and physical addresses analyzed and documented.
- [ ] Host NIC states and system tray icons verified.
- [ ] All hardware switch configurations erased and reloaded prior to exit (`erase startup-config`).

# GNI102 – Lab 3: Switch Configuration, MAC Addresses, and NIC Operations

This lab series covers the fundamentals of Cisco Layer 2 Catalyst switches, the formation and analysis of the switch MAC address table, address resolution, and physical host NIC verification.

---

# Lab 3.1: Basic Switch and End Device Configuration

## Topology

<p align="center">
  <img src="images/topology-3.1.png" alt="Lab 3.1 Topology" width="50%">
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

# Lab - View Network Device MAC Addresses

## Instructions Part 1: Configure Devices and Verify Connectivity

In this part, you will set up the network topology and configure basic settings, such as the interface IP addresses and device name. For device name and address information, refer to the Topology and Addressing Table.

### Step 1: Cable the network as shown in the topology.

a. Attach the devices shown in the topology and cable as necessary.  
b. Power on all the devices in the topology.

### Step 2: Configure the IPv4 address for the PC.

a. Configure the IPv4 address, subnet mask, and default gateway address for PC-A.  
b. From the command prompt on PC-A, ping the switch address.

Were the pings successful? Explain.  
______________________________________________________________________  
______________________________________________________________________

### Step 3: Configure basic settings for the switch.

In this step, you will configure the device name and the IP address, and disable DNS lookup on the switch.

a. Console into the switch and enter global configuration mode.
   ```ios
   Switch> enable
   Switch# configure terminal
   Enter configuration commands, one per line. End with CNTL/Z.
   Switch(config)#
   ```

b. Assign a hostname to the switch based on the Addressing Table.
   ```ios
   Switch(config)# hostname S1
   ```

c. Disable DNS lookup.
   ```ios
   S1(config)# no ip domain-lookup
   ```

d. Configure and enable the SVI interface for VLAN 1.
   ```ios
   S1(config)# interface vlan 1
   S1(config-if)# ip address 192.168.1.2 255.255.255.0
   S1(config-if)# no shutdown
   S1(config-if)# end
   ```

### Step 4: Verify network connectivity.

Ping the switch from PC-A.

Were the pings successful? Write down answer below .  
______________________________________________________________________  
______________________________________________________________________

Every device on an Ethernet LAN has a MAC address that is assigned by the manufacturer and stored in the firmware of the NIC. Ethernet MAC addresses are 48-bits long. They are displayed using six sets of hexadecimal digits that are usually separated by dashes, colons, or periods. The following example shows the same MAC address using the three different notation methods:

```text
00-05-9A-3C-78-00    00:05:9A:3C:78:00    0005.9A3C.7800
```
  ______________________________________________________________________

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

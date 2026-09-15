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

# Lab 3.2 – View the Switch MAC Address Table

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

## Background / Scenario

The purpose of a Layer 2 LAN switch is to deliver Ethernet frames to host devices on the local network. The switch records host MAC addresses that are visible on the network, and maps those MAC addresses to its own Ethernet switch ports. This process is called building the MAC address table. When a switch receives a frame from a PC, it examines the frame’s source and destination MAC addresses. The source MAC address is recorded and mapped to the switch port from which it arrived. Then the destination MAC address is looked up in the MAC address table. If the destination MAC address is a known address, then the frame is forwarded out of the corresponding switch port associated with that MAC address. If the MAC address is unknown, then the frame is broadcasted out of all switch ports, except the one from which it came. It is important to observe and understand the function of a switch and how it delivers data on the network. The way a switch operates has implications for network administrators whose job it is to ensure secure and consistent network communication.

Switches are used to interconnect and deliver information to computers on local area networks. Switches deliver Ethernet frames to host devices identified by network interface card MAC addresses.

In Part 1, you will build a multi-switch topology with a trunk linking the two switches. In Part 2, you will ping various devices and observe how the two switches build their MAC address tables.

> **Note:** The switches used are Cisco Catalyst 2960s with Cisco IOS Release 15.2(2) (lanbasek9 image). Other switches and Cisco IOS versions can be used. Depending on the model and Cisco IOS version, the commands available and output produced might vary from what is shown in the labs.
>
> **Note:** Make sure that the switches have been erased and have no startup configurations. If you are unsure contact your instructor.

---

## Required Resources

* 2 Switches (Cisco 2960 with Cisco IOS Release 15.2(2) lanbasek9 image or comparable)
* 2 PCs (Windows with terminal emulation program, such as PuTTY)
* Console cables to configure the Cisco IOS devices via the console ports
* Ethernet cables as shown in the topology

> **Note:** The Fast Ethernet interfaces on Cisco 2960 switches are autosensing and an Ethernet straight-through cable may be used between switches S1 and S2. If using another model Cisco switch, it may be necessary to use an Ethernet crossover cable.

---

## Instructions

### Part 1: Build and Configure the Network

#### Step 1: Cable the network according to the topology.

#### Step 2: Configure PC hosts.

#### Step 3: Initialize and reload switches as necessary.

#### Step 4: Configure basic settings for each switch.
a. Configure device name as shown in the topology.  
b. Configure IP address as listed in Addressing Table.  
c. Assign `cisco` as the console and vty passwords.  
d. Assign `class` as the privileged EXEC password.

---

### Part 2: Examine the Switch MAC Address Table

A switch learns MAC addresses and builds the MAC address table, as network devices initiate communication on the network.

#### Step 1: Record network device MAC addresses.

a. Open a command prompt on PC-A and PC-B and type `ipconfig /all`.

What are the Ethernet adapter physical addresses?

* **PC-A MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **PC-B MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

<br>

b. Console into switch S1 and S2 and type the `show interface gi1/0/1` command on each switch.

On the second line of command output, what is the hardware addresses (or burned-in address [bia])?

* **S1 Gi1/0/1 MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

* **S2 Gi 1/0/1 MAC Address:**  
  ______________________________________________________________________  
  ______________________________________________________________________

<br>

#### Step 2: Display the switch MAC address table.

Console into switch S2 and view the MAC address table, both before and after running network communication tests with ping.

a. Establish a console connection to S2 and enter privileged EXEC mode.  
b. In privileged EXEC mode, type the `show mac address-table` command and press Enter.

```ios
S2# show mac address-table
```

Even though there has been no network communication initiated across the network (i.e., no use of ping), it is possible that the switch has learned MAC addresses from its connection to the PC and the other switch.

Are there any MAC addresses recorded in the MAC address table?  
______________________________________________________________________  
______________________________________________________________________

<br>

What MAC addresses are recorded in the table? To which switch ports are they mapped and to which devices do they belong? Ignore MAC addresses that are mapped to the CPU.  
______________________________________________________________________  
______________________________________________________________________

<br>

If you had not previously recorded MAC addresses of network devices in Step 1, how could you tell which devices the MAC addresses belong to, using only the output from the `show mac address-table` command? Does it work in all scenarios?  
______________________________________________________________________  
______________________________________________________________________

<br>

#### Step 3: Clear the S2 MAC address table and display the MAC address table again.

a. In privileged EXEC mode, type the `clear mac address-table dynamic` command and press Enter.

```ios
S2# clear mac address-table dynamic
```

b. Quickly type the `show mac address-table` command again.

* Does the MAC address table have any addresses in it for VLAN 1? Are there other MAC addresses listed?  
  ______________________________________________________________________  
  ______________________________________________________________________

* Wait 10 seconds, type the `show mac address-table` command, and press Enter. Are there new addresses in the MAC address table?  
  ______________________________________________________________________  
  ______________________________________________________________________

<br>

#### Step 4: From PC-B, ping the devices on the network and observe the switch MAC address table.

a. From PC-B, open a command prompt and type `arp -a`.

Not including multicast or broadcast addresses, how many device IP-to-MAC address pairs have been learned by ARP?  
______________________________________________________________________  
______________________________________________________________________

<br>

b. From the PC-B command prompt, ping PC-A, S1, and S2.

Did all devices have successful replies? If not, check your cabling and IP configurations.  
______________________________________________________________________  
______________________________________________________________________

<br>

c. From a console connection to S2, enter the `show mac address-table` command.

Has the switch added additional MAC addresses to the MAC address table? If so, which addresses and devices?  
______________________________________________________________________  
______________________________________________________________________

<br>

From PC-B, open a command prompt and retype `arp -a`.

Does the PC-B ARP cache have additional entries for all network devices that were sent pings?  
______________________________________________________________________  
______________________________________________________________________

---

## Reflection Question

On Ethernet networks, data is delivered to devices by their MAC addresses. For this to happen, switches and PCs dynamically build ARP caches and MAC address tables. With only a few computers on the network this process seems fairly easy. What might be some of the challenges on larger networks?  
______________________________________________________________________  
______________________________________________________________________
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

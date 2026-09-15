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

## Background / Scenario

Every device on an Ethernet LAN is identified by a Layer 2 MAC address. This address is assigned by the manufacturer and stored in the firmware of the NIC. This lab will explore and analyze the components that make up a MAC address, and how you can find this information on a switch and a PC.

You will cable the equipment as shown in the topology. You will configure the switch and PC to match the addressing table. You will verify your configurations by testing for network connectivity.

After the devices have been configured and network connectivity has been verified, you will use various commands to retrieve information from the devices to answer questions about your network equipment.

> **Note:** The switches used are Cisco Catalyst 2960s with Cisco IOS Release 15.2(2) (lanbasek9 image). Other switches and Cisco IOS versions can be used. Depending on the model and Cisco IOS version, the commands available and the output produced might vary from what is shown in the labs.
>
> **Note:** Make sure that the switches have been erased and have no startup configurations. If you are unsure, ask your instructor.

---

## Required Resources

* 1 Switch (Cisco 2960 with Cisco IOS Release 15.2(2) lanbasek9 image or comparable)
* 1 PC (Windows with a terminal emulation program, such as PuTTY)
* Console cable to configure the Cisco switch via the console ports
* Ethernet cables as shown in the topology

---

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

<br>

Every device on an Ethernet LAN has a MAC address that is assigned by the manufacturer and stored in the firmware of the NIC. Ethernet MAC addresses are 48-bits long. They are displayed using six sets of hexadecimal digits that are usually separated by dashes, colons, or periods. The following example shows the same MAC address using the three different notation methods:

```text
00-05-9A-3C-78-00    00:05:9A:3C:78:00    0005.9A3C.7800
```

> **Note:** MAC addresses are also called physical addresses, hardware addresses, or Ethernet hardware addresses.

You will issue commands to display the MAC addresses on a PC and a switch, and analyze the properties of each one.

### Step 5: Analyze the MAC address for the PC-A NIC.

Before you analyze the MAC address on PC-A, look at an example from a different PC NIC. You can issue the `ipconfig /all` command to view the MAC address of your NIC. An example screen output is shown below. When using the `ipconfig /all` command, notice that MAC addresses are referred to as physical addresses. ```diff - Reading the MAC address from left to right, the first six hex digits refer to the vendor (manufacturer) of this device. These first six hex digits (3 bytes) are also known as the organizationally unique identifier (OUI). ``` This 3-byte code is assigned to the vendor by the IEEE organization.

To find the manufacturer, use the keywords IEEE OUI standards to find an OUI lookup tool on the internet or navigate to `http://standards-oui.ieee.org/oui.txt` to find the registered OUI vendor codes. The last six digits are the NIC serial number assigned by the manufacturer.

a. Using the output from the `ipconfig /all` command, answer the following questions.

```cmd
C:\> ipconfig /all
<output omitted> 
Ethernet adapter Ethernet:
   Connection-specific DNS Suffix  . :
   Description . . . . . . . . . . . : Intel(R) 82577LM Gigabit Network Connection
   Physical Address. . . . . . . . . : 5C-26-0A-24-2A-60
   DHCP Enabled. . . . . . . . . . . : Yes 
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::b875:731b:3c7b:c0b1%10(Preferred) 
   IPv4 Address. . . . . . . . . . . : 192.168.1.147(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Lease Obtained. . . . . . . . . . : Friday, September 6, 2019 11:08:36 AM 
   Lease Expires . . . . . . . . . . : Saturday, September 7, 2019 11:08:36 AM 
   Default Gateway . . . . . . . . . : 192.168.1.1
<output omitted>
```

* **Question:** What is the OUI portion of the MAC address for this device?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question:** What is the serial number portion of the MAC address for this device?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question:** Using the example above, find the name of the vendor that manufactured this NIC.  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

<br>

b. From the command prompt on PC-A, issue the `ipconfig /all` command and identify the OUI portion of the MAC address for the NIC of PC-A.

* **Question:** Identify the serial number portion of the MAC address for the NIC of PC-A.  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Identify the name of the vendor that manufactured the NIC of PC-A.**  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

### Step 6: Analyze the MAC address for the S1 Gi1/0/6 interface.

You can use a variety of commands to display MAC addresses on the switch.

a. Console into S1 and use the `show interfaces vlan 1` command to find the MAC address information. A sample is shown below. Use output generated by your switch to answer the questions.

```ios
S1# show interfaces vlan 1
Vlan1 is up, line protocol is up
Hardware is GiEtherSVI, address is 001b.0c6d.8f40 (bia 001b.0c6d.8f40)
Internet address is 192.168.1.2/24 MTU 1500 bytes, BW 1000000 Kbit/sec, DLY 10 usec, reliability 255/255, txload 1/255, rxload 1/255 Encapsulation ARPA, loopback not set
Keepalive not supported
ARP type: ARPA, ARP Timeout 04:00:00
Last input never, output 00:14:51, output hang never Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0 Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
0 packets input, 0 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicasts)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
34 packets output, 11119 bytes, 0 underruns
0 output errors, 2 interface resets
0 unknown protocol drops
0 output buffer failures, 0 output buffers swapped out
```

* **Question:** What is the MAC address for VLAN 1 on S1?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question:** What is the MAC serial number for VLAN 1?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question:** What does bia stand for?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question:** Why does the output show the same MAC address twice?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

<br>

b. Another way to display the MAC address on the switch is to use the `show arp` command. Use the `show arp` command to display MAC address information. This command maps the Layer 2 address to its corresponding Layer 3 address. A sample is shown below. Use output generated by your switch to answer the questions.

```ios
S1# show arp
Protocol  Address          Age (min)  Hardware Addr   Type  Interface
Internet  192.168.1.2             -   001b.0c6d.8f40  ARPA  Vlan1
Internet  192.168.1.3             0   5c26.0a24.2a60  ARPA  Vlan1
```

* **Question:** What Layer 2 addresses are displayed on S1?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Question:** What Layer 3 addresses are displayed on S1?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

### Step 7: View the MAC addresses on the switch.

Issue the `show mac address-table` command on S1. A sample is shown below. Use output generated by your switch to answer the questions.

```ios
S1# show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
 All    0100.0ccc.cccc    STATIC      CPU
 All    0100.0ccc.cccd    STATIC      CPU
 All    0180.c200.0000    STATIC      CPU
 All    0180.c200.0001    STATIC      CPU
 All    0180.c200.0002    STATIC      CPU
 All    0180.c200.0003    STATIC      CPU
 All    0180.c200.0004    STATIC      CPU
 All    0180.c200.0005    STATIC      CPU
 All    0180.c200.0006    STATIC      CPU
 All    0180.c200.0007    STATIC      CPU
 All    0180.c200.0008    STATIC      CPU
 All    0180.c200.0009    STATIC      CPU
 All    0180.c200.000a    STATIC      CPU
 All    0180.c200.000b    STATIC      CPU
 All    0180.c200.000c    STATIC      CPU
 All    0180.c200.000d    STATIC      CPU
 All    0180.c200.000e    STATIC      CPU
 All    0180.c200.000f    STATIC      CPU
 All    0180.c200.0010    STATIC      CPU
 All    ffff.ffff.ffff    STATIC      CPU
   1    5c26.0a24.2a60    DYNAMIC     Gi1/0/6
Total Mac Addresses for this criterion: 21
```

* **Question:** Did the switch display the MAC address of PC-A? If you answered yes, what port was it on?  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

---

## Reflection Questions

* **Can you have broadcasts at the Layer 2 level? If so, what would the MAC address be?**  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

* **Why would you need to know the MAC address of a device?**  
  *Answer:*  
  ______________________________________________________________________  
  ______________________________________________________________________

  ## Clean-up (Lab 3.3)
Reset devices before proceeding:
```ios
erase startup-config
reload
```

---
---

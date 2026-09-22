# Lab 3.3 – View Network Device MAC Addresses

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

Before you analyze the MAC address on PC-A, look at an example from a different PC NIC. You can issue the `ipconfig /all` command to view the MAC address of your NIC. An example screen output is shown below. When using the `ipconfig /all` command, notice that MAC addresses are referred to as physical addresses. Reading the MAC address from left to right, the first six hex digits refer to the vendor (manufacturer) of this device. These first six hex digits (3 bytes) are also known as the organizationally unique identifier (OUI). This 3-byte code is assigned to the vendor by the IEEE organization.

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

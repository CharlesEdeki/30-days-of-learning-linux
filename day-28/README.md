# Day 28 - How the Network Knows Where to Go

## Objective
Understand the foundational concepts of Linux networking: how data
travels between machines, what the network stack is and how its
layers relate to each other, how IP addresses work, and what subnets
and CIDR notation actually mean.

---

## What I Learned

### The Two Questions Networking Answers
- When a computer sends data, how does it know where to send it?
- When the destination receives data, how does it know what it got?
- Every networking concept is ultimately an answer to one of these

### Basic Network Structure
- A LAN (Local Area Network) is a group of machines on the same
  local network
- A router connects the LAN to the internet (WAN/uplink)
- Every machine on a network is called a host, including routers
- The router has two connections: one to the LAN, one to the internet
- All LAN machines get internet access through the router

### Packets
- Data does not travel as a continuous stream. It travels in packets
- Each packet has two parts: a header and a payload
- Header: identifying info including source, destination, protocol
- Payload: the actual data being sent
- Breaking into packets allows multiple hosts to communicate
  simultaneously by interleaving packets on the same connection
- Easier error detection: only corrupted packets need resending
- The OS handles translation between packets and application data

### The Network Stack
Four layers from top to bottom:

**Application layer**
- Where HTTP, TLS, FTP, and other application protocols live
- The language applications use to talk to each other
- Processing happens in user space

**Transport layer**
- Handles breaking data into packets and reassembly at destination
- Manages source and destination ports
- TCP and UDP are the two dominant protocols
- Also called the protocol layer
- Handled primarily by the kernel in Linux

**Network/Internet layer**
- Moves packets from source host to destination across networks
- IP (Internet Protocol) governs this layer
- IPv4 and IPv6 are the two versions in use
- Handled by the kernel

**Physical/Link layer**
- Sends raw data across physical medium: copper, fibre, radio
- Ethernet is the most common example
- Also called the host-to-network layer

### How Data Travels
- Sending: data travels down through the layers, application
  to physical
- Receiving: data travels up through the layers, physical to
  application
- Routers in the middle pass data through their lower layers

### The Internet Layer and IP Addresses
- Internet layer is software-based. No hardware requirements
- Works over any physical medium on any OS
- Every host has at least one IP address per connected network
- IPv4: four numbers separated by dots (dotted-quad notation)
  Example: 10.23.2.4
- View active IP addresses: ip address show
- Output grouped by physical interface
- inet line shows the IPv4 address
- /24 after the address is subnet notation

### Subnets
- A subnet is a neighbourhood where all hosts share the same
  address prefix
- Example: 10.23.2.4 — the street name is 10.23.2, the house
  number is 4
- Any address starting with 10.23.2 is on the same subnet
- Subnet mask defines where the network prefix ends and the
  host part begins
- 255.255.255.0 means: first three sections are fixed (network),
  last section is where individual hosts live

### CIDR Notation
- Subnet masks always follow the same binary pattern: a block
  of 1s followed by a block of 0s, nothing else
- 255.255.255.0 in binary is 24 ones followed by 8 zeros
- CIDR shorthand: just count the leading 1s and write /N
- /24 means the first 24 bits are the fixed network part
- 10.23.2.4/24 means: address is 10.23.2.4, first 24 bits are
  the network, any address starting 10.23.2 is on the same subnet
- /24 allows 254 usable host addresses (256 minus 2 reserved)
- /16 fixes the first two sections, allows more hosts per subnet

---

## What I Built / Practiced

```bash
# View IP addresses and interfaces
ip address show

# View routing table
ip route show

# Check network interfaces
ip link show
```

---

## Challenges Faced
- Reading day focused on concepts and mental models
- CIDR notation required thinking in binary to understand why
  /24 equals 255.255.255.0. Key insight: count the 1s in the
  subnet mask binary representation and that is your CIDR number

---

## Key Takeaways
- Networking answers two questions: where to send data, and what
  kind of data just arrived. Day 28 covered the where
- Data travels in packets with headers (addressing info) and
  payloads (actual data). The OS handles this transparently
- The network stack has four layers. Application and transport
  live in user space. Network and physical are in the kernel
- An IP address is a host's identity on a network. A subnet
  defines which hosts are neighbours
- The subnet mask is a dividing line across an IP address
  separating the fixed network part from the variable host part
- CIDR /24 is just a count of the fixed bits. Nothing more
- The transport and application layers, which answer the what
  question, are coming on Day 29

---

## Commands Used

```bash
ip address show      # view IP addresses per interface
ip route show        # view the routing table
ip link show         # view network interfaces and their state
```

---

## Resources
- How Linux Works by Brian Ward, chapter 9
- [Medium: Day 28 write-up](https://medium.com/@charlesedeki093/day-28-how-the-network-knows-where-to-go-b74ebc981315)

---

## Output
- Day 28 write-up published on Medium

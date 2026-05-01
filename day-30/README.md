# Day 30 - The Last Pieces of the Network

## Objective
Complete the networking foundation by understanding ICMP and ping
for network testing, DNS name resolution, the physical Ethernet
layer, and how kernel network interfaces bridge software and hardware.
Close out the 30-day challenge.

---

## What I Learned

### ICMP and ping
- ICMP (Internet Control Message Protocol) carries no user data
  and has no application layer above it
- It exists purely to configure and diagnose networks
- ping sends ICMP echo request packets and measures the response
- icmp_seq: sequence number. Gaps mean lost packets
- time: round-trip time. Over 1 second means serious problems
- Ctrl+C stops ping and prints summary: transmitted, received,
  loss percentage, min/avg/max/mdev round-trip breakdown
- Some hosts disable ICMP responses deliberately. Silence from
  ping does not always mean the host is down
- ping -4 forces IPv4. ping -6 forces IPv6

### DNS
- DNS (Domain Name Service) maps human-readable names to IP addresses
- host command performs manual DNS lookups
- A hostname can have multiple IPv4 and IPv6 addresses
- Reverse lookup (IP to hostname) is unreliable. Many admins
  never configure it and one IP can map to multiple hostnames

### The Physical Layer and Ethernet
- IP addresses, routing, subnets are all software
- Ethernet handles the physical layer: getting data off the machine
- MAC address: hardware address tied to the physical interface
  itself. Format: 92:00:06:b3:6d:8c (six hex pairs)
- Unlike IP addresses, MAC addresses are baked into hardware
- Data travels in frames on an Ethernet network
- Frame: envelope around the packet containing source and
  destination MAC addresses
- Frames cannot cross between separate physical networks
- Routers strip incoming frames, repackage data into new frames
  addressed to the next hop, and forward them
- IP packets travel the full journey. Frames are replaced at
  every hop

### Kernel Network Interfaces
- Network interfaces bridge the software internet layer and the
  hardware physical layer without making them dependent on each other
- Interface naming: predictable names stay the same across reboots
- lo (loopback): always present. How the system talks to itself.
  Always 127.0.0.1 for IPv4 and ::1 for IPv6
- eth0: physical network card with public IP, global IPv6,
  and link-local IPv6 addresses
- UP flag: interface is active and working
- NO-CARRIER: interface configured but nothing connected
- link/ether line: MAC address for that interface
- ip command: views and configures software layer settings
- ethtool: goes deeper into hardware and physical layer settings

### What the VM Output Revealed
- docker0 interface present with state DOWN and 172.17.0.1/16
- Docker creates this virtual bridge interface for container networking
- State DOWN means no containers currently running
- 172.17.0.1/16 is Docker's internal subnet, separate from external
- veth interfaces with high numbers are virtual Ethernet pairs
  Docker creates dynamically, one per running container
- Their presence confirms this server runs containers regularly

---

## What I Built / Practiced

```bash
# Test network connectivity
ping 172.20.0.1

# DNS lookup
host www.example.com

# View all network interfaces and addresses
ip address show

# View routing table
ip route show

# Hardware details
ethtool eth0
```

### Actual ping output from VM
```
~$ ping 172.20.0.1
PING 172.20.0.1 (172.20.0.1) 56(84) bytes of data.
64 bytes from 172.20.0.1: icmp_seq=1 ttl=64 time=0.067 ms
64 bytes from 172.20.0.1: icmp_seq=2 ttl=64 time=0.053 ms
22 packets transmitted, 22 received, 0% packet loss, time 21537ms
rtt min/avg/max/mdev = 0.031/0.063/0.096/0.014 ms
```

22 packets, 0% loss, sub-millisecond round trips. Clean local
network connection.

### Actual ip address show output from VM
```
1: lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    link/ether 92:00:06:b3:6d:8c
    inet 91.98.230.160/32 scope global dynamic eth0
    inet6 2a01:4f8:1c1c:ff9d::1/64 scope global
    inet6 fe80::9000:6ff:feb3:6d8c/64 scope link

3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> state DOWN
    link/ether a6:7c:b3:2a:0b:48
    inet 172.17.0.1/16 scope global docker0
```

---

## Challenges Faced
- docker0 appearing in the output was unexpected. Resolved by
  understanding that Docker creates virtual network interfaces
  for container networking even when no containers are running
- The veth interfaces required additional reading. They are
  virtual Ethernet pairs created per container to connect
  containers to the docker0 bridge

---

## Key Takeaways
- ping is the first tool to reach for when testing connectivity.
  Zero packet loss and low round-trip times mean a clean path
- DNS translates names to addresses. host does it manually
- Ethernet frames carry IP packets hop by hop. The IP packet
  travels the full journey. Frames are replaced at every router
- The loopback interface lo is always present. 127.0.0.1 always
  means the local machine
- docker0 in ip address show means Docker is installed. Its
  presence connects Day 30 directly to the next phase of the
  data engineering roadmap
- The network layer is complete. Application layer, transport
  layer, ports, and protocols are the logical next step

---

## 30-Day Challenge Complete

### What changed
- Day 1: Linux was a terminal with commands that worked until
  they did not
- Day 30: Linux is a layered system where every behaviour has
  a reason and every tool has a specific job

### The foundation in place
- Process and resource management
- Filesystem and storage
- User authentication and permissions
- System startup and service management
- Logging and diagnostics
- Networking fundamentals

### What comes next
- Production-level project combining Linux skills with the
  data engineering roadmap
- Containers, orchestration, cloud infrastructure
- The docker0 interface was a preview

---

## Commands Used

```bash
ping host                    # test connectivity via ICMP
ping -4 host                 # force IPv4
ping -6 host                 # force IPv6
host domain                  # DNS lookup
host ip_address              # reverse DNS lookup
ip address show              # all interfaces and addresses
ip route show                # routing table
ip -6 address show           # IPv6 addresses
ip -6 route show             # IPv6 routing table
ethtool interface            # hardware and physical layer details
```

---

## Resources
- How Linux Works by Brian Ward, chapters 9
- [Medium: Day 30 write-up](https://medium.com/@charlesedeki093/day-30-the-last-pieces-of-the-network-and-what-30-days-actually-taught-me-7fe269409a01)
- Full 30-day repository: https://github.com/CharlesEdeki/30-days-of-learning-linux

---

## Output
- 30-day Linux challenge completed
- Day 30 write-up published on Medium
- Full repository committed and pushed to GitHub

# Day 29 - How Data Finds Its Way: Routing, IPv6, and Dual-Stack

## Objective
Understand how the kernel routing table works, how the default
gateway fits into routing decisions, how IPv6 addresses the
limitations of IPv4, and how dual-stack configurations run both
protocols simultaneously.

---

## What I Learned

### The Kernel Routing Table
- The routing table is the kernel's decision engine for every
  outgoing packet
- To view it: ip route show
- Each entry has a destination, an instruction, and an interface
- default via 10.23.2.1 dev enp0s31f6: for anything not covered
  by a more specific rule, send it to the router at 10.23.2.1
- 10.23.2.0/24 dev enp0s31f6: to reach anything in this subnet,
  go directly through the interface. No middleman needed
- Directly connected subnets are reached without help
- Everything outside the local subnet goes to the default gateway

### The Default Gateway
- The default entry in the routing table is called the default gateway
- In CIDR notation it is written as 0.0.0.0/0 for IPv4
- A prefix of zero bits matches every address
- Without a default gateway a machine can only reach hosts on
  its directly connected subnets
- By convention on most /24 networks the router sits at address 1
  of the subnet. 10.23.2.0/24 router is typically 10.23.2.1
- This is convention, not a rule, but almost universal

### Longest Prefix Match
- When multiple routing rules match a destination the kernel
  picks the most specific one
- Specificity is measured by prefix length
- 10.23.2.0/24 is more specific than 0.0.0.0/0
- The kernel always prefers the longer prefix regardless of
  the order rules appear in the table
- Same logic as a GPS preferring a street-level instruction
  over a city-level one

### IPv6: Why It Exists
- IPv4 uses 32 bits per address giving about 4.3 billion unique
  addresses
- More internet-connected devices exist than IPv4 addresses
- IPv6 uses 128 bits per address: eight groups of four hex
  digits separated by colons
- Full example: 2001:0db8:0a0b:12f0:0000:0000:0000:8b6e
- Abbreviation rule 1: drop leading zeros in any group
- Abbreviation rule 2: one consecutive sequence of all-zero
  groups can be collapsed to ::
- Abbreviated: 2001:db8:a0b:12f0::8b6e
- Address space is so vast that running out is not a concern

### IPv6 Subnets
- Still uses CIDR notation
- Typical IPv6 subnet is /64
- First 64 bits are the network part
- Last 64 bits are the interface ID identifying the host

### Two Addresses Per IPv6 Host
- Global unicast address: valid across the entire internet.
  Labelled scope global in ip command output. The public address
- Link-local address: valid only on the local network, never
  routed to the internet. Always starts with fe80::
  Lives in the fe80::/10 subnet. Labelled scope link in output
- Global unicast is your public mailing address
- Link-local is what neighbours shout across the fence.
  Works locally, meaningless outside the local network
- IPv6 default gateway often uses a link-local address so
  it does not need to change if global address space is reorganised

### Dual-Stack
- Running IPv4 and IPv6 simultaneously on the same host and network
- The two protocols are completely independent, running in parallel
- Applications choose IPv4 or IPv6 depending on destination support
- IPv4 applications do not automatically speak IPv6
- Adding IPv6 support requires minimal code changes because
  the network layers are separate
- Most modern applications and servers support both

---

## What I Built / Practiced

```bash
# View routing table
ip route show

# View IPv6 addresses
ip -6 address show

# View IPv6 routing table
ip -6 route show

# View all interfaces and addresses
ip address show
```

---

## Challenges Faced
- Reading day focused on concepts and mental models
- The longest prefix match rule required re-reading. Key insight:
  the kernel does not care about rule order. It always picks the
  rule with the most bits in the prefix. More bits means more
  specific means higher priority

---

## Key Takeaways
- The routing table is the kernel's GPS. Directly connected
  subnets are reached directly. Everything else goes to the
  default gateway
- The default gateway matches everything via 0.0.0.0/0 but
  loses to any more specific rule because /0 is the least
  specific prefix possible
- When multiple rules match, longest prefix wins. Order does
  not matter
- IPv6 solves the IPv4 address exhaustion problem with 128-bit
  addresses. The space is effectively inexhaustible
- Every IPv6 host has at least two addresses: a global unicast
  for internet communication and a link-local for local network
  communication only
- Dual-stack runs both protocols in parallel. Most modern
  systems do this by default
- The network layer is now in place. Day 30 covers what happens
  above it: ports, protocols, and the transport layer

---

## Commands Used

```bash
ip route show          # view IPv4 routing table
ip -6 address show     # view IPv6 addresses
ip -6 route show       # view IPv6 routing table
ip address show        # view all addresses per interface
ip link show           # view network interfaces
```

---

## Resources
- How Linux Works by Brian Ward, chapter 9
- [Medium: Day 29 write-up](https://medium.com/@charlesedeki093/day-29-how-data-finds-its-way-073d01ef5f20)

---

## Output
- Day 29 write-up published on Medium

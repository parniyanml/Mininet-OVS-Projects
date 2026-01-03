# Mininet-OVS-Projects

**Practical implementation of network topologies using Mininet and Open vSwitch (OVS), featuring packet analysis with Wireshark.**

## Project Overview

This repository documents a two-phase research and implementation project focused on software-defined networking (SDN) concepts, Linux network namespaces, and traffic analysis. The project utilizes Mininet and Open vSwitch (OVS) to simulate various network topologies and analyzes protocol behavior (ICMP, DNS, HTTP) using Wireshark.

The work is divided into two primary phases:
1.  **Phase 1:** Fundamentals of network namespaces, virtual interfaces, Mininet topologies, and basic switching.
2.  **Phase 2:** Advanced routing, DNS traffic analysis, OVS flow management, and connecting virtual networks to the external internet (NAT).

## Table of Contents

* [Phase 1: Network Namespaces & Topologies](#phase-1-network-namespaces--topologies)
* [Phase 2: Routing, DNS & OVS Flows](#phase-2-routing-dns--ovs-flows)
* [technologies Used](#technologies-used)
* [Key Implementations](#key-implementations)
* [Credits](#credits)

## Phase 1: Network Namespaces & Topologies

This phase focuses on the manual creation of network isolation using Linux namespaces and automating topologies with Mininet.

### Key Objectives
* **Namespace Management:** Creating and managing isolated network environments using `ip netns`.
* **Virtual Interfaces:** Establishing connectivity between namespaces using `veth` pairs and assigning IP addresses.
* **Open vSwitch (OVS) Basics:** Creating virtual bridges (`ovs-vsctl`) to connect multiple namespaces.
* **Mininet Topologies:** implementation and analysis of standard Mininet topologies:
    * **Single:** One switch connected to $k$ hosts.
    * **Reversed:** Similar to Single, but with reversed connection ports.
    * **Tree:** Hierarchical structure (e.g., depth=2, fanout=2).
    * **Linear:** Hosts and switches arranged in a line.

### Traffic Analysis
* Captured ICMP (Ping) and ARP traffic flow between isolated hosts.
* Analyzed loopback interfaces and internal bridge ports.

## Phase 2: Routing, DNS & OVS Flows

This phase expands into higher-layer protocols, static routing, and granular traffic control using OpenFlow rules.

### Key Objectives
* **DNS Protocol Analysis:**
    * Captured and analyzed DNS queries (A, AAAA, HTTPS records) over UDP port 53.
    * Configured local resolution using `/etc/hosts` and analyzed `/etc/resolv.conf`.
* **Client-Server Architecture:**
    * Implemented a manual topology with separate `Client` and `Server` namespaces connected via an OVS bridge.
    * Configured static routing tables (`ip route add default via...`) to enable communication between different subnets.
* **HTTP Service:**
    * Deployed a Python SimpleHTTP server (`python3 -m http.server`) inside a namespace.
    * Fetched content using `wget` to analyze TCP handshakes and HTTP request/response headers.
* **OVS Flow Control:**
    * Managed flow tables using `ovs-ofctl`.
    * Implemented priority-based rules to filter or redirect specific traffic (e.g., dropping packets or redirecting based on destination IP).
* **NAT & IP Forwarding:**
    * Enabled IP forwarding (`sysctl net.ipv4.ip_forward=1`).
    * Configured `iptables` for NAT/Masquerading to allow isolated namespaces to access the external internet (World).

## Technologies Used

* **Mininet:** Network emulator for creating virtual hosts, switches, and links.
* **Open vSwitch (OVS):** Virtual multilayer switch.
* **Linux Networking:** `ip route`, `ip netns`, `ifconfig`, `iptables`.
* **Wireshark:** Network protocol analyzer for capturing and inspecting packets.
* **Python:** Used for scripting and running simple HTTP servers.

## Key Implementations

### Creating a veth pair between namespaces
```bash
# Create namespaces
sudo ip netns add H1
sudo ip netns add R

# Create veth pair
sudo ip link add H1-eth0 type veth peer name R-eth1

# Assign interfaces to namespaces
sudo ip link set H1-eth0 netns H1
sudo ip link set R-eth1 netns R
```

### Adding Flows to OVS
```bash
# Add a flow to output specific traffic to port 1 with priority 101
sudo ovs-ofctl add-flow switch "priority=101,ip_dst=100.1.0.1,actions=output:1"

# Drop traffic with lower priority
sudo ovs-ofctl add-flow switch "priority=50,actions=drop"
```

## Credits

**Project By:** Parniyan Malekzadeh
**Supervisor:** Prof. Heidarpour
**Institution:** Isfahan University of Technology

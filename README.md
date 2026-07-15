# Cisco Packet Tracer EIGRP Routing Lab

This Cisco Packet Tracer lab demonstrates how Enhanced Interior Gateway Routing Protocol (EIGRP) can dynamically exchange routing information between two Cisco routers.

The network contains two separate LANs connected through a router-to-router link. Before EIGRP was configured, each router only knew about its directly connected networks, so PC1 and PC2 could not communicate. After EIGRP was enabled, both routers learned the remote networks and established successful end-to-end communication.

## Lab Objectives

- Build a network containing two routers, two switches, and two computers.
- Configure IPv4 addresses on the routers and end devices.
- Verify local network connectivity.
- Configure EIGRP on both routers.
- Establish an EIGRP neighbor relationship.
- Verify dynamically learned routes.
- Test communication between devices on separate LANs.

## Network Topology

The network contains:

- Two Cisco 2901 routers named **NY** and **LA**
- Two Cisco 2960 Layer 2 switches
- Two computers named **PC1** and **PC2**
- Two separate LANs
- One router-to-router connection

<p align="center">
  <img width="799" height="557" alt="Image" src="https://github.com/user-attachments/assets/1fac64dc-725a-4e91-b17d-a01c5fb3a5d9" />
</p>

## IP Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| NY Router | GigabitEthernet0/1 | 192.168.10.1 | 255.255.255.0 | N/A |
| NY Router | GigabitEthernet0/0 | 192.168.20.1 | 255.255.255.0 | N/A |
| LA Router | GigabitEthernet0/0 | 192.168.20.2 | 255.255.255.0 | N/A |
| LA Router | GigabitEthernet0/1 | 192.168.30.1 | 255.255.255.0 | N/A |
| PC1 | FastEthernet0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 | FastEthernet0 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |

## Initial Connectivity Limitation

Before EIGRP was configured:

- PC1 could communicate with the NY router.
- PC2 could communicate with the LA router.
- The NY router only knew about the `192.168.10.0/24` and `192.168.20.0/24` networks.
- The LA router only knew about the `192.168.20.0/24` and `192.168.30.0/24` networks.
- PC1 could not communicate with PC2.
- No routing information was being exchanged between the routers.

<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/e60883b2-6d5a-413d-a1c9-c6a09b1a8ae9" width="430" alt="NY router EIGRP configuration">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/39fe4c48-ae04-4812-8ebc-8e3b18e849ff" width="430" alt="LA router EIGRP configuration">
    </td>
  </tr>
</table>

## EIGRP Configuration

EIGRP autonomous system number **10** was configured on both routers.

Both routers must use the same autonomous system number to form an EIGRP neighbor relationship and exchange routing information.

### NY Router Configuration

```console
enable
configure terminal

router eigrp 10
network 192.168.10.0
network 192.168.20.0

end
```

### LA Router Configuration

```console
enable
configure terminal

router eigrp 10
network 192.168.20.0
network 192.168.30.0

end
```

The `network` commands identify the directly connected networks that will participate in the EIGRP routing process.

## EIGRP Neighbor Establishment

After EIGRP was enabled, the NY and LA routers discovered each other through the `192.168.20.0/24` network.

A message similar to the following appeared on both routers:

```console
%DUAL-5-NBRCHANGE: IP-EIGRP 10: Neighbor is up: new adjacency
```

This message confirmed that the routers successfully formed an EIGRP neighbor relationship.

<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/4ea113d2-88c1-405a-96a6-430a5150a5f5" width="430" alt="EIGRP routing table verification">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/df40eaf5-6971-49c8-a329-4cec03b79f61" width="430" alt="EIGRP routing table verification">
    </td>
  </tr>
</table>

## Routing Table Verification

The following command was used on both routers to verify their routing tables:

```console
show ip route
```

Routes learned through EIGRP are identified by the letter `D`.

### New York Router Routing Table

| Route Type | Destination Network | Next Hop | Exit Interface |
|---|---|---|---|
| Connected | 192.168.10.0/24 | Directly connected | GigabitEthernet0/1 |
| Connected | 192.168.20.0/24 | Directly connected | GigabitEthernet0/0 |
| EIGRP | 192.168.30.0/24 | 192.168.20.2 | GigabitEthernet0/0 |

The NY router dynamically learned the `192.168.30.0/24` network from the LA router.

### Los Angeles Router Routing Table

| Route Type | Destination Network | Next Hop | Exit Interface |
|---|---|---|---|
| Connected | 192.168.20.0/24 | Directly connected | GigabitEthernet0/0 |
| Connected | 192.168.30.0/24 | Directly connected | GigabitEthernet0/1 |
| EIGRP | 192.168.10.0/24 | 192.168.20.1 | GigabitEthernet0/0 |

The LA router dynamically learned the `192.168.10.0/24` network from the NY router.

## Connectivity Testing

A ping test was performed from PC1 to PC2 using the following command:

```console
ping 192.168.30.10
```

PC2 successfully returned ICMP echo replies.

The successful replies confirmed that:

- PC1 could reach PC2.
- Traffic could travel across both routers.
- EIGRP was exchanging routing information correctly.
- Bidirectional communication between the two LANs was established.

<p align="center">
  <img width="642" height="647" alt="Image" src="https://github.com/user-attachments/assets/8ac872f9-62bb-4c4d-afda-2e84f3d28a70" />
</p>

## Verification Commands

The following Cisco IOS commands can be used to verify and troubleshoot EIGRP:

```console
show ip route
show ip route eigrp
show ip eigrp neighbors
show ip eigrp topology
show ip protocols
show ip interface brief
```

## Results

After EIGRP was configured:

- The NY and LA routers formed an EIGRP neighbor relationship.
- The NY router learned the `192.168.30.0/24` network.
- The LA router learned the `192.168.10.0/24` network.
- EIGRP routes appeared in the routing tables with the letter `D`.
- PC1 successfully communicated with PC2.
- End-to-end connectivity between the two LANs was established.

## Skills Demonstrated

- Cisco Packet Tracer
- IPv4 addressing
- Subnet configuration
- Cisco IOS command-line configuration
- Dynamic routing
- EIGRP configuration
- EIGRP neighbor verification
- Routing table analysis
- ICMP connectivity testing
- Basic network troubleshooting

## Tools and Technologies

- Cisco Packet Tracer
- Cisco 2901 routers
- Cisco 2960 switches
- Cisco IOS CLI
- Enhanced Interior Gateway Routing Protocol
- ICMP ping testing

## Key Takeaways

This lab demonstrated how EIGRP allows routers to automatically exchange routing information.

Before EIGRP was enabled, each router only knew about its directly connected networks. After the routers formed an EIGRP neighbor relationship, they learned the remote LAN routes and provided successful communication between PC1 and PC2.

---

## Lab Documentation

The complete lab report includes the network topology, initial routing limitations, EIGRP configuration, routing-table verification, and successful connectivity testing.

📄 **[View the Cisco Packet Tracer EIGRP Routing Lab](https://github.com/user-attachments/files/30030468/Cisco.Packet.Tracer.EIGRP.Routing.Lab.pdf)**

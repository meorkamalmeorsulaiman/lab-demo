# Multicast

Traffic send to multiple destination or receiver. Address range:
- Private: 239.0.0.0/8

## Multicast Routing 

- Multicast routing is necessary to route multicast traffic throughout the network
- To foward multicast traffic between network segments we use PIM
- Forwarding multicast traffic with PIM requires IGP or Unicast fowarding
- Routers in the network forms PIM neighborhip with each other and build the multicast tree based on multicast traffic/signalling
- Routers use multicast tree to forward multicast traffic.

## Building a Multicast State or Tree

All trees are listed in `show ip mroute` or `show pim topology`

### Rendezvous Point - RP

- RP is a meeting point for multicast traffic. It link between Source and Receiver
- The link between Source to RP called **Source Tree**
- The link between Receiver to RP called **Shared Tree**
- Example - [Source and Share Tree](https://github.com/meorkamalmeorsulaiman/lab-demo/blob/main/lab-multicast/figures/01.png)
- Source tree use by **First Hop Router (FHR)** to forward multicast traffic from source to the RP
  - A FHR is a gateway for source - [First Hop Router and Last Hop Router](https://github.com/meorkamalmeorsulaiman/lab-demo/blob/main/lab-multicast/figures/02.png)
- The RP use **Shared Tree** to forward down to the receiver
- In general, the initial multicast traffic fowarded to the receiver as below
- Example - [Multicast Forwarding](https://github.com/meorkamalmeorsulaiman/lab-demo/blob/main/lab-multicast/figures/03.png)

### Shared Tree

- The Shared Tree built by the **Last Hop Router (LHR)**
  - LHR is a gateway for receiver
- It will created when a receiver join a multicast group. It will sends IGMP Join to the LHR
- LHR will then send a PIM join to the RP and forms the Shared Tree
- LHR knows the RP via PIM configuration
- Shared Tree also known as (*,G) entry in `show ip mroute` or `show pim topology`
- If the RP is not directly connected, the Join will travel hop by hop. This will create an entry on each hop along the way.
- Example - [Shared Tree Creation](https://github.com/meorkamalmeorsulaiman/lab-demo/blob/main/lab-multicast/figures/04.png)

### Source Tree

- The Source Tree built by RP
- When a source send a multicast traffic, the initial multicast traffi from source is encapsulated in PIM-SM message called **Register message** by FHR
- The Register message is sent thru unidirectional tunnel - PIM Register tunnel toward the RP
- FHR knows the RP via PIM configuration
- Once the RP received, it decapsulate the Register message. It will forward the actual multicast traffic toward the Shared Tree
- The RP will also sends a PIM join back to the **FHR** to build the Source Tree
- Source tree also known as (S,G) entry in `show ip mroute` or `show pim topology`
- In general, the **FHR** will register the source with the RP and request the RP to built a Source Tree back to the **FHR**
- Example - [Source Tree Creation](https://github.com/meorkamalmeorsulaiman/lab-demo/blob/main/lab-multicast/figures/05.png)

### Source Tree Switchover

- When LHR received first multicast packet, It aware of the source IP.
- The LHR check the source IP in unicast table that the source IP could be reach using shorter path.
- It sends (S,G) PIM join directly to the FHR hop by hop. This will also create an entry on each hop along the way.
- Example - [Source Tree Switchover](https://github.com/meorkamalmeorsulaiman/lab-demo/blob/main/lab-multicast/figures/06.png)

## Inter Domain Multicast

PIM only work within a single domain. If the requirment to operate multicast application on multiple domain. Then the few features requires - MSDP

### MSDP

- Use to share active source tree between RP
- Once the active source learnt, the RP will sent PIM join the the FHR
- The traffic from source will be forward to RP in the different domain.

### Requirement for Inter-Domain Multicast

- Enable pim on inter-as links
- For MSDP Peering using RP loopbacks and pim should be enable
- Ensure that the RP loopback routeble between the domain so that will pass the RPF check
- Ensure that the source subnet routable from different domain so that it will pass the RPF check
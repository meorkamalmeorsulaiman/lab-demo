# LDP and IGP Sync

## Topology 

```
R1 -- R2 -- R3
|     |     |
R4 -- R5 -- R6
```
- R3 and R6 interface - `GigabitEthernet0/0/0/1`
- Loopback - `10.1.1.x' where x=router

## Steady state 

R6 and R3 point-to-point link state

```
RP/0/0/CPU0:R3#show ospf int gig0/0/0/1
Tue Apr  8 03:57:02.079 UTC

GigabitEthernet0/0/0/1 is up, line protocol is up
  Internet Address 10.3.6.3/24, Area 0
  Label stack Primary label 1 Backup label 3 SRTE label 10
  Process ID CCIE, Router ID 10.1.1.3, Network Type POINT_TO_POINT, Cost: 1
  LDP Sync Enabled, Sync Status: Achieved
  Transmit Delay is 1 sec, State POINT_TO_POINT, MTU 1500, MaxPktSz 1500
  Forward reference No, Unnumbered no,  Bandwidth 1000000
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:06:400
  Index 3/3, flood queue length 0
  Next 0(0)/0(0)
  Last flood scan length is 1, maximum is 2
  Last flood scan time is 0 msec, maximum is 0 msec
  LS Ack List: current length 0, high water mark 1
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 10.1.1.6
  Suppress hello for 0 neighbor(s)
  Multi-area interface Count is 0

RP/0/0/CPU0:R6#show ospf interface gig0/0/0/1
Tue Apr  8 03:56:23.862 UTC

GigabitEthernet0/0/0/1 is up, line protocol is up
  Internet Address 10.3.6.6/24, Area 0
  Label stack Primary label 1 Backup label 3 SRTE label 10
  Process ID CCIE, Router ID 10.1.1.6, Network Type POINT_TO_POINT, Cost: 1
  LDP Sync Enabled, Sync Status: Achieved
  Transmit Delay is 1 sec, State POINT_TO_POINT, MTU 1500, MaxPktSz 1500
  Forward reference No, Unnumbered no,  Bandwidth 1000000
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:02:492
  Index 3/3, flood queue length 0
  Next 0(0)/0(0)
  Last flood scan length is 1, maximum is 2
  Last flood scan time is 0 msec, maximum is 0 msec
  LS Ack List: current length 0, high water mark 5
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 10.1.1.3
  Suppress hello for 0 neighbor(s)
  Multi-area interface Count is 0
```

OSPF Database 

```
RP/0/0/CPU0:R3#show ospf dat router self-originate
Tue Apr  8 03:57:06.059 UTC


            OSPF Router with ID (10.1.1.3) (Process ID CCIE)

                Router Link States (Area 0)

  LS age: 103
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 10.1.1.3
  Advertising Router: 10.1.1.3
  LS Seq Number: 80000006
  Checksum: 0xf95b
  Length: 84
   Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.1.3
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.2
     (Link Data) Router Interface address: 10.2.3.3
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.2.3.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.6
     (Link Data) Router Interface address: 10.3.6.3
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.3.6.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

RP/0/0/CPU0:R3#show ospf database router adv-router 10.1.1.6
Tue Apr  8 04:17:11.796 UTC


            OSPF Router with ID (10.1.1.3) (Process ID CCIE)

                Router Link States (Area 0)

  Routing Bit Set on this LSA
  LS age: 353
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 10.1.1.6
  Advertising Router: 10.1.1.6
  LS Seq Number: 8000000c
  Checksum: 0xcf64
  Length: 84
   Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.1.6
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.5
     (Link Data) Router Interface address: 10.5.6.6
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.5.6.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.3
     (Link Data) Router Interface address: 10.3.6.6
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.3.6.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1
```
Forwarding table

```
RP/0/0/CPU0:R3#show route 10.1.1.6
Tue Apr  8 04:18:02.243 UTC

Routing entry for 10.1.1.6/32
  Known via "ospf CCIE", distance 110, metric 2, type intra area
  Installed Apr  8 04:11:19.700 for 00:06:42
  Routing Descriptor Blocks
    10.3.6.6, from 10.1.1.6, via GigabitEthernet0/0/0/1
      Route metric is 2
  No advertising protos.
RP/0/0/CPU0:R3#show mpls forwarding prefix 10.1.1.6/32
Tue Apr  8 04:19:45.026 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes
Label  Label       or ID              Interface                    Switched
------ ----------- ------------------ ------------ --------------- ------------
24004  Pop         10.1.1.6/32        Gi0/0/0/1    10.3.6.6        1986
RP/0/0/CPU0:R6#show route 10.1.1.3
Tue Apr  8 04:18:25.721 UTC

Routing entry for 10.1.1.3/32
  Known via "ospf CCIE", distance 110, metric 2, type intra area
  Installed Apr  8 04:11:19.860 for 00:07:05
  Routing Descriptor Blocks
    10.3.6.3, from 10.1.1.3, via GigabitEthernet0/0/0/1
      Route metric is 2
  No advertising protos.
RP/0/0/CPU0:R6#show mpls forwarding prefix 10.1.1.3/32
Tue Apr  8 04:20:18.333 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes
Label  Label       or ID              Interface                    Switched
------ ----------- ------------------ ------------ --------------- ------------
24002  Pop         10.1.1.3/32        Gi0/0/0/1    10.3.6.3        980
```
Traceroute from R1 toward R6 and R3 loopbacks and point-to-point link
```
RP/0/0/CPU0:R1#traceroute 10.1.1.6 source loopback 0
Tue Apr  8 04:19:09.538 UTC

Type escape sequence to abort.
Tracing the route to 10.1.1.6

 1  10.1.2.2 [MPLS: Label 24004 Exp 0] 9 msec  0 msec  0 msec
 2  10.2.3.3 [MPLS: Label 24004 Exp 0] 9 msec  9 msec  9 msec
 3  10.3.6.6 9 msec
RP/0/0/CPU0:R1#traceroute 10.1.1.3 source loopback 0
Tue Apr  8 04:19:15.137 UTC

Type escape sequence to abort.
Tracing the route to 10.1.1.3

 1  10.1.2.2 [MPLS: Label 24001 Exp 0] 9 msec  0 msec  0 msec
 2  10.2.3.3 0 msec
RP/0/0/CPU0:R1#traceroute 10.3.6.3 source loopback 0
Tue Apr  8 04:20:54.721 UTC

Type escape sequence to abort.
Tracing the route to 10.3.6.3

 1  10.1.2.2 [MPLS: Label 24006 Exp 0] 0 msec  0 msec  0 msec
 2  10.2.3.3 0 msec
RP/0/0/CPU0:R1#traceroute 10.3.6.6 source loopback 0
Tue Apr  8 04:20:58.760 UTC

Type escape sequence to abort.
Tracing the route to 10.3.6.6

 1  10.1.2.2 [MPLS: Label 24006 Exp 0] 9 msec  0 msec  0 msec
 2  10.2.3.3 0 msec  0 msec  0 msec
 3  10.3.6.6 0 msec
```
## LDP and IGP isn't sync - R3

Interface state on R3 and R6:
 - LDP was disabled on R3 only
```
RP/0/0/CPU0:R3#show mpls ldp interface gigabitEthernet 0/0/0/1
Tue Apr  8 04:37:28.603 UTC
Interface GigabitEthernet0/0/0/1 (0x60)
   VRF: 'default' (0x60000000)
   Disabled:
RP/0/0/CPU0:R3#show ospf int gig0/0/0/1
Tue Apr  8 04:22:18.185 UTC

GigabitEthernet0/0/0/1 is up, line protocol is up
  Internet Address 10.3.6.3/24, Area 0
  Label stack Primary label 0 Backup label 0 SRTE label 0
  Process ID CCIE, Router ID 10.1.1.3, Network Type POINT_TO_POINT, Cost: 1
  LDP Sync Enabled, Sync Status: Not Achieved
  Transmit Delay is 1 sec, State POINT_TO_POINT, MTU 1500, MaxPktSz 1500
  Forward reference No, Unnumbered no,  Bandwidth 1000000
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:09:584
  Index 3/3, flood queue length 0
  Next 0(0)/0(0)
  Last flood scan length is 1, maximum is 2
  Last flood scan time is 0 msec, maximum is 0 msec
  LS Ack List: current length 0, high water mark 1
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 10.1.1.6
  Suppress hello for 0 neighbor(s)
  Multi-area interface Count is 0

RP/0/0/CPU0:R6#show mpls ldp interface gig0/0/0/1
Tue Apr  8 04:39:31.894 UTC
Interface GigabitEthernet0/0/0/1 (0x60)
   VRF: 'default' (0x60000000)
   Enabled via config: LDP interface
RP/0/0/CPU0:R6#show ospf int gig0/0/0/1
Tue Apr  8 04:22:45.263 UTC

GigabitEthernet0/0/0/1 is up, line protocol is up
  Internet Address 10.3.6.6/24, Area 0
  Label stack Primary label 1 Backup label 3 SRTE label 10
  Process ID CCIE, Router ID 10.1.1.6, Network Type POINT_TO_POINT, Cost: 1
  LDP Sync Enabled, Sync Status: Not Achieved
  Transmit Delay is 1 sec, State POINT_TO_POINT, MTU 1500, MaxPktSz 1500
  Forward reference No, Unnumbered no,  Bandwidth 1000000
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:06:579
  Index 3/3, flood queue length 0
  Next 0(0)/0(0)
  Last flood scan length is 1, maximum is 2
  Last flood scan time is 0 msec, maximum is 0 msec
  LS Ack List: current length 0, high water mark 5
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 10.1.1.3
  Suppress hello for 0 neighbor(s)
  Multi-area interface Count is 0
```
Type-1 LSA on R3 and R6:
  - Both R3 and R6 increase the point-to-point link metric

```
RP/0/0/CPU0:R3#show ospf dat router self-originate
Tue Apr  8 04:23:10.042 UTC


            OSPF Router with ID (10.1.1.3) (Process ID CCIE)

                Router Link States (Area 0)

  LS age: 58
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 10.1.1.3
  Advertising Router: 10.1.1.3
  LS Seq Number: 8000000d
  Checksum: 0xb599
  Length: 84
   Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.1.3
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.2
     (Link Data) Router Interface address: 10.2.3.3
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.2.3.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.6
     (Link Data) Router Interface address: 10.3.6.3
      Number of TOS metrics: 0
       TOS 0 Metrics: 65535

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.3.6.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1


RP/0/0/CPU0:R3#show ospf database router adv-router 10.1.1.6
Tue Apr  8 04:23:17.041 UTC


            OSPF Router with ID (10.1.1.3) (Process ID CCIE)

                Router Link States (Area 0)

  Routing Bit Set on this LSA
  LS age: 66
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 10.1.1.6
  Advertising Router: 10.1.1.6
  LS Seq Number: 8000000d
  Checksum: 0x979c
  Length: 84
   Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.1.6
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.5
     (Link Data) Router Interface address: 10.5.6.6
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.5.6.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 10.1.1.3
     (Link Data) Router Interface address: 10.3.6.6
      Number of TOS metrics: 0
       TOS 0 Metrics: 65535

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.3.6.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 1
```

Forwarding table
```
RP/0/0/CPU0:R3#show route 10.1.1.6/32
Tue Apr  8 04:24:18.457 UTC

Routing entry for 10.1.1.6/32
  Known via "ospf CCIE", distance 110, metric 4, type intra area
  Installed Apr  8 04:22:11.276 for 00:02:07
  Routing Descriptor Blocks
    10.2.3.2, from 10.1.1.6, via GigabitEthernet0/0/0/0
      Route metric is 4
  No advertising protos.
RP/0/0/CPU0:R3#show mpls forwarding prefix 10.1.1.6/32
Tue Apr  8 04:23:57.648 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes
Label  Label       or ID              Interface                    Switched
------ ----------- ------------------ ------------ --------------- ------------
24004  24004       10.1.1.6/32        Gi0/0/0/0    10.2.3.2        0

RP/0/0/CPU0:R6#show route 10.1.1.3/32
Tue Apr  8 04:24:57.844 UTC

Routing entry for 10.1.1.3/32
  Known via "ospf CCIE", distance 110, metric 4, type intra area
  Installed Apr  8 04:22:11.466 for 00:02:46
  Routing Descriptor Blocks
    10.5.6.5, from 10.1.1.3, via GigabitEthernet0/0/0/0
      Route metric is 4
  No advertising protos.
RP/0/0/CPU0:R6#show mpls forwarding prefix 10.1.1.3/32
Tue Apr  8 04:25:04.674 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes
Label  Label       or ID              Interface                    Switched
------ ----------- ------------------ ------------ --------------- ------------
24002  24002       10.1.1.3/32        Gi0/0/0/0    10.5.6.5        0
```


Traceroute from R1 toward R6 and R3 loopbacks and point-to-point link
 - Path from R1 to R6 loopback is `R1->R2->R5->R6` instead of `R1->R2->R3->R6`
 - R3 will longer using the point-to-point links to reach R6 loopback
```
RP/0/0/CPU0:R1#traceroute 10.1.1.6 source loopback 0
Tue Apr  8 04:25:32.872 UTC

Type escape sequence to abort.
Tracing the route to 10.1.1.6

 1  10.1.2.2 [MPLS: Label 24004 Exp 0] 9 msec  0 msec  0 msec
 2  10.2.5.5 [MPLS: Label 24004 Exp 0] 9 msec  9 msec  9 msec
 3  10.5.6.6 9 msec  *  9 msec
RP/0/0/CPU0:R1#traceroute 10.1.1.3 source loopback 0
Tue Apr  8 04:25:39.301 UTC

Type escape sequence to abort.
Tracing the route to 10.1.1.3

 1  10.1.2.2 [MPLS: Label 24001 Exp 0] 0 msec  0 msec  0 msec
 2  10.2.3.3 0 msec  *  0 msec
RP/0/0/CPU0:R1#traceroute 10.3.6.3 source loopback 0
Tue Apr  8 04:25:50.780 UTC

Type escape sequence to abort.
Tracing the route to 10.3.6.3

 1  10.1.2.2 [MPLS: Label 24006 Exp 0] 0 msec  0 msec  0 msec
 2  10.2.3.3 0 msec  *  9 msec
RP/0/0/CPU0:R1#traceroute 10.3.6.6 source loopback 0
Tue Apr  8 04:25:57.710 UTC

Type escape sequence to abort.
Tracing the route to 10.3.6.6

 1  10.1.2.2 [MPLS: Label 24006 Exp 0] 9 msec  0 msec  0 msec
 2  10.2.3.3 0 msec  0 msec  0 msec
 3  10.3.6.6 0 msec  *  0 msec
```



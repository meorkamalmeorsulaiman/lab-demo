# Startup 

ACL

```
R1# show ip access-lists 50

IP access list 50
        100 permit ip 1.1.1.1/32 any
        120 permit ip 1.1.1.2/32 any
```

LAG
```
R1# show port-channel summary
Flags:  D - Down        P - Up in port-channel (members)
        I - Individual  H - Hot-standby (LACP only)
        s - Suspended   r - Module-removed
        b - BFD Session Wait
        S - Switched    R - Routed
        U - Up (port-channel)
        p - Up in delay-lacp mode (member)
        M - Not in use. Min-links not met
--------------------------------------------------------------------------------
Group Port-       Type     Protocol  Member Ports
      Channel
--------------------------------------------------------------------------------
3     Po3(SD)     Eth      LACP      Eth1/8(D)
```

Interfaces
```
R1# show run int eth1/7

!Command: show running-config interface Ethernet1/7
!No configuration change since last restart
!Time: Fri May 16 08:59:19 2025

version 9.3(1) Bios:version

interface Ethernet1/7
  description TEST
  switchport access vlan 33

R1# show run int eth1/8

!Command: show running-config interface Ethernet1/8
!No configuration change since last restart
!Time: Fri May 16 08:59:22 2025

version 9.3(1) Bios:version

interface Ethernet1/8
  switchport trunk allowed vlan 3
  channel-group 3 mode active
```

## Running Config

ACL
```
ip access-list 50
  120 permit ip 2.2.2.2/32 any
  130 permit ip 2.2.2.1/32 any
```

LACP - Removed

Interfaces - Cleaned
```
interface Ethernet1/7
interface Ethernet1/8
```

## Expected After Roll using running config files it will update the running configuration

Duplicate ACL detected

```
R1# copy bootflash:///running-config.cfg running-config
Duplicate sequence number
Performing image verification and compatibility check, please wait....
Copy complete, now saving to disk (please wait)...
Copy complete.
```

Check ACL entries
- Not updated as running config when u have duplicated ACL
```
R1# show ip access-lists 50

IP access list 50
        100 permit ip 1.1.1.1/32 any
        120 permit ip 1.1.1.2/32 any
        130 permit ip 2.2.2.1/32 any
```

Check LACP 
- Port-channel not removed

```
R1# show run int po3

!Command: show running-config interface port-channel3
!Running configuration last done at: Fri May 16 09:03:25 2025
!Time: Fri May 16 09:04:54 2025

version 9.3(1) Bios:version

interface port-channel3
  switchport trunk allowed vlan 3
R1# show port-channel summary
Flags:  D - Down        P - Up in port-channel (members)
        I - Individual  H - Hot-standby (LACP only)
        s - Suspended   r - Module-removed
        b - BFD Session Wait
        S - Switched    R - Routed
        U - Up (port-channel)
        p - Up in delay-lacp mode (member)
        M - Not in use. Min-links not met
--------------------------------------------------------------------------------
Group Port-       Type     Protocol  Member Ports
      Channel
--------------------------------------------------------------------------------
3     Po3(SD)     Eth      LACP      Eth1/8(D)
```

Interfaces
- Interface retain the same
```
R1# show run int eth1/7

!Command: show running-config interface Ethernet1/7
!Running configuration last done at: Fri May 16 09:03:25 2025
!Time: Fri May 16 09:05:38 2025

version 9.3(1) Bios:version

interface Ethernet1/7
  description TEST
  switchport access vlan 33

R1# show run int eth1/8

!Command: show running-config interface Ethernet1/8
!Running configuration last done at: Fri May 16 09:03:25 2025
!Time: Fri May 16 09:05:40 2025

version 9.3(1) Bios:version

interface Ethernet1/8
  switchport trunk allowed vlan 3
  channel-group 3 mode active
```

## Using Config Replace

Verify 
```
R1# configure replace bootflash:///running-config.cfg verify-only

Version match between user file and running configuration.
Pre-check for User config PASSED
Collecting Running-Config
Converting to checkpoint file
Generating Rollback Patch
Validating Patch
========================================================
`config t `
`interface Ethernet1/8`
`no channel-group 3 force mode active`
`no switchport trunk allowed vlan`
Syntax error while parsing 'no switchport trunk allowed vlan'

`exit`
Skip non dme command for CR validation
`interface Ethernet1/7`
`no description TEST`
`exit`
Skip non dme command for CR validation
`no interface port-channel3`
`ip access-list 50`
`no 100`
`no 120`
`exit`
Skip non dme command for CR validation
`feature scp-server`
`ip access-list 50`
`130 permit ip 2.2.2.1/32 any`
`120 permit ip 2.2.2.2/32 any`
`interface Ethernet1/7`
`switchport access vlan 1`
`interface Ethernet1/8`
`switchport mode access`
Syntax error while parsing 'switchport mode access'

`switchport trunk allowed vlan 1-4094`
Syntax error while parsing 'switchport trunk allowed vlan 1-4094'

`exit`
Skip non dme command for CR validation
========================================================
Patch validation completed successful
```

Replace configuration
```
R1# configure replace bootflash:///running-config.cfg

Version match between user file and running configuration.
Pre-check for User config PASSED
ADVISORY: Config Replace operation started...
Modifying running configuration from another VSH terminal in parallel
is not recommended, as this may lead to Config Replace failure.

Collecting Running-Config
Generating Rollback patch for switch profile
Rollback Patch is Empty
Collecting Running-Config
Generating Rollback Patch
Executing Rollback Patch
During CR operation,will retain L3 configuration
when vrf member change on interface
Generating Running-config for verification
Generating Rollback Patch

Configure replace completed successfully. Please run 'show config-replace log exec' to see if there is any configuration that requires reload to take effect.
```
```
R1# show config-replace log exec
Operation            : Config-replace to user config
Checkpoint file name : .replace_tmp_6048
Scheme               : tmp
Cfg-replace done By  : admin
Cfg-replace mode     : atomic
Verbose              : disabled
Start Time           : Fri, 09:33:32 16 May 2025
Start Time UTC       : Fri, 09:33:32 16 May 2025
End Time             : Fri, 09:33:45 16 May 2025
End Time UTC         : Fri, 09:33:45 16 May 2025
Rollback Status      : Success

Executing Patch:
----------------
`config t `
`interface Ethernet1/8`
`no channel-group 3 force mode active`
`no switchport trunk allowed vlan`
`exit`
`interface Ethernet1/7`
`no description TEST`
`exit`
`no interface port-channel3`
`ip access-list 50`
`no 100`
`no 120`
`exit`
`feature scp-server`
`ip access-list 50`
`130 permit ip 2.2.2.1/32 any`
`120 permit ip 2.2.2.2/32 any`
`interface Ethernet1/7`
`switchport access vlan 1`
`interface Ethernet1/8`
`switchport mode access`
`switchport trunk allowed vlan 1-4094`
`exit`
```

## Validate Config Replace

ACL
- Replaced
```
R1# show ip access-lists 50

IP access list 50
        120 permit ip 2.2.2.2/32 any
        130 permit ip 2.2.2.1/32 any
```
LACP
- Removed
```
R1# show port-channel summary
R1# show run int po3
                   ^
Invalid range at '^' marker.
```
Interfaces
- Cleaned up
```
R1# show run int eth1/7

!Command: show running-config interface Ethernet1/7
!Running configuration last done at: Fri May 16 09:33:40 2025
!Time: Fri May 16 09:36:04 2025

version 9.3(1) Bios:version

interface Ethernet1/7

R1# show run int eth1/8

!Command: show running-config interface Ethernet1/8
!Running configuration last done at: Fri May 16 09:33:40 2025
!Time: Fri May 16 09:36:06 2025

version 9.3(1) Bios:version

interface Ethernet1/8
```








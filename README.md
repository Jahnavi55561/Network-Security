# Secure Campus Network Using Cisco Packet Tracer

A mini project that designs a small two-subnet campus network and
applies basic security configurations to Cisco routers and switches
using **Cisco Packet Tracer**.

## Project Overview

The network contains one Cisco 2911 router, two Cisco 2960 switches, and
six PCs. The router connects two IPv4 LANs:

-   `192.168.10.0/24` --- connected through switch S1
-   `192.168.20.0/24` --- connected through switch S2

The project demonstrates router and switch configuration, IP addressing,
console and privileged-mode security, SSH remote administration,
password obfuscation, switch port security, and network verification.

## Objectives

-   Build a two-LAN campus network in Cisco Packet Tracer.
-   Configure router interfaces and switch management IP addresses.
-   Configure console passwords and privileged EXEC protection.
-   Enable SSH version 2 for remote device administration.
-   Enable password encryption/obfuscation in device configurations.
-   Restrict PC-facing switch ports using sticky MAC learning and port
    security.
-   Test connectivity and verify the security settings.

## Tools and Devices

  Component     Quantity / Model
  ------------- -------------------------
  Router        1 × Cisco 2911 (R1)
  Switches      2 × Cisco 2960 (S1, S2)
  End devices   6 × PCs (PC1--PC6)
  Cables        Copper straight-through
  Simulator     Cisco Packet Tracer

## Network Topology

``` text
                         R1
                    Cisco 2911
                     /       \
                  G0/0       G0/1
                   /           \
                 S1             S2
             Cisco 2960     Cisco 2960
              /  |  \         /  |  \
            PC1 PC2 PC3     PC4 PC5 PC6

       LAN 1: 192.168.10.0/24
       LAN 2: 192.168.20.0/24
```

### Physical Connections

  From       To
  ---------- ---------
  R1 G0/0    S1 G0/1
  R1 G0/1    S2 G0/1
  S1 Fa0/1   PC1
  S1 Fa0/2   PC2
  S1 Fa0/3   PC3
  S2 Fa0/1   PC4
  S2 Fa0/2   PC5
  S2 Fa0/3   PC6

## IP Addressing Plan

  Device   Interface   IP address        Subnet mask       Default gateway
  -------- ----------- ----------------- ----------------- -----------------
  R1       G0/0        `192.168.10.1`    `255.255.255.0`   ---
  R1       G0/1        `192.168.20.1`    `255.255.255.0`   ---
  S1       VLAN 1      `192.168.10.2`    `255.255.255.0`   `192.168.10.1`
  S2       VLAN 1      `192.168.20.2`    `255.255.255.0`   `192.168.20.1`
  PC1      NIC         `192.168.10.10`   `255.255.255.0`   `192.168.10.1`
  PC2      NIC         `192.168.10.11`   `255.255.255.0`   `192.168.10.1`
  PC3      NIC         `192.168.10.12`   `255.255.255.0`   `192.168.10.1`
  PC4      NIC         `192.168.20.10`   `255.255.255.0`   `192.168.20.1`
  PC5      NIC         `192.168.20.11`   `255.255.255.0`   `192.168.20.1`
  PC6      NIC         `192.168.20.12`   `255.255.255.0`   `192.168.20.1`

## Configuration Summary

The supplied Packet Tracer project/report covers these configuration
tasks:

1.  Configure R1 interfaces with `192.168.10.1/24` and
    `192.168.20.1/24`.
2.  Set router and switch hostnames and configure console and enable
    secrets.
3.  Enable `service password-encryption` to obfuscate applicable line
    passwords in the configuration.
4.  Configure SSH version 2 using the domain name `campus.local`, a
    local admin account, RSA keys, and SSH-only VTY access.
5.  Configure S1 and S2 management IP addresses on VLAN 1 and their
    default gateways.
6.  Enable port security on PC-facing ports Fa0/1--Fa0/3, allowing one
    sticky MAC address per port and using shutdown as the violation
    action.
7.  Configure the six PCs with the IP addresses shown above.
8.  Shut down unused switch ports Fa0/4--Fa0/24 and save device
    configurations.

### Example Verification Commands

Run the relevant commands in the router or switch CLI:

``` text
show ip interface brief
show ip ssh
show running-config
show port-security
show port-security address
show port-security interface fastEthernet 0/1
show mac address-table
copy running-config startup-config
```

## How to Run the Project

1.  Install and open **Cisco Packet Tracer**.
2.  Open the supplied `.pkt` project file.
3.  Inspect the topology and confirm the router, switches, PCs, and
    cable connections.
4.  Open each PC and select **Desktop → IP Configuration** to inspect or
    enter its IP address, subnet mask, and default gateway.
5.  Open the router and switch **CLI** tabs to inspect or apply the
    configurations described in this README.
6.  Use the PC **Desktop → Command Prompt** to run the ping tests below.
7.  Verify SSH access and port-security settings using the commands
    below.
8.  Save the Packet Tracer file after making any changes.

## Testing and Verification

Run these tests in Packet Tracer and record the actual observed results:

### Connectivity tests

From PC1:

``` text
ping 192.168.10.1
ping 192.168.20.10
ping 192.168.20.11
ping 192.168.20.12
```

### SSH tests

From PC1:

``` text
ssh -l admin 192.168.10.2
ssh -l admin 192.168.10.1
```

From PC4:

``` text
ssh -l admin 192.168.20.2
```

### Port-security checks

``` text
show port-security
show port-security address
show port-security interface fastEthernet 0/1
```

To demonstrate a violation, connect PC1 to S1 Fa0/1 and generate traffic
so the switch learns its MAC address. Disconnect PC1 and connect a
different PC to that same port. With the configured maximum of one
secure MAC address and violation mode set to shutdown, the switch should
detect the violation and place the port into an error-disabled/shutdown
state.

To recover the interface:

``` text
configure terminal
interface fastEthernet 0/1
shutdown
no shutdown
exit
```

**Important:** Do not mark a test as successful until its output has
been observed in Packet Tracer. The project report's screenshots show
the topology, selected interface/status output, and a port-security
violation message; they do not independently establish that every ping
and SSH test passed.

## Security Features

  -----------------------------------------------------------------------
  Feature                             Purpose
  ----------------------------------- -----------------------------------
  Console password                    Protects local console access

  `enable secret`                     Protects privileged EXEC mode

  `service password-encryption`       Obfuscates applicable plaintext
                                      line passwords in the
                                      configuration; it is not strong
                                      cryptographic protection

  SSH version 2                       Provides encrypted remote
                                      administration

  Sticky MAC learning                 Learns and retains the permitted
                                      MAC address on a secured port

  Port-security maximum 1             Limits a PC-facing port to one
                                      secure MAC address

  Violation mode shutdown             Shuts down the port when a
                                      port-security violation occurs

  Shutdown of unused ports            Reduces opportunities for
                                      unauthorized devices to connect
  -----------------------------------------------------------------------

## Repository Contents

-   `README.md` --- project overview, topology, addressing,
    configuration summary, and testing guide.
-   `miniproject (1).pkt` --- Cisco Packet Tracer project file (include
    this file in the repository if you want others to open the
    simulation).
-   Project report --- documentation and implementation evidence (if
    included).

## Notes

-   The example credentials shown in the project brief are for the lab
    demonstration only. Change them before using any similar
    configuration outside a classroom/lab environment.
-   RSA key size options can depend on the Packet Tracer device/IOS
    simulation. Use a supported size when prompted.
-   This is a simulated educational network project, not a production
    campus-network security design.

## Conclusion

This mini project demonstrates a small two-subnet campus network and
combines basic IP connectivity with router and switch access protection,
SSH administration, password obfuscation, and switch port security. It
provides hands-on practice with Cisco Packet Tracer configuration and
verification commands.

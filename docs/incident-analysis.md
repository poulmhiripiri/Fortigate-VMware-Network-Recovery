# Incident analysis

## Incident statement

The owner reported that VMware Workstation network adapter settings had reset and the FortiGate lab was unavailable. A usable pre-reset VMware network inventory was not available in the troubleshooting exchange. The firewall configuration remained accessible through the VM console.

## Scope and impact

The affected services were connectivity within the FortiGate lab, management access from the Windows host, the FortiGate upstream path, and the dependent lab VMs. The owner confirmed that the VMs were also not working because they depended on the VMnet segments to reach FortiGate for DHCP addresses and used FortiGate as their internet gateway.

This was a shared network dependency failure: loss of the virtual segment could prevent DHCP exchange and gateway reachability, affecting multiple guest workloads without requiring a fault in each guest. The captured configuration identifies DHCP on port3; exact membership and recovery status of every affected VM were not supplied. Ubuntu was the client whose address and route were inspected. No production impact or outage duration was established.

## Evidence chronology

The stages below represent the troubleshooting order on 10 October 2026; exact change timestamps and packet-capture evidence were not collected.

| Stage | Observation | Decision |
|---|---|---|
| Initial inspection | FortiGate adapters referenced VMnet2/8/3; VMnet2 and VMnet3 absent from editor | Inspect firewall before modifying it |
| Routing inspection | Networks 192.168.8.0/24, 192.168.253.0/24 and 192.168.21.0/24; default via .253.2 | Use route/interface records to recover subnet design |
| Interface inspection | Management .8.10, WAN .253.15, LAN .21.10 | Restore matching virtual segments |
| DHCP inspection | DHCP entry on port3 advertises gateway .21.10 | Avoid VMware DHCP competition on LAN |
| Host verification | VMnet2 .8.1, VMnet8 .253.1, VMnet3 .21.1 | Proceed to service tests |
| Recovery validation | Management webpage and SSH available; upstream pings successful | Management and WAN restored |
| Client inspection | Ubuntu .21.40/24 and default gateway .21.10 | Run final LAN forwarding and DNS tests |

## Cause assessment

**Observed fault:** missing VMware virtual networks plus a VMnet8 subnet inconsistent with the FortiGate WAN. **Reported trigger:** VMware networking reset. **Unproven initiating cause:** why that reset occurred. The FortiGate also displayed a filesystem warning following an unsafe reboot; no causal link to the VMware reset was established.

## Alternatives considered by the reasoning

Changing FortiGate addresses to match VMware defaults would alter the preserved design and could invalidate policies, DHCP settings and client configuration. Reconstructing VMware networks was the smaller intervention because the firewall retained coherent addressing and routing records.

Retaining adapter instances also avoids adding uncertainty about NIC identity or ordering. The initial port mapping was an inference; match MAC addresses if recovery does not validate the expected path.

## Residual work

- Capture final Ubuntu gateway, external IP, DNS and HTTPS tests.
- Confirm the DHCP pool: supplied output ends before start/end addresses. Avoid overlap with host .21.1 and FortiGate .21.10.
- Confirm NAT gateway .253.2 in Virtual Network Editor and record a post-recovery editor screenshot.
- Compare VMware adapter MACs to FortiGate port MACs and record the correspondence.
- Back up FortiGate configuration and plan a separate filesystem consistency check. The console indicates that a disk scan reboots the appliance; do not treat it as a read-only network test.
- Save the recovered network inventory and backups after the complete lab is verified.

## Professional takeaway

Operational resilience requires more than following a diagram. When documentation is incomplete, an engineer must extract design intent from surviving state, separate evidence from assumptions, make controlled changes and prove restoration at the service level. This case demonstrates that workflow while recording the limits of the available evidence.

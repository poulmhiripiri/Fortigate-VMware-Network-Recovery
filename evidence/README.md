# Evidence index

These selected original screenshots were supplied by the lab owner. They have been renamed for navigation, not edited. Some show appliance identifiers. Browser screenshots, Windows host account output and VM settings containing personal paths are excluded; relevant observations are transcribed in the documentation.

| File | Original attachment | What it shows |
|---|---|---|
| [01-vmware-before.png](01-vmware-before.png) | image(7).png | Only VMnet0/1/8 present; VMnet8 subnet 192.168.21.0/24 |
| [02-fortigate-interfaces.png](02-fortigate-interfaces.png) | image(9).png | Management, WAN and LAN IPs and aliases |
| [03-fortigate-default-route.png](03-fortigate-default-route.png) | image(10).png | Gateway 192.168.253.2 through port2 |
| [04-fortigate-dhcp.png](04-fortigate-dhcp.png) | image(20261010-134557).png | DHCP on port3 with gateway 192.168.21.10; range is incomplete |
| [05-recovery-wan-tests.png](05-recovery-wan-tests.png) | image(20261010-140856).png | Successful SSH and pings to NAT gateway, 1.1.1.1 and 8.8.8.8; filesystem warning |

Other supplied evidence, documented as observations: Windows host VMnet addresses .8.1/.253.1/.21.1; management HTTP login page; Ubuntu ens33 dynamic .21.40/24 and default gateway .21.10. The Ubuntu screenshot included a local username and was not bundled.

## Evidence limits

No pre-failure working baseline, exact initiating cause, full DHCP pool, MAC comparison, post-recovery Virtual Network Editor screenshot, LAN forwarding test output or DNS/HTTPS results were supplied. Configuration text is a partial transcription and is not an executable restore backup.

# Recovery runbook

## 1. Preserve and inspect

Use the VMware console when network management is unavailable. Save a FortiGate configuration backup when possible and record current VMware network selections. Do not use Restore Defaults again during reconstruction. Preserve VM network adapter instances and MAC addresses.

Run these read-only FortiGate commands:

```text
show system interface
show router static
get router info routing-table all
show system dhcp server
```

Press Space at `--More--` to capture complete output. Capture only the relevant excerpts for a public case study; full backups can contain secrets.

For uncertain adapter mappings, compare the VMware adapter's Advanced MAC address with:

```text
diagnose hardware deviceinfo nic port1
diagnose hardware deviceinfo nic port2
diagnose hardware deviceinfo nic port3
```

This MAC comparison was recommended as a verification method; output was not captured during this case.

## 2. Restore virtual networks

Shut down affected guests cleanly before changing the virtual network definitions. Open Edit → Virtual Network Editor and use Change Settings if administrative access is required.

| VMnet | Type | Subnet | Mask | VMware DHCP | Host adapter |
|---|---|---|---|---|---|
| VMnet2 | Host-only | 192.168.8.0 | 255.255.255.0 | Disabled for this restoration | Enabled for management |
| VMnet8 | NAT | 192.168.253.0 | 255.255.255.0 | Disabled for static WAN | Present in observed host output |
| VMnet3 | Host-only | 192.168.21.0 | 255.255.255.0 | Disabled; FortiGate DHCP configured | Present in observed host output |

Set VMnet8 NAT Settings gateway to 192.168.253.2. Host VMnet8 address 192.168.253.1 is not the NAT gateway. Apply changes.

Retain FortiGate VM connections: Network Adapter → Custom VMnet2; Network Adapter 2 → Custom VMnet8; Network Adapter 3 → Custom VMnet3. Enable Connect at power on and check Connected while running.

Windows host adapter inventory: VMnet2 192.168.8.1/24, VMnet8 192.168.253.1/24, VMnet3 192.168.21.1/24. Leave gateways blank on those adapters; retain the host's normal internet route. Verify addresses are unused and excluded from DHCP pools.

## 3. Test management and WAN

Power on FortiGate. From Windows, browse to https://192.168.8.10 and connect using SSH if needed. The captured webpage used HTTP, while SSH authentication was confirmed. HTTPS was configured on port1 but its browser result was not captured.

Do not use ping failure on port1 as conclusive evidence: its captured allowaccess list includes https, ssh and http, but not ping.

From FortiGate:

```text
execute ping 192.168.253.2
execute ping 1.1.1.1
execute ping 8.8.8.8
```

These three tests passed in the recorded incident. They establish upstream reachability from the firewall itself, not LAN policy forwarding.

## 4. Restore and validate dependent VMs

Inventory each affected VM's intended VMnet attachment. Reconnect each to its correct recovered segment and confirm Connected and Connect at power on. For guests that depend on FortiGate DHCP, renew or re-establish the lease and verify the address, mask, gateway and DNS settings. Guests on the documented LAN should use VMnet3 and gateway 192.168.21.10; do not assume every VM in the inventory belongs to that segment.

Validate each dependent workload after the shared network is restored. A recovered firewall WAN path alone does not prove that all guest services are restored.

### Test a LAN guest

Attach the LAN guest to Custom VMnet3. Preserve its intended static addressing, or use DHCP where appropriate. A Windows guest using DHCP can run `ipconfig /renew` followed by `ipconfig /all`. Ubuntu in this case already showed a dynamic address; no renewal command was captured.

Inside Ubuntu:

```bash
ip address show ens33
ip route
ping -c 4 192.168.21.10
ping -c 4 1.1.1.1
resolvectl query example.com
curl -I https://example.com
```

If resolvectl is unavailable, use `getent hosts example.com`. If curl is unavailable, test HTTPS with the guest browser. Expected subnet is 192.168.21.0/24 and default gateway is 192.168.21.10. Observed client address was 192.168.21.40/24.

## 5. Branch on failures

| Failure | Next checks |
|---|---|
| Management page/SSH unavailable | VMnet2 attachment, host address, Connected state, MAC mapping and interface access settings |
| NAT gateway ping fails | VMnet8 subnet and gateway, port2 link/address, VMware NAT service and MAC mapping |
| NAT gateway works, external IP fails | Host internet connectivity, VMware NAT service and FortiGate default route |
| LAN gets no usable DHCP address | VMnet3 attachment, guest link, complete FortiGate DHCP range/status, address pool exhaustion and competing DHCP |
| LAN cannot ping .21.10 | Guest address/mask, VMnet3 attachment and port3 link/access settings |
| LAN gateway works, external IP fails | Inspect existing port3-to-port2 policy, route and source NAT; do not add a broad rule blindly |
| External IP works, names fail | DHCP-provided DNS, FortiGate system DNS if advertised, DNS reachability and policy |
| DNS works, HTTPS fails | Inspect HTTPS policy/security profiles, proxy configuration, certificates and guest clock |

For a client forwarding failure, gather `show firewall policy` and the relevant route information before changing policies. No policy modification or packet debug was captured in this recovery.

## 6. Close the incident

Record each validation result, export VMware virtual network settings, retain FortiGate backups securely and update the network inventory. Address the filesystem warning separately with a backup and planned downtime. Full LAN restoration is only confirmed after the remaining client tests pass.

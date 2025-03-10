---
title: network solution
date: 2025-03-10 10:04:37
tags: solution
categories: network
---

# make bond

Bonding Mode

| Mode                    | Max Speed (Single Flow) | Max Speed (Multiple Flows)        | Key Feature                                                  |
| ----------------------- | ----------------------- | --------------------------------- | ------------------------------------------------------------ |
| mode=0 (round-robin)    | Sum of all slaves       | Sum of all slaves                 | Best for maximizing bandwidth, may cause out-of-order packets. |
| mode=1 (active-backup)  | One interface’s speed   | One interface’s speed             | Best for redundancy, no speed gain.                          |
| mode=2 (balance-xor)    | One interface’s speed   | Sum of all slaves                 | Good for performance; switch support required.               |
| mode=4 (802.3ad - LACP) | One interface’s speed   | Sum of all slaves                 | Efficient load balancing for multiple flows; requires switch support. |
| mode=5 (balance-tlb)    | One interface’s speed   | Sum of all slaves (outgoing only) | Adaptive transmit load balancing.                            |
| mode=6 (balance-alb)    | One interface’s speed   | Sum of all slaves                 | Adaptive load balancing without switch support.              |

>  assume make ib7s400p0 to bond1, which have assigned  ip 172.30.12.46 with ib7s400p0

**optional**: Load the Bonding Kernel Module

Ensure the bonding driver is loaded:

```
sudo modprobe bonding
```

To persist this across reboots:

```
echo "bonding" | sudo tee /etc/modules-load.d/bonding.conf
```

**optional**: Remove the IP Address from ib7s400p0

Since the bond interface will carry the IP, you must remove the IP from ib7s400p0 first:

```shell
sudo ip addr del 172.30.12.46/24 dev ib7s400p0
```

**Step 2: Create the Bond Interface (bond1)**

Create the bond interface:

```
sudo ip link add bond1 type bond
```

**Step 3: Configure Bonding Mode**

For your use case, you can set mode=active-backup (best for redundancy with one NIC now) or mode=802.3ad (if planning for LACP in the future).

**Set Active-Backup Mode (Recommended for 1 NIC Now)**

```shell
echo "active-backup" | sudo tee /sys/class/net/bond1/bonding/mode
```

**OR Set 802.3ad Mode (If Future Expansion is Planned)**

```shell
echo "802.3ad" | sudo tee /sys/class/net/bond1/bonding/mode
```

**Step 4: Add ib7s400p0 as a Slave**

1. Remove any IP address from ib7s400p0 (if it has one):

```shell
sudo ip addr flush dev ib7s400p0
```

2. Add ib7s400p0 to bond1:

```shell
# sudo ip link set ib7s400p0 down
sudo ip link set ib7s400p0 master bond1
sudo ip link set ib7s400p0 up
```

3. Bring bond1 up:

```shell
sudo ip link set bond1 up
```

**Step 5: Assign an IP Address**

If using a **static IP**:

```shell
sudo ip addr add 172.30.12.46/24 dev bond1
```

Or if using **DHCP**:

```shell
sudo dhclient bond1
```

Try to force traffic through bond1 by removing the direct route through ib7s400p0

```shell
sudo ip route del 172.30.12.0/24 dev ib7s400p0
```

**Step 6: Persistent Configuration (Rocky 9 / RHEL 9) (optional)**

To ensure the bond configuration persists after reboot:

1. Create /etc/sysconfig/network-scripts/ifcfg-bond1

```
DEVICE=bond1
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=dhcp      # Or use 'static' if assigning a static IP
ONBOOT=yes
BONDING_OPTS="mode=active-backup miimon=100"
```

2. Create /etc/sysconfig/network-scripts/ifcfg-ib7s400p0

```
DEVICE=ib7s400p0
MASTER=bond1
SLAVE=yes
BOOTPROTO=none
ONBOOT=yes
```

3. Restart NetworkManager to apply changes:

```
sudo systemctl restart NetworkManager
```

**Step 7: Verify Configuration**

1. Check bond details:

```
cat /proc/net/bonding/bond1
```

ib7s400p0 should now appear as a **Slave Interface**.

2. Confirm IP address and route:

```
ip a
ip route
```

3. Test connectivity:

```
ping 172.30.12.47 # other same bond addr
```

**Step 8: Optional - Test Failover (For active-backup Mode)**

​	1.	Temporarily bring down ib7s400p0:

```
sudo ip link set ib7s400p0 down
```

​	2.	Check bond1 status:

```
cat /proc/net/bonding/bond1
```

In active-backup mode, bond1 should remain active (even though ib7s400p0 is down).

In 802.3ad mode, bond1 would go down since no alternate NIC exists yet.

**Summary**

Use **active-backup** mode if ib7s400p0 is the only slave (recommended now).

Use **802.3ad** if planning to add more NICs for higher throughput in the future.

Ensure /etc/sysconfig/network-scripts/ configs are properly set for persistence.

# Troubleshooting Notes — Jeremy's IT Lab CCNA Mega Lab

Selected issues I encountered while configuring the guided Packet Tracer activity.

## 1. PC1 could not ping SRV1

**Symptoms:** PC1 could ping `10.5.0.1` (server VLAN gateway) but not `10.5.0.4` (SRV1).

**Checks:**

```text
PC1> ping 10.5.0.1
PC1> ping 10.5.0.4
DSW-A1# show ip route 10.5.0.0
```

OSPF had a route toward `10.5.0.0/24`, and the VLAN gateway was reachable. I checked the server's own IP configuration instead of changing the routing protocol.

**Root cause:** SRV1 had `10.0.5.4` instead of `10.5.0.4`.

**Fix:** Configure SRV1 as `10.5.0.4/24`, gateway `10.5.0.1`.

**Lesson:** Verify endpoint IP, mask, and gateway before modifying working routing protocols.

## 2. HSRP Active/Standby mismatch — DSW-B1

**Symptoms:** DSW-B1 VLAN 99 showed priority **100** and state **Standby**, though the activity required it to be Active with priority 105.

**Checks:**

```cisco
show running-config | section interface Vlan99
show standby brief
```

**Fix:**

```cisco
interface vlan 99
 standby 1 priority 105
```

Preemption had already been configured. Save and verify the Active role using `show standby brief`.

## 3. Missing PortFast and BPDU Guard — ASW-A1

**Symptoms:** Packet Tracer assessment flagged both STP security commands on ASW-A1.

**Check:** Inspected the `startup-config` for interface `FastEthernet0/1`.

**Fix:**

```cisco
interface f0/1
 spanning-tree portfast
 spanning-tree bpduguard enable
```

**Lesson:** Apply host-facing STP settings on the correct port and save the configuration before checking the assessment.

## 4. Missing OSPF passive interface — DSW-A1

**Symptoms:** Activity assessment reported `passive-interface Vlan40` as incorrect.

**Check:**

```cisco
show running-config | include passive-interface
```

**Fix:**

```cisco
router ospf 1
 passive-interface vlan 40
```

**Lesson:** A passive OSPF interface continues advertising its connected network but does not form adjacencies on it.

## 5. Unsaved configurations and enable secret typo

Some assessment items were shown under **Startup Configuration**. I checked running versus startup configurations and saved corrections:

```cisco
copy running-config startup-config
```

An enable-secret spelling mistake was also corrected across affected devices before reassessment.

## Final validation

**Packet Tracer score: 1,753 / 1,753 (100%).** See the [README](../README.md) for screenshots and technical coverage.

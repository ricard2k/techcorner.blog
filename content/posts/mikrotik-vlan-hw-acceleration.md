+++
title = 'Mikrotik VLAN Hardware Acceleration'
date = 2026-01-13T11:11:44+00:00
draft = false
+++

## Introduction

When configuring VLANs on MikroTik devices, understanding hardware acceleration is crucial for maintaining network performance. Without proper hardware offloading, all VLAN traffic gets processed by the device's CPU, which can quickly become a bottleneck in high-throughput environments. This leads to reduced throughput, increased latency, and potential packet loss—especially problematic for networks handling significant traffic volumes or requiring low-latency applications.

Hardware acceleration leverages the switching chip's capabilities to handle VLAN tagging, filtering, and forwarding operations at wire speed, bypassing the CPU entirely. This means your network can achieve near line-rate performance even with complex VLAN configurations, making the difference between a 100 Mbps CPU-limited setup and a multi-gigabit wire-speed network.

## The Evolution: From `/interface/switch` to `/interface/bridge`

MikroTik's approach to hardware acceleration has evolved significantly over the years, creating two distinct configuration paradigms that often confuse network administrators.

### Legacy Method: `/interface/switch`

Older MikroTik devices equipped with legacy switching chips—such as the Atheros IPQ and Qualcomm QCA series—relied exclusively on the `/interface/switch` menu for hardware-accelerated switching and VLAN configuration. These chips required specific configuration through switch rules and VLAN tables to enable hardware offloading. While functional, this method had several limitations:

- Configuration was chip-specific and not standardized across different models
- Limited integration with RouterOS's broader feature set
- Less intuitive for administrators familiar with standard bridging concepts
- Difficult to troubleshoot and verify hardware offloading status

Devices like the RB450G, RB750, and older hEX models fall into this category.

### Modern Method: `/interface/bridge`

Newer MikroTik devices featuring modern switching chips—including the Marvell 88E6xxx, Marvell 98DX, and Realtek RTL839x series—use the unified `/interface/bridge` menu for both software and hardware-accelerated switching. This represents a significant improvement in RouterOS's architecture:

- **Unified Configuration**: VLANs are configured through standard bridge VLAN filtering, eliminating the need for separate switch menus
- **Automatic Hardware Offloading**: When properly configured, the bridge automatically offloads supported operations to the switching chip
- **Better Integration**: Seamless integration with other RouterOS features like firewall, QoS, and routing
- **Transparent Operation**: The same configuration works whether hardware offloading is available or not (falling back to CPU processing)

Modern devices like the RB5009, CCR2004, and recent hEX/hAP models utilize this approach. The key is enabling bridge VLAN filtering and ensuring the `hw` flag appears next to bridge ports, indicating successful hardware offloading.

Understanding which method your device requires is essential for proper VLAN configuration and optimal performance.

## Configuration Comparison

| Operation | Bridge | Switch |
|-----------|--------|--------|
| Enabling VLAN | `/interface/bridge` set vlan-filtering=yes | `/interface/switch` configuration menu |
| Conecting router processor to an VLAN | Bridge VLAN interface or VLAN interface on bridge | Switch VLAN interface or separate VLAN interface |
| Filtering VLANs in the bidge | `/interface/bridge/vlan` with tagged/untagged ports | Not applicable (per-port filtering) |
| FIltering VLANs in the port | `/interface/bridge/vlan` pvid and frame-types | `/interface/switch/port` with vlan-mode settings |
| Encapsulating/decapsulating VLANs in the port | Bridge port pvid and `/interface/bridge/vlan` configuration | `/interface/switch/port` and `/interface/switch/vlan` configuration |
|Wultiple WiFI SSIDs separated BY VLAN|||



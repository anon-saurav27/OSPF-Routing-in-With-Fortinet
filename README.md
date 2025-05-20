# OSPF-Routing-in-With-Fortinet
# 🛡️ Routing by OSPF in Fortinet - Lab Project

This project demonstrates **dynamic routing using OSPF** (Open Shortest Path First) in a **Fortinet firewall** setup, integrated with other routing protocols like **RIP v2** and **EIGRP**. The objective is to simulate real-world multi-protocol routing environments using OSPF as the backbone protocol.

---

## 📊 Network Topology

![Routing by OSPF in Fortinet](Screenshot%202025-05-20%20114741.png)

---

## 🧠 Project Objectives

- Configure **OSPF with multiple areas** (Area 0, 1, 2, 3, 4)
- Implement **route redistribution** between OSPF, EIGRP, and RIP v2
- Use **Fortinet firewall** as the central routing device
- Establish **end-to-end connectivity** between all network segments
- Provide **management access** through a dedicated interface

---

## 📚 Theory & Concepts

### 🔹 OSPF (Open Shortest Path First)
OSPF is a link-state routing protocol used to dynamically learn routes and converge quickly in large networks. It supports hierarchical design with areas to optimize performance.

### 🔹 EIGRP (Enhanced Interior Gateway Routing Protocol)
A Cisco proprietary hybrid routing protocol offering quick convergence and support for VLSM and summarization.

### 🔹 RIP v2 (Routing Information Protocol)
A distance-vector routing protocol that supports CIDR and uses hop count as its metric. Suitable for small networks.

### 🔹 Route Redistribution
The process of sharing routing information between different routing protocols (e.g., from RIP to OSPF) through the Fortinet firewall.

---

## 🧩 Network Segmentation

| Router | Protocol | Area/Group | Networks Advertised |
|--------|----------|------------|----------------------|
| R1     | OSPF     | Area 2     | 172.10.1.0/24 - 172.10.5.0/24 |
| R2     | EIGRP 20 | -          | 172.20.1.0/24 - 172.20.5.0/24 |
| R3     | OSPF     | Area 3     | 172.30.1.0/24 - 172.30.5.0/24 |
| R4     | RIP v2   | -          | 172.40.1.0/24 - 172.40.5.0/24 |
| Fortinet | OSPF   | Areas 0,1,3,4 | Redistribution point |

---

## ⚙️ Configuration Summary

### 🔸 Fortinet Firewall

```bash
# Enable OSPF
config router ospf
    set router-id 1.1.1.1
end

# Configure OSPF Areas
config router ospf
    config area
        edit 0.0.0.0
        next
        edit 0.0.0.1
        next
        edit 0.0.0.3
        next
        edit 0.0.0.4
        next
    end
end

# OSPF Interface assignments
config router ospf
    config ospf-interface
        edit "port1"
            set area 0.0.0.1
        next
        edit "port2"
            set area 0.0.0.0
        next
        edit "port3"
            set area 0.0.0.3
        next
        edit "port4"
            set area 0.0.0.4
        next
    end
end

 Redistribution Example (RIP & EIGRP to OSPF)

# Redistribute RIP
config router ospf
    config redistribute "rip"
        set status enable
    end
end

# Redistribute EIGRP
config router ospf
    config redistribute "eigrp"
        set status enable
    end
end


🧪 Testing & Validation
✅ Ping tests between R1, R2, R3, and R4 LANs
✅ Routing table verification on Fortinet (get router info routing-table all)
✅ OSPF neighbor relationships (get router ospf neighbor)
✅ Traceroute to verify route paths
✅ RIP and EIGRP debug logs


🛠 Tools Used
GNS3 / EVE-NG for network emulation

FortiGate VM

Cisco IOS routers

Wireshark for packet capture and analysis

Terminal or CLI for configuration




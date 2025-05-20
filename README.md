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
 # 📟 Router R1 – OSPF Area 2 (Connected to Fortinet via Area 1)
conf t
hostname R1

interface e0/0
 ip address 192.168.5.1 255.255.255.0
 no shutdown

router ospf 1
 router-id 1.1.1.1
 network 192.168.5.0 0.0.0.255 area 1
 network 172.10.1.0 0.0.0.255 area 2
 network 172.10.2.0 0.0.0.255 area 2
 network 172.10.3.0 0.0.0.255 area 2
 network 172.10.4.0 0.0.0.255 area 2
 network 172.10.5.0 0.0.0.255 area 2
end


# 📟 Router R2 – EIGRP AS 20 (Connected to Fortinet via Area 0)
conf t
hostname R2

interface e0/0
 ip address 192.168.2.1 255.255.255.0
 no shutdown

router eigrp 20
 network 172.20.1.0 0.0.0.255
 network 172.20.2.0 0.0.0.255
 network 172.20.3.0 0.0.0.255
 network 172.20.4.0 0.0.0.255
 network 172.20.5.0 0.0.0.255
 network 192.168.2.0 0.0.0.255
 no auto-summary
end


# 📟 Router R3 – OSPF Area 3

conf t
hostname R3

interface e0/0
 ip address 192.168.3.1 255.255.255.0
 no shutdown

router ospf 1
 router-id 3.3.3.3
 network 192.168.3.0 0.0.0.255 area 3
 network 172.30.1.0 0.0.0.255 area 3
 network 172.30.2.0 0.0.0.255 area 3
 network 172.30.3.0 0.0.0.255 area 3
 network 172.30.4.0 0.0.0.255 area 3
 network 172.30.5.0 0.0.0.255 area 3
end



# 📟 Router R4 – RIP v2 (Connected to Fortinet via Area 4)
conf t
hostname R4

interface e0/0
 ip address 192.168.4.1 255.255.255.0
 no shutdown

router rip
 version 2
 network 192.168.4.0
 network 172.40.0.0
 no auto-summary
end

## 🛡 Fortinet Firewall – OSPF Core + Redistribution
# 1️⃣ Configure OSPF & Router ID

config router ospf
 set router-id 10.10.10.1
end

# 2️⃣ Define OSPF Areas
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

# 3️⃣ Assign Interfaces to Areas
config router ospf
 config ospf-interface
  edit "port1"
   set interface "port1"
   set area 0.0.0.1
  next
  edit "port2"
   set interface "port2"
   set area 0.0.0.0
  next
  edit "port3"
   set interface "port3"
   set area 0.0.0.3
  next
  edit "port4"
   set interface "port4"
   set area 0.0.0.4
  next
 end
end

# 4️⃣ Enable Redistribution
# RIP → OSPF
config router ospf
 config redistribute "rip"
  set status enable
  set metric 10
 end
end

# EIGRP → OSPF
config router ospf
 config redistribute "eigrp"
  set status enable
  set metric 10
 end
end

# 5️⃣ Optional: Static Default Route (for management or internet)
config router static
 edit 1
  set dst 0.0.0.0/0
  set gateway 192.168.1.1
  set device "port5"
 next
end

## ✅ Verification Commands
# Cisco Routers
show ip route
show ip ospf neighbor
show ip protocols
show ip rip database
show ip eigrp neighbors

# Fortinet Firewall
get router info ospf status
get router info ospf neighbor
get router info routing-table all

```









# COMPUTER-NETWORKS-SDN-PROJECT
# SDN Static Routing using POX Controller

## 🚀 Project Overview
This project demonstrates **Software-Defined Networking (SDN)** by implementing **static routing** using a POX controller.

The network is configured such that:
- ✅ Only **h1 (10.0.0.1)** ↔ **h4 (10.0.0.4)** can communicate  
- ❌ All other host-to-host communication is blocked  

This showcases how SDN controllers enforce **fine-grained traffic control**.

---

## 🧠 What is SDN?
SDN separates:
- **Control Plane** → decision making (controller)
- **Data Plane** → packet forwarding (switches)

The controller installs rules, and switches simply follow them.

---

## 🧩 Features
- Static flow rule installation
- Controlled communication between specific hosts
- Packet dropping for unauthorized paths
- Flow inspection using OVS tools
- Performance testing using `iperf`

---

## 🌐 Network Topology
h1 --- s1 --- s2 --- s3 --- s4 --- h4
| |
h2 h3


---

## 🔌 Port Mapping

| Switch | Port 1 | Port 2 | Port 3 | Port 4 |
|--------|--------|--------|--------|--------|
| s1     | h1     | s2     | -      | -      |
| s2     | h2     | s1     | s3     | -      |
| s3     | h3     | s2     | s4     | -      |
| s4     | h4     | s3     | -      | -      |

---

## 🔁 Allowed Path
h1 → s1 → s2 → s3 → s4 → h4

---

## ⚙️ Flow Rules Summary

Each switch contains rules like:
- Match: `src=10.0.0.1, dst=10.0.0.4`
- Action: forward to next switch
- Reverse rules for return traffic

---

## ✅ Why h1 → h4 Works
- Controller installs matching flow rules
- Packets follow predefined path
- Reply follows reverse path

---

## ❌ Why h1 → h2 Fails
- No flow rule exists
- Packet sent to controller
- Controller drops packet

---

## 📁 Project Structure
├── myrouter.py # POX controller logic
├── validate.py # Testing script
├── regression_test.sh # Rule validation
├── requirements.txt # Dependencies


---

## ⚙️ Prerequisites


sudo apt update
sudo apt install mininet -y

git clone https://github.com/noxrepo/pox
cd pox
git checkout dart


How to Run
Terminal 1
cd ~/pox
./pox.py myrouter

Terminal 2
sudo mn -c
sudo mn --topo linear,4 \
--controller=remote,ip=127.0.0.1,port=6633 \
--switch ovsk,protocols=OpenFlow10

Tests
h1 ping h4 -c 5
h1 ping h2 -c 3
iperf h1 h4

Expected Results
| Test        | Result           |
| ----------- | ---------------- |
| h1 ping h4  | 0% packet loss   |
| h1 ping h2  | 100% packet loss |
| iperf h1 h4 | ~945 Mbps        |

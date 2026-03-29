# Proxmox Lab Setup

## 🖥️ Environment
- Host: Proxmox VE (HP EliteDesk 800 G3 SFF)
- RAM: 24GB 
- Storage: 1tb M.2 for hosts
          16tb 7200 RPM HDD

---

## 🌐 Network Configuration

### vmbr0 (WAN)
- Connected to home network

### vmbr1 (LAN)
- Internal isolated lab network

---

## 🔥 Virtual Machines

### pfSense
- WAN: vmbr0
- LAN: vmbr1

### Windows Server
- Role: Domain Controller
- Domain: corp.local

### Windows Client
- Joined to domain

### Kali Linux
- Attacker machine

### Wazuh Server
- Centralized logging

---

## ✅ Outcome
A segmented lab environment simulating an enterprise network.

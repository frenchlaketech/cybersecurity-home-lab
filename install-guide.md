🖥️ install-guide.md – Proxmox Homelab Setup
1. Hardware Requirements
Machine: HP EliteDesk 800 G3 SFF
CPU: Intel i5/i7 (6th–7th gen)
RAM: Minimum 16GB, ideally 32–64GB
Storage:
NVMe SSD for OS + VMs
Optional SATA SSDs for additional VM disks / ZFS
Network: 1x Gigabit NIC (can add extra via PCIe)
2. BIOS / Firmware Prep
Boot into BIOS (F10 during startup)
Enable Virtualization (VT-x)
Enable VT-d / IOMMU (for PCI passthrough if needed)
Disable Secure Boot (required for Proxmox)
Set boot order: NVMe/SATA drive first
Optional: Update BIOS to latest version
3. Proxmox VE Installation
Download Proxmox VE ISO: https://www.proxmox.com/en/downloads
Create bootable USB (Rufus / Balena Etcher)
Boot from USB
Follow installer steps:
Select target drive (NVMe recommended)
Set hostname (homelab.local)
Set root password
Configure management interface (initially use vmbr0 → DHCP)
Finish installation and reboot
4. Initial Configuration
Access Proxmox Web UI
URL: https://<Proxmox-IP>:8006/
Login: root / <your password>
Update Proxmox
apt update && apt full-upgrade -y
reboot
Set Timezone / NTP
GUI → Datacenter → Node → System → Time
Enable NTP for accurate logging
5. Storage Setup
Recommended: ZFS (RAID1) if using 2+ drives
Otherwise:
LVM-thin on NVMe for VM disks
Additional SSDs can be added as separate storage pools
6. Network Bridges

Since HP EliteDesk has 1 NIC, we use virtual bridges:

Bridge	Purpose	Connected to
vmbr0	WAN	Physical NIC (internet)
vmbr1	MGMT	Internal only
vmbr2	USER	Internal only
vmbr3	ATTACK	Internal only
Create Bridges
# Example: create vmbr1
nano /etc/network/interfaces

# Add:
auto vmbr1
iface vmbr1 inet static
    address 10.10.10.1/24
    bridge_ports none
    bridge_stp off
    bridge_fd 0
Repeat for vmbr2, vmbr3
Reboot networking or node
7. VM Template Preparation
pfSense ISO
Windows Server ISO
Windows 10/11 ISO
Kali Linux ISO
Ubuntu Server ISO (optional, for web server / Wazuh)
Upload ISOs
GUI → Datacenter → Storage → ISO Images → Upload
8. VM Deployment (Basic Steps)
pfSense Firewall
VM type: Linux → Other
Disk: 16GB
RAM: 2GB
CPU: 2 cores
Network:
WAN → vmbr0
LAN → vmbr2
OPT → vmbr1 (MGMT)
OPT2 → vmbr3 (ATTACK)
Windows Server
Disk: 60GB
RAM: 8GB
CPU: 2–4 cores
Network → vmbr2 (USER)
Windows 10 Client
Disk: 40GB
RAM: 4GB
Network → vmbr2 (USER)
Kali Linux
Disk: 40GB
RAM: 4GB
Network → vmbr3 (ATTACK)
Proxmox Host / Wazuh
Can be a container (LXC) or VM
Network → vmbr1 (MGMT)
9. Next Steps (Post-Install)
Configure pfSense firewall rules
Join Windows clients to Active Directory
Install Wazuh agent on Windows VMs
Start building attack scenarios

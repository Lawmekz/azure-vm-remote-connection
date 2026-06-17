# Azure VM Remote Connection — Assessment 4

## Overview
This project demonstrates setting up and remotely connecting to both a Windows and Linux Virtual Machine on Microsoft Azure using RDP and SSH via Azure Bastion.

---

## Virtual Machines Created

### Windows VM
| Item | Value |
|---|---|
| **VM Name** | Windows-VM-Assessment |
| **Resource Group** | VM-Assessment-RG |
| **Region** | West Europe (Zone 1) |
| **OS** | Windows Server 2022 Datacenter Azure Edition |
| **VM Size** | Standard B2s v2 (2 vCPUs, 8 GiB RAM) |
| **Public IP** | 4.231.88.162 |
| **Private IP** | 10.0.0.4 |
| **Created** | 16 June 2026 |

### Linux VM
| Item | Value |
|---|---|
| **VM Name** | Linux-VM-Assessment |
| **Resource Group** | Linux-VM-Assessment_group |
| **Region** | West Europe |
| **OS** | Ubuntu 24.04.4 LTS (Noble Numbat) |
| **VM Size** | Standard B2s v2 (2 vCPUs, 8 GiB RAM) |
| **Public IP** | 20.54.142.190 |
| **Private IP** | 10.1.0.4 |
| **Created** | 16 June 2026 |

---

## Tasks Completed

### Task 1 — Configure Networking and Security
- Created NSG: `Windows-VM-Assessment-nsg` with inbound rule allowing RDP on **Port 3389**
- Created NSG for Linux VM allowing SSH on **Port 22**
- Both NSGs configured with DenyAllInbound as default rule

### Task 2 — Identify Connection Endpoints
- Windows VM Public IP: **4.231.88.162**
- Linux VM Public IP: **20.54.142.190**
- Both located via Azure Portal VM Overview page

### Task 3 — Establish Windows RDP Connection
- Used **Remote Desktop Connection** (`mstsc`) on local PC
- Connected to `4.231.88.162` using credentials:
  - Username: `azureuser`
- Successfully accessed Windows Server 2022 desktop
- Server Manager Dashboard confirmed successful connection

### Task 4 — Establish Linux SSH Connection via Azure Bastion
- Deployed **Azure Bastion** (Developer SKU): `Linux-VM-Assessment-vnet-bastion`
- Connected to Linux VM through browser-based Bastion terminal
- Authentication Type: VM Password
- Username: `azureuser`
- Successfully accessed Ubuntu 24.04 LTS command prompt

### Task 5 — Verify System Access

#### Windows VM (PowerShell):
```powershell
Get-ComputerInfo | Select-Object CsName, OsName, OsVersion
whoami
ipconfig
```
**Results:**
- Computer Name: `Windows-VM-Asse`
- OS: Microsoft Windows Server 2022 Datacenter Azure Edition
- OS Version: 10.0.20348
- User: `windows-vm-asse\azureuser`
- IPv4: `10.0.0.4`

#### Linux VM (Bash):
```bash
whoami
hostname
cat /etc/os-release
free -h
uname -a
uptime
```
**Results:**
- User: `azureuser`
- Hostname: `Linux-VM-Assessment`
- OS: Ubuntu 24.04.4 LTS (Noble Numbat)
- Kernel: Linux 6.17.0-1018-azure x86_64
- RAM: 7.7Gi total, 6.9Gi free
- Uptime: 1 hour 10 minutes

### Task 6 — Secure the Environment
- Edited RDP inbound NSG rule
- Restricted Source from `Any` to `105.112.228.40/32`
- Only administrator's specific public IP can access VM via RDP

### Task 7 — Manage Remote Sessions
- Windows: Signed out via Start menu — RDP session terminated
- Linux: Typed `exit` command — Bastion session disconnected

---

## NSG Rules Summary

### Windows VM Inbound Rules
| Priority | Name | Port | Protocol | Source | Action |
|---|---|---|---|---|---|
| 300 | RDP | 3389 | TCP | 105.112.228.40/32 | Allow |
| 65000 | AllowVnetInBound | Any | Any | VirtualNetwork | Allow |
| 65001 | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Allow |
| 65500 | DenyAllInBound | Any | Any | Any | Deny |

### Linux VM Inbound Rules
| Priority | Name | Port | Protocol | Source | Action |
|---|---|---|---|---|---|
| 300 | SSH | 22 | TCP | Any | Allow |
| 65000 | AllowVnetInBound | Any | Any | VirtualNetwork | Allow |
| 65001 | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Allow |
| 65500 | DenyAllInBound | Any | Any | Any | Deny |

---

## Azure Bastion
- **Bastion Name:** Linux-VM-Assessment-vnet-bastion
- **SKU:** Developer
- **Provisioning State:** Succeeded
- Bastion enables secure RDP/SSH connectivity without exposing VMs through public IP addresses
- Connected to Linux VM via browser-based terminal through Bastion

---

## Security Best Practices Applied
- RDP access restricted to a single trusted IP address (105.112.228.40/32)
- SSH access managed securely via Azure Bastion (no public SSH exposure needed)
- Auto-shutdown enabled at 11:00 PM UTC on both VMs
- Static Public IPs assigned for consistent endpoint management
- NSGs attached at network interface level for granular traffic control
- SSH key-based authentication configured during Linux VM creation

---

## Troubleshooting

### RDP Connection Issues
| Problem | Solution |
|---|---|
| Cannot connect to RDP | Check NSG rule allows port 3389, verify Public IP is correct |
| Wrong credentials | Reset password via Azure Portal → Reset password |
| RDP port blocked | Verify inbound rule priority is lower than DenyAllInBound (65500) |
| Connection timeout | Check VM is in Running state, not stopped |

### SSH Connection Issues
| Problem | Solution |
|---|---|
| Permission denied (publickey) | Enable password auth via Run Command or use Bastion |
| Connection refused | Verify NSG allows port 22, check VM is running |
| Password not accepted | Reset via Azure Portal → Help → Reset password (min 12 chars) |
| Popup blocked on Bastion | Allow popups from portal.azure.com in browser settings |

### General Issues
| Problem | Solution |
|---|---|
| VM size unavailable | Change region or select different size (B2s_v2 recommended) |
| Public IP missing | Create new Static Standard IP in Networking tab |
| NSG rule not saving | Ensure IP format is correct e.g. 105.112.228.40/32 |

---

## RDP/SSH Client Setup by Operating System

### Windows
- **RDP:** Built-in — press `Win+R`, type `mstsc`
- **SSH:** Use PowerShell or Windows Terminal: `ssh user@ip`

### macOS
- **RDP:** Download Microsoft Remote Desktop from App Store
- **SSH:** Use Terminal: `ssh -i key.pem user@ip`

### Linux
- **RDP:** Install Remmina: `sudo apt install remmina`
- **SSH:** Use Terminal: `ssh -i key.pem user@ip`

---

## Screenshots
See the `/screenshots` folder for visual proof of:
- VM deployment completion (Windows & Linux)
- VM Overview pages with Public IPs
- Successful RDP connection (Windows Server desktop)
- Server Manager Dashboard
- PowerShell system verification output
- Bastion connection to Linux VM
- Linux system verification commands
- NSG rules configuration (before and after restriction)
- Session termination

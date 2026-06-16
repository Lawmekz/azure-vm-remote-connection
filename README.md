# Azure VM Remote Connection — Assessment 4

## Overview
This project demonstrates setting up and remotely connecting to a Windows Virtual Machine on Microsoft Azure using Remote Desktop Protocol (RDP).

---

## Project Details
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

---

## Tasks Completed

### Task 1 — Configure Networking and Security
- Created a Network Security Group (NSG): `Windows-VM-Assessment-nsg`
- Added inbound rule to allow RDP traffic on **Port 3389** over TCP

### Task 2 — Identify Connection Endpoints
- Located Public IP address: **4.231.88.162** from the Azure Portal VM Overview page
- Virtual Network: `Windows-VM-Assessment-vnet/default`

### Task 3 — Establish Windows RDP Connection
- Used **Remote Desktop Connection** (`mstsc`) on local PC
- Connected to `4.231.88.162` using credentials:
  - Username: `azureuser`
- Successfully accessed the Windows Server 2022 desktop environment

### Task 4 — Verify System Access
Ran the following PowerShell commands inside the VM:
```powershell
Get-ComputerInfo | Select-Object CsName, OsName, OsVersion
whoami
ipconfig
(Get-Date) - (gcim Win32_OperatingSystem).LastBootUpTime
```
**Results:**
- Computer Name: `Windows-VM-Asse`
- OS: Microsoft Windows Server 2022 Datacenter Azure Edition
- OS Version: 10.0.20348
- Logged in as: `windows-vm-asse\azureuser`
- IPv4 Address: `10.0.0.4`

### Task 5 — Secure the Environment
- Edited the RDP inbound NSG rule
- Restricted **Source** from `Any` to `105.112.228.40/32`
- Only the administrator's specific public IP can now access the VM via RDP

### Task 6 — Manage Remote Sessions
- Properly terminated the RDP session by signing out
- RDP window disconnected confirming clean session termination

---

## NSG Rules Summary

### Inbound Rules
| Priority | Name | Port | Protocol | Source | Action |
|---|---|---|---|---|---|
| 300 | RDP | 3389 | TCP | 105.112.228.40/32 | Allow |
| 65000 | AllowVnetInBound | Any | Any | VirtualNetwork | Allow |
| 65001 | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Allow |
| 65500 | DenyAllInBound | Any | Any | Any | Deny |

---

## Security Best Practices Applied
- RDP access restricted to a single trusted IP address
- Auto-shutdown enabled at 11:00 PM UTC to prevent unnecessary costs
- Static Public IP assigned for consistent endpoint management
- NSG attached at network interface level for granular control

---

## Screenshots
See the `/screenshots` folder for visual proof of:
- VM deployment completion
- VM Overview page with Public IP
- Successful RDP connection (Windows Server desktop)
- PowerShell system verification output
- NSG rules configuration
- Session termination

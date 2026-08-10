# Azure VM Networking & File Sharing Using VNet

## 📌 Project Overview

This project demonstrates **private communication and file sharing
between two Azure Virtual Machines (VMs)** deployed inside the same
Azure Virtual Network (VNet) but placed in different subnets.

Two VMs were created and connected to the same VNet:

-   **VM1** → `default` subnet
-   **VM2** → `default1` subnet

Connectivity between the VMs was verified using **Ping**, and a
`SharedFiles` folder was created on one VM and accessed from the other
VM using Windows network file sharing.

> **No Azure Storage Account was used for the VM-to-VM file sharing
> demonstration.**

------------------------------------------------------------------------

## 🎯 Objectives

-   Create an Azure Virtual Network.
-   Create two subnets inside the VNet.
-   Deploy two Windows Virtual Machines.
-   Connect each VM to a different subnet of the same VNet.
-   Verify private network connectivity between the VMs.
-   Configure Windows file sharing.
-   Create a `SharedFiles` folder on one VM.
-   Access the shared folder from the other VM.
-   Demonstrate file sharing without using an Azure Storage Account.
-   Capture screenshots as proof of successful configuration and
    testing.

------------------------------------------------------------------------

## 🏗️ Architecture

``` text
                    Azure Virtual Network
                         VNet1
                    10.0.0.0/16
                          │
              ┌───────────┴───────────┐
              │                       │
              ▼                       ▼
       default subnet          default1 subnet
       10.0.0.0/24             10.0.1.0/24
              │                       │
              ▼                       ▼
            VM1                     VM2
        Windows VM              Windows VM
              │                       │
              └──── Private VNet ─────┘
                    Communication
                          │
                    Ping / SMB
                          │
                          ▼
                    SharedFiles
```

------------------------------------------------------------------------

# 1. Create the Virtual Network

An Azure Virtual Network named **VNet1** was created to provide private
network connectivity between the two VMs.

### VNet Configuration

  Configuration            Value
  ------------------------ ---------------
  VNet Name                `VNet1`
  Address Space            `10.0.0.0/16`
  Subnet 1                 `default`
  Subnet 1 Address Range   `10.0.0.0/24`
  Subnet 2                 `default1`
  Subnet 2 Address Range   `10.0.1.0/24`

### Network Structure

``` text
VNet1
│
├── default
│   └── 10.0.0.0/24
│
└── default1
    └── 10.0.1.0/24
```

The two subnets are part of the same VNet, allowing resources in them to
communicate using private IP addresses, subject to network security and
firewall rules.

------------------------------------------------------------------------

# 2. Deploy the Virtual Machines

Two Windows-based Azure Virtual Machines were deployed.

## VM1

``` text
VM Name: VM1
OS: Windows Server
Subnet: default
```

## VM2

``` text
VM Name: VM2
OS: Windows Server
Subnet: default1
```

### VM Placement

``` text
VNet1
│
├── default (10.0.0.0/24)
│       └── VM1
│
└── default1 (10.0.1.0/24)
        └── VM2
```

Each VM received a private IP address from its respective subnet.

------------------------------------------------------------------------

# 3. Verify VM Connectivity

After deploying both VMs, connectivity was tested using the **Ping**
command.

The private IP address of the destination VM was used for testing.

### From VM1

``` powershell
ping <VM2-Private-IP>
```

### From VM2

``` powershell
ping <VM1-Private-IP>
```

Successful replies confirmed that the two VMs could communicate over the
VNet.

### Expected Result

``` text
VM1  ───────────────►  VM2
       Ping Reply

VM2  ───────────────►  VM1
       Ping Reply
```

> If ping is blocked, the Windows Firewall ICMP Echo Request rule must
> be enabled for the appropriate network profile before testing.

------------------------------------------------------------------------

# 4. Configure File Sharing

To demonstrate file sharing without an Azure Storage Account, a folder
named:

``` text
SharedFiles
```

was created on one of the VMs.

For example:

``` text
VM2
└── SharedFiles
```

The folder was configured as a Windows network share.

------------------------------------------------------------------------

# 5. Access the Shared Folder from the Other VM

From the other VM, the shared folder can be accessed using the
destination VM's private IP address:

``` text
\\<VM2-Private-IP>\SharedFiles
```

Alternatively, Windows network sharing can be accessed through File
Explorer using:

``` text
\\<VM2-Private-IP>
```

The `SharedFiles` folder should appear as an available network share.

------------------------------------------------------------------------

# 6. File Sharing Architecture

``` text
                  Azure VNet1
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ▼                             ▼
       VM1                           VM2
   default subnet              default1 subnet
   10.0.0.0/24                 10.0.1.0/24
        │                             │
        │      Private Network        │
        └──────────────►──────────────┘
                                      │
                                      ▼
                                SharedFiles
                                  Folder
```

The file transfer occurs directly between the VMs over the private Azure
network.

``` text
VM1
 │
 │ SMB / Windows File Sharing
 │
 ▼
VM2
 │
 └── SharedFiles
```

**Azure Storage Account is not involved.**

------------------------------------------------------------------------

# 7. Technologies & Azure Services Used

-   Microsoft Azure
-   Azure Virtual Network (VNet)
-   Azure Subnets
-   Azure Virtual Machines
-   Windows Server
-   Private IP Addressing
-   ICMP / Ping
-   Windows File Sharing
-   SMB
-   File Explorer
-   PowerShell / Command Prompt

------------------------------------------------------------------------

# 8. Important Commands

### Check IP Configuration

``` powershell
ipconfig
```

### Test Connectivity

``` powershell
ping <private-ip-address>
```

### Test SMB Port

``` powershell
Test-NetConnection <private-ip-address> -Port 445
```

### View Network Shares

``` powershell
Get-SmbShare
```

### Access a Shared Folder

``` text
\\<private-ip-address>\SharedFiles
```

### Map the Shared Folder as a Network Drive

``` powershell
net use Z: \\<private-ip-address>\SharedFiles
```

------------------------------------------------------------------------

# 9. Testing & Verification

The project was verified in multiple stages.

### Test 1 --- VNet and Subnets

Verified that both subnets exist inside `VNet1`.

``` text
VNet1
├── default
└── default1
```

### Test 2 --- VM Deployment

Verified that:

-   VM1 is connected to `default`.
-   VM2 is connected to `default1`.

### Test 3 --- Ping

Ping was performed between the two VMs using their private IP addresses.

``` text
VM1 ── Ping ──► VM2
VM2 ── Ping ──► VM1
```

### Test 4 --- Shared Folder

The `SharedFiles` folder created on one VM was accessed from the other
VM.

``` text
VM1
 │
 └── Access SharedFiles
          │
          ▼
         VM2
```

### Test 5 --- File Transfer

A test file can be created inside `SharedFiles` and opened from the
other VM to confirm successful file sharing.

------------------------------------------------------------------------

# 10. Screenshots

Recommended screenshots for this project:

``` text
screenshots/
│
├── 01-vnet-overview.png
├── 02-vnet-subnets.png
├── 03-vm1-configuration.png
├── 04-vm2-configuration.png
├── 05-vm1-private-ip.png
├── 06-vm2-private-ip.png
├── 07-ping-vm1-to-vm2.png
├── 08-ping-vm2-to-vm1.png
├── 09-sharedfiles-folder.png
├── 10-folder-sharing-settings.png
├── 11-network-share-from-other-vm.png
└── 12-file-sharing-test.png
```

### Suggested Screenshot Descriptions

**01 --- VNet Overview**\
Shows the Azure Virtual Network configuration.

**02 --- VNet Subnets**\
Shows:

``` text
default  → 10.0.0.0/24
default1 → 10.0.1.0/24
```

**03 & 04 --- VM Configuration**\
Shows that VM1 and VM2 are connected to different subnets.

**05 & 06 --- Private IPs**\
Shows the private IP address of each VM.

**07 & 08 --- Ping Tests**\
Shows successful communication between the VMs.

**09 --- SharedFiles Folder**\
Shows the folder created on the host VM.

**10 --- Sharing Configuration**\
Shows that the folder has been configured as a network share.

**11 --- Network Share**\
Shows `SharedFiles` being accessed from the second VM.

**12 --- File Sharing Test**\
Shows a test file being successfully accessed through the network share.

------------------------------------------------------------------------

# 11. Repository Structure

``` text
azure-vm-networking-file-sharing/
│
├── README.md
│
├── screenshots/
│   ├── 01-vnet-overview.png
│   ├── 02-vnet-subnets.png
│   ├── 03-vm1-configuration.png
│   ├── 04-vm2-configuration.png
│   ├── 05-vm1-private-ip.png
│   ├── 06-vm2-private-ip.png
│   ├── 07-ping-vm1-to-vm2.png
│   ├── 08-ping-vm2-to-vm1.png
│   ├── 09-sharedfiles-folder.png
│   ├── 10-folder-sharing-settings.png
│   ├── 11-network-share-from-other-vm.png
│   └── 12-file-sharing-test.png
│
└── commands/
    └── networking-and-file-sharing.md
```

------------------------------------------------------------------------

# 12. Project Result

The project successfully demonstrated **VM-to-VM communication and file
sharing using an Azure Virtual Network**.

Final setup:

``` text
                         VNet1
                     10.0.0.0/16
                          │
             ┌────────────┴────────────┐
             │                         │
             ▼                         ▼
       default subnet            default1 subnet
       10.0.0.0/24               10.0.1.0/24
             │                         │
             ▼                         ▼
            VM1                       VM2
             │                         │
             └────── Private VNet ─────┘
                       │
                 ┌─────┴─────┐
                 │           │
                Ping        SMB
                 │           │
                 └─────┬─────┘
                       ▼
                  SharedFiles
```

### Result

✅ One Azure Virtual Network created\
✅ Two subnets created\
✅ Two VMs deployed in different subnets\
✅ Private IP communication verified using Ping\
✅ `SharedFiles` folder created\
✅ File sharing demonstrated between VMs\
✅ No Azure Storage Account used for file sharing

------------------------------------------------------------------------

# 🎓 Key Learning Outcomes

Through this project, I learned:

-   How Azure Virtual Networks provide private connectivity.
-   How to divide a VNet into multiple subnets.
-   How to deploy VMs into specific subnets.
-   How VMs in different subnets of the same VNet communicate.
-   How private IP addresses are used for VM-to-VM communication.
-   How to troubleshoot connectivity using Ping.
-   How Windows Firewall can affect ICMP communication.
-   How Windows SMB file sharing works.
-   How to share folders between Windows VMs.
-   How to perform file sharing without using an Azure Storage Account.

------------------------------------------------------------------------

## 👨‍💻 Project Type

**Azure Cloud Computing / Networking / Virtual Machine Practical**

**Repository:** `azure-vm-networking-file-sharing`

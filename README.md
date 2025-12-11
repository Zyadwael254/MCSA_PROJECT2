# MCSA_PROJECT2
# 🗃️ Windows Server Full LAB - Deployment Guide

## 📜 Overview (نظرة عامة)
This document guides the complete setup and configuration of a Windows Server domain environment, covering Active Directory Domain Services (AD DS), Group Policy Objects (GPOs), DHCP, DNS Advanced Features, Hyper-V, WDS, and Backup Solutions.

## 🎯 Objectives (الأهداف)
* Establish a fully functional, secure, and resilient domain environment (`ABC.LOCAL`).
* Implement advanced network services (DHCP Failover, DNS Load Balancing).
* Deploy core infrastructure services (Hyper-V, WDS).
* Enforce security and access control for users and resources (Files, Printers).
* Ensure business continuity through Backup and an Additional Domain Controller (ADC).

---

## 🛠️ Phase 1: Core Services Setup (AD, DHCP, GPO)

*(هذه الأقسام مأخوذة من الصورة الأولى)*

### 1.1 Domain and Active Directory Setup
* **Domain Name:** `ABC.LOCAL`
* **PDC Server Name:** `PDC`
* **OU Structure:** Create separate OUs for: `HR`, `Sales`, `Finance`, `Dev`, `IT`.
* **Users & Groups:** Create users and separate security groups for each department, and add the users to their respective department groups.

### 1.2 Security Policy Configuration (GPOs)
| Policy | Requirement | Details |
| :--- | :--- | :--- |
| **Password Policy** | Must change every **60 days**. | Min length **6 digits**, require **complex password**, remember **last 3 passwords**. |
| **Account Lockout** | Lockout for **1 hour** after **4 wrong password** attempts. |
| **System Restrictions** | Prohibit access to: **External storage**, **Task Manager**, **Control Panel**. | Remove **`Manage`** and **`Properties`** from the computer context menu. |
| **Remote Access** | Enable **Remote Desktop** connection for all machines in the domain. |

### 1.3 DHCP Configuration
* **Scope:** Start `192.168.1.40` to `192.168.1.230 /24`.
* **Exclusions:** Exclude the range from IP address `192.168.1.80` to `192.168.1.85`.
* **DHCP Failover:** Configure **DHCP Failover** on the **Windows Server Core** instance.

---

## ⚙️ Phase 2: Advanced Infrastructure (DNS, Hyper-V, WDS)

### 2.1 Domain Controllers and Replication
* **Additional DC (ADC):** Create an **Additional Domain Controller** and configure it with an **alternate DNS**.

### 2.2 DNS Configuration
* **Load Balancing (Round Robin):** Use DNS to create **load balancing** for `www.abc.local` across two IP Addresses: `192.168.1.8` and `192.168.1.9`.

### 2.3 Virtualization (Hyper-V)
* **Hyper-V Role:** Install the Hyper-V role on **Windows Server Core**.
* **Core VM:** Create a **Core Virtual Machine (VM)**.
* **Replication:** Configure **Hyper-V Replica** for the Core VM with the **PDC server**.

### 2.4 Client Deployment (WDS)
* **WDS Install:** Install Windows Deployment Services (WDS) role.
* **Client OS Deployment:** Install **Windows 10 for the client** through **WDS**.

---

## 📂 Phase 3: Access Control and User Environment

### 3.1 Client Machine Setup
* **Domain Join:** Join the Windows 10 client machine to the domain (`ABC.LOCAL`).
* **Local Admin Rights:** Add the **`Dev`** group as a **local administrator** on the client machine.

### 3.2 File Share Management (GPO/ACLs)
* **Shared Folder:** Create a shared folder for the `Dev` and `HR` departments.
    * `Dev` and `HR` can **create** and **edit** files.
    * `Dev` and `HR` **cannot delete** files.
* **Network Drive:** Create a **MAP network drive** for the `Dev` and `HR` departments (via GPO Preference).

### 3.3 Printer Management (GPO)
* **Printer Deployment:** Add a **Printer** for all domain users.
* **Printing Restrictions:**
    * Printing must be **black and white printing only**.
    * Users can use the printer only from **9:00 am to 4:00 pm**. (Time restriction via GPO/Printer Schedule).

---

## 💾 Phase 4: Business Continuity (Backup)

* **Full Server Backup:** Create a **full server backup** every day at **11:00 pm** for the **main server (PDC)**.
* **Backup Location:** Store the backup on the **ADC (Additional Domain Controller)**.

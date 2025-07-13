# 🔐 Hub-Spoke Architecture with Azure Firewall (Traffic Inspection)

## 🚀 Problem Statement

Design and implement a **Hub-Spoke network topology** in Azure where:

- All outbound traffic from **Spoke VMs** is routed through a **central Azure Firewall** deployed in the **Hub VNet**.
- Only specific **FQDNs** like `github.com` should be **allowed**.
- Access to others like `facebook.com` should be **blocked**.
- Prove that the **traffic is inspected by the firewall**, using `tracepath`, `curl`, and **Azure Firewall Logs**.

## 🧱 Architecture Overview

- **Hub VNet**: `10.0.0.0/16`
  - `AzureFirewallSubnet` → `10.0.1.0/24`
  - `AzureBastionSubnet` → `10.0.2.0/27`
- **Spoke1 VNet**: `10.1.0.0/16` → `vm-spoke1`
- **Spoke2 VNet**: `10.2.0.0/16` → `vm-spoke2`
- UDR from Spokes → Firewall IP
- Firewall rules:
  - ✅ `github.com` → allow
  - ❌ `facebook.com` → deny

## ✅ Test Results

curl github.com
```bash
curl -I https://github.com
# HTTP/2 200 OK 
```
curl facebook.com
```bash
 curl -I https://facebook.com
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to facebook.com:443 
```
🔹 tracepath github.com
```bash
 tracepath github.com
 1?: [LOCALHOST]                      pmtu 1500
 1:  10.0.1.5                                              2.460ms 
 1:  10.0.1.6                                              2.141ms 
```

# 🚀 Step-by-Step: Hub-Spoke with Azure Firewall (Hands-On Guide)

This document tracks all the **actual steps I performed** during my Azure Firewall-based hub-spoke networking project.

---

## 🔹 Step 1: Create Hub VNet
- Name: `vnet-hub`
- Address space: `10.0.0.0/16`
- Subnet: `AzureFirewallSubnet` → `10.0.1.0/24`

---

## 🔹 Step 2: Deploy Azure Firewall
- Name: `azfw-hub`
- Public IP: `pip-azfw-hub`
- Virtual network: `vnet-hub`
- Captured private IP for UDR: `10.0.1.x`

---

## 🔹 Step 3: Create Spoke VNets
- `vnet-spoke1` → Subnet: `10.1.1.0/24` → VM: `vm-spoke1`
- `vnet-spoke2` → Subnet: `10.2.1.0/24` → VM: `vm-spoke2`

---

## 🔹 Step 4: VNet Peering
- Hub ↔ Spoke1 (bi-directional)
- Hub ↔ Spoke2 (bi-directional)

---

## 🔹 Step 5: Route Table + UDR
- Route table: `rt-spoke-to-firewall`
- Route: `0.0.0.0/0` → Next hop: Firewall private IP
- Associated to both Spoke subnets

---

## 🔹 Step 6: Create Firewall Rules
### ✅ Allow Rule
- Rule collection: `app-allow-github`
- Target FQDN: `github.com`
- Protocols: HTTP, HTTPS

### ❌ Deny Rule
- Rule collection: `app-deny-facebook`
- Target FQDN: `facebook.com`
- Protocols: HTTP, HTTPS

---

## 🔹 Step 7: Deploy Azure Bastion
- Bastion deployed in `vnet-hub`
- Subnet: `AzureBastionSubnet` → `10.0.2.0/27`
- Used to SSH into spoke VMs

---

## 🔹 Step 8: Validate with CLI Tools
From `vm-spoke1`:

```bash
nslookup github.com
curl -I https://github.com       # Success ✅
curl -I https://facebook.com     # Blocked ❌
tracepath github.com             # First hop = Firewall
```

## 🔹 Step 9: Enable Azure Firewall Logs (Diagnostics)

To prove firewall inspection and allow advanced traffic analysis, I enabled logging to **Log Analytics Workspace**.

### 🔧 Steps I Followed:

1. **Created Log Analytics Workspace**
   - Name: `log-fw-hub`
   - Resource Group: `rg-hub-spoke`
   - Region: Same as Azure Firewall 

2. **Linked Azure Firewall to the Workspace**
   - Went to: `azfw-hub` → **Diagnostic settings**
   - Clicked **+ Add diagnostic setting**
   - Name: `fw-logs`
   - ✅ Enabled log categories:
     - `AzureFirewallApplicationRule`
     - `AzureFirewallNetworkRule`
     - `AzureFirewall`
   - ✅ Destination: **Send to Log Analytics**
   - Selected: `log-fw-hub`
   - Clicked **Save**


3. **Verified Logs**
   - Went to: `log-fw-hub` → Logs
   - Ran Kusto Query:

```kusto
AzureDiagnostics
| where ResourceType == "AZUREFIREWALLS"
| where Fqdn_s contains "github.com" or Fqdn_s contains "facebook.com"
```

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/922c5e86-c5d9-4a11-9850-096ebf7ace4d" />



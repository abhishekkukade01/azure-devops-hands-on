
# 🔐 Secure Web App with Azure WAF and Private SQL

> **Project Goal**: Secure a web application using Application Gateway (WAF-enabled) and Azure SQL with private endpoint access only.

---

## 📌 Problem Statement

Companies hosting sensitive web applications need to ensure both front-end (web) and back-end (database) layers are secure from malicious threats and exposed only to authorized internal resources.

---

## 🧱 Architecture Overview

**Components Used:**

* Application Gateway with WAF v2 (Web Layer Protection)
* NGINX Web Server on VM (Web App)
* Azure SQL Database with Private Endpoint (Database Layer Protection)
* Virtual Network and Subnets (Network Isolation)

**Key Security Features:**

* WAF with OWASP rules to block XSS and SQLi
* SQL DB only accessible via private IP (no public endpoint)
* NGINX reverse proxied via Application Gateway

---



---

## ✅ Step-by-Step Implementation

### Step 1: Create Resource Group and Virtual Network

* `vnet-webapp` with 2 subnets:

  * `subnet-web` (for VM)
  * `subnet-appgw` (for Application Gateway)
<img width="2940" height="902" alt="image" src="https://github.com/user-attachments/assets/224f9f33-9cb1-4983-bf61-8ffae9699a05" />


### Step 2: Create Azure SQL Database

* Server name: `sqlsvr-secureweb`
* DB name: `sqldb-secureweb`
* **No public access**
* Create Private Endpoint to `subnet-db` in VNet

<img width="2940" height="1163" alt="image" src="https://github.com/user-attachments/assets/fcaa182a-3c70-426f-8a6c-cc1c4ca2e899" />


### Step 3: Deploy Application Gateway (WAF v2)

* Attach to `subnet-appgw`
* Enable **WAF mode: Prevention**
* Add OWASP default rules
* Backend pool: Target the Web App VM private IP
* Health Probe and HTTP Rule
<img width="2902" height="1186" alt="image" src="https://github.com/user-attachments/assets/ce6fa233-45e7-416b-ba3a-8f0c97c56922" />


### Step 4: Deploy VM and Install NGINX

* VM in `subnet-web`

* Install NGINX:

  ```bash
  sudo apt update
  sudo apt install nginx -y
  ```

* Test from browser using AG public IP → You should see the NGINX welcome page

### Step 5: Test WAF Protection

* Run attack string from curl:

  ```bash
  curl "http://<AG-Public-IP>/?q=<script>alert(1)</script>"
  ```
* Result:
  `403 Forbidden` returned (WAF blocked XSS)

### ✅ Screenshot: Blocked by WAF

<img width="2905" height="1445" alt="image" src="https://github.com/user-attachments/assets/dd18de1d-953e-4d7c-bc39-2d01dc3b99fb" />


### Step 6: Configure Private DNS Resolution for SQL

* VM can't resolve by default: `NXDOMAIN`
* After Private Endpoint is created:

  ```bash
  nslookup sqlsvr-secureweb.database.windows.net
  ```

  → Resolves to `10.x.x.x`

### Step 7: Confirm SQL is NOT Public

* From browser: Public IP not reachable
* SQL is only accessible inside VNet
<img width="2109" height="495" alt="image" src="https://github.com/user-attachments/assets/317e92aa-bee4-413a-ac8d-127efabb74e5" />


---

## 🔍 Learnings
| Concept              | Explanation                                                              |
| -------------------- | ------------------------------------------------------------------------ |
| **WAF vs Firewall**  | WAF works at L7 for HTTP protection; Firewall secures full L3–L7 traffic |
| **Private Endpoint** | Maps Azure PaaS service (SQL) to internal IP to avoid public exposure    |
| **App Gateway**      | Load balances + provides WAF security and reverse proxy                  |

---



---

## ✅ Outcome

* ✅ Web app accessed securely via Application Gateway
* ✅ SQL DB locked behind private endpoint
* ✅ WAF blocked common attack vectors

> This project showcases enterprise-grade layered security using native Azure Networking services.

---


# **3-Tier Architecture in Azure**

## **Security Measures and Their Purpose**

| Security Measure         | Purpose |
|-------------------------|------------------------------------------------------------|
| **NVA (Firewall)**      | Filters and inspects traffic before reaching backend VMs. |
| **Route Tables**        | Forces traffic through the NVA instead of direct subnet access. |
| **NIC (Network Interface Cards)** | Allows VMs to communicate with different networks. |
| **NSG (Network Security Groups)** | Controls inbound/outbound traffic at the subnet/VM level. |
| **IP Tables (Linux Firewall)** | Blocks unauthorized access at the VM level. |
| **IP Forwarding**       | Allows NVA to forward traffic between subnets. |

---

## **Key Components Explained**

### **NVA (Network Virtual Appliance)**
- Acts as a **firewall & packet filter** in the DMZ subnet.
- Controls **incoming & outgoing traffic** between App and DB VMs.
- Can be **Azure Firewall, Palo Alto, Fortinet**, etc.
- Helps enforce security rules (**e.g., allow only HTTP/HTTPS traffic to App VM**).

**Why is it important?**
- Prevents **direct access** to backend resources (**DB VM**).
- Filters **malicious traffic** before it reaches your app.
- Logs & **monitors traffic** for security analysis.

---

### **Route Tables**
- Defines where **network traffic** should be sent within your Azure **VNet**.
- Ensures that traffic flows **through the NVA (DMZ)** instead of going directly.

---

### **NIC (Network Interface Card)**
- A **virtual network adapter** that connects a VM to a subnet.
- Each **VM has at least one NIC**.
- The **NVA typically has two NICs**:
  - **External NIC** (connected to public subnet for inbound traffic).
  - **Internal NIC** (connected to private subnet for outbound traffic).

**Why is it important?**
- Allows a VM to communicate with **different subnets** or networks.
- **Multiple NICs** enable **traffic segmentation & better security**.

---

### **NSG (Network Security Group)**
- **Firewall-like rules** that allow/deny traffic to **VMs or subnets**.
- Works at the **subnet or NIC level**.
- Used to **control inbound/outbound traffic**.

---

### **IP Tables (For Linux Firewalls)**
- **Linux-based firewall** used inside the NVA or VMs to **filter packets**.
- Can be used to **allow/block traffic** based on:
  - **Source**
  - **Destination**
  - **Port**
  - **Protocol**

---

### **IP Forwarding**
- Allows the **NVA to forward packets** between networks.
- Needed for the **NVA to inspect and send traffic** between App and DB VMs.

**How to Enable IP Forwarding on the NVA:**
1. Go to **Azure Portal** → **NVA VM** → **Networking**.
2. Click on the **Network Interface**.
3. Under **IP Configurations**, enable **IP Forwarding**.

🔹 **Why is it important?**
- Ensures **traffic is not blocked** by default on the NVA.
- Allows **proper routing of traffic** between subnets.

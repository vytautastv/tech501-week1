# Setting Up a VNet with Public and Private Subnets, and SSH into an Azure VM

## Prerequisites
- An Azure account with appropriate permissions to create resources.
- Azure CLI or access to the Azure Portal.
- An SSH key pair for authentication.

---

## Steps to Set Up a VNet with Public and Private Subnets

### 1. Create a Virtual Network
1. Open the **Azure Portal** or use the Azure CLI.
2. Navigate to **Virtual Networks** and click **+ Create**.
3. Fill in the following:
   - **Resource Group:** Choose or create a resource group.
   - **Name:** Give the VNet a meaningful name (e.g., `my-vnet`).
   - **Region:** Select the desired Azure region.

### 2. Add Subnets
1. Under the VNet configuration, add two subnets:
   - **Public Subnet:**
     - Name: `public-subnet`
     - Address Range: Specify (e.g., `10.0.1.0/24`).
   - **Private Subnet:**
     - Name: `private-subnet`
     - Address Range: Specify (e.g., `10.0.2.0/24`).
2. Ensure the public subnet will have a **Network Security Group (NSG)** to allow SSH and other necessary traffic.

---

## Steps to Create a Virtual Machine in the Public Subnet

### 1. Create a Virtual Machine
1. Navigate to **Virtual Machines** and click **+ Create**.
2. Fill in the required details:
   - **Resource Group:** Use the same group as the VNet.
   - **Name:** Name your VM (e.g., `my-vm`).
   - **Region:** Match the VNet’s region.
   - **Image:** Choose an OS image (e.g., Ubuntu Server 20.04 LTS).
   - **Authentication Type:** Select **SSH public key** and upload your public key (`~/.ssh/id_rsa.pub`).
   - **Size:** Choose a VM size (e.g., Standard_B2s).
3. Under **Networking**, select:
   - **Virtual Network:** Choose the VNet you created.
   - **Subnet:** Choose the `public-subnet`.
   - **Public IP Address:** Ensure a public IP is assigned for external access.

### 2. Review and Create
1. Click **Review + Create** and validate the configuration.
2. Click **Create** to deploy the VM.

---

## Steps to SSH into the Azure Virtual Machine

### 1. Retrieve the Public IP Address
1. Go to the **Virtual Machine** resource in the Azure Portal.
2. Under **Overview**, note the **Public IP Address**.

### 2. SSH into the VM
Run the following command in your terminal:
```bash
ssh -i ~/.ssh/id_rsa <username>@<public-ip>

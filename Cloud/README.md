# Cloud Computing Notes

## 1. How Do We Know if Something Is in the Cloud?
**Definition:** Cloud computing means storing and accessing data or applications over the internet rather than on local servers or personal devices.

### Key Indicators:
- Accessible from multiple devices via the internet.
- Managed by a third-party provider (e.g., AWS, Azure).
- Billed on a pay-as-you-go or subscription model.
- Scalable resources (storage, compute) without physical upgrades.

---

## 2. Differences Between On-Premises and Cloud

| **Feature**    | **On-Premises**                | **Cloud**                                  |
|----------------|--------------------------------|-------------------------------------------|
| **Location**   | Data stored on local servers  | Data stored on third-party servers        |
| **Cost**       | High upfront costs (CapEx)    | Lower upfront costs, ongoing fees (OpEx)  |
| **Scalability**| Limited by hardware capacity  | Easily scalable                           |
| **Maintenance**| Requires in-house management | Managed by cloud provider                 |
| **Accessibility** | Limited to on-site or VPN connections | Accessible anywhere with internet |

---

## 3. The 4 Cloud Deployment Models

| **Deployment Model** | **Description**                                           | **Use Cases / How It Works**                          |
|-----------------------|-----------------------------------------------------------|-----------------------------------------------------|
| **Private Cloud**     | Dedicated infrastructure for one organization. Managed internally or by a third party. | High-security environments like financial institutions. |
| **Public Cloud**      | Shared infrastructure hosted by providers (e.g., AWS, Google Cloud). | Cost-effective for startups and SMBs.              |
| **Hybrid Cloud**      | Combination of private and public clouds, connected securely. | Data-sensitive businesses with scalability needs.   |
| **Multi-Cloud**       | Use of multiple public clouds for different services. | Avoid vendor lock-in or enhance resilience.        |

---

## 4. Types of Cloud Services

| **Service Type** | **Description**                                | **Examples**           |
|-------------------|-----------------------------------------------|------------------------|
| **IaaS**          | Infrastructure-as-a-Service: Provides virtualized hardware resources. | AWS EC2, Azure VMs     |
| **PaaS**          | Platform-as-a-Service: Offers a development environment for building apps. | Google App Engine      |
| **SaaS**          | Software-as-a-Service: Delivers ready-to-use software over the internet. | Google Workspace       |

### Differences:
- **IaaS** is the foundation (hardware).
- **PaaS** adds development tools to build applications.
- **SaaS** delivers complete software solutions.

---

## 5. Advantages and Disadvantages of the Cloud

### Advantages:
- **Scalability:** Resources scale with demand.
- **Cost Efficiency:** Pay-as-you-go model minimizes upfront costs.
- **Flexibility:** Accessible from anywhere.
- **Disaster Recovery:** Built-in redundancy and backups.

### Disadvantages:
- **Security Concerns:** Sensitive data is stored externally.
- **Downtime Risks:** Dependence on internet connectivity.
- **Vendor Lock-in:** Migration between providers can be challenging.

---

## 6. CapEx vs. OpEx in Relation to the Cloud

| **Cost Type** | **Description**                     | **Relation to Cloud**                      |
|---------------|-------------------------------------|--------------------------------------------|
| **CapEx**     | Capital Expenditure: Upfront hardware costs. | On-premises setups require CapEx.          |
| **OpEx**      | Operational Expenditure: Ongoing expenses. | Cloud shifts costs to a subscription model (OpEx). |

---

## 7. Is Migrating to the Cloud Always Cheaper?
- **Yes:** For startups, small businesses, and fluctuating workloads due to no upfront investments.
- **No:** For large enterprises with consistent workloads, on-prem may still be cost-effective.

---

## 8. Market Share and Trends
### Major Players (as of latest data):
- **AWS (~32%)**
- **Microsoft Azure (~23%)**
- **Google Cloud (~11%)**

### Trend:
- Increased growth in multi-cloud and hybrid deployments.

---

## 9. What Are the 3 Largest Cloud Providers Known For?
- **AWS:** Broadest range of services, early market leader, robust ecosystem.
- **Azure:** Strong integration with Microsoft products (e.g., Office 365).
- **Google Cloud:** Superior data analytics and AI/ML capabilities.

---

## 10. Which Cloud Provider Is Best? Why?
- **AWS:** Best for startups and diverse workloads.
- **Azure:** Best for businesses already using Microsoft products.
- **Google Cloud:** Best for data-driven companies.

---

## 11. What Do You Pay for When Using the Cloud?
- Compute (VMs, containers)
- Storage (data, backups)
- Bandwidth (data transfer)
- Managed services (e.g., databases, AI tools)
- Support plans

---

## 12. The 4 Pillars of DevOps
1. **Culture:** Collaboration between teams.
2. **Automation:** CI/CD pipelines.
3. **Lean Practices:** Minimize waste in processes.
4. **Measurement:** Continuous feedback and monitoring.67

# File Ownership and Permissions in Linux

## Why is managing file ownership important?
Managing file ownership is important because it helps control access to files and directories. Proper ownership ensures:
- Only authorized users can modify or execute sensitive files.
- Group collaboration is streamlined by assigning files to specific groups.
- System security is maintained by restricting access to critical files.

Incorrect ownership settings can lead to unauthorized access, data loss, or security vulnerabilities.

---

## What is the command to view file ownership?
The `ls -l` command is used to view file ownership. For example:
```bash
ls -l

## Example

-rwxr–r– 1 adminuser developers 512 Jan 23 10:15 script.sh

### Breakdown of Permissions:
1. **File Type**:
   The first character indicates the file type:
   - `-`: Regular file.
   - `d`: Directory.
   - `l`: Symbolic link.

2. **Permissions**:
   The next 9 characters (`rwxr--r--`) represent permissions:
   - `rwx`: **User (owner)** has read, write, and execute permissions.
   - `r--`: **Group** (developers) has read-only permission.
   - `r--`: **Other** (everyone else) has read-only permission.

3. **Links**:
   `1` indicates the number of hard links to the file.

4. **Owner**:
   The owner of the file is `adminuser`.

5. **Group**:
   The file belongs to the group `developers`.

6. **File Size**:
   The file size is `512` bytes.

7. **Last Modified Date**:
   The file was last modified on `Jan 23` at `10:15`.

8. **File Name**:
   The file is named `script.sh`.

### Key Notes:
- The owner `adminuser` can read, write, and execute the file.
- Members of the `developers` group can only read the file.
- All other users can only read the file.

---

## Additional Notes
- **Changing Permissions**:
  Use `chmod` to modify permissions. Example:
  `chmod u+x file.sh` (adds execute permission for the owner).

- **Changing Ownership**:
  Use `chown` to change ownership. Example:
  `sudo chown user:group file.sh`.

- **Numeric Representation of Permissions**:
  Each permission set can be represented by a number:
  - `r` = 4, `w` = 2, `x` = 1.
  Example: `chmod 754 file.sh` means:
  - User: `7` (read + write + execute).
  - Group: `5` (read + execute).
  - Other: `4` (read only).


What permissions are set when a user creates a file or directory? Who does the file or directory belong to?

When a user creates a file or directory:
	•	The file or directory belongs to the user (as the owner) and their primary group.
	•	The default permissions are determined by the user’s umask (user file-creation mask), which subtracts permissions from the maximum possible values (777 for directories, 666 for files).

For example:
	•	If the umask is 022, a newly created file will have 644 permissions (rw-r--r--).
	•	If the umask is 022, a newly created directory will have 755 permissions (rwxr-xr-x).



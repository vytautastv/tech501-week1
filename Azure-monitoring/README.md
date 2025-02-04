Azure VM Monitoring & Dashboard Setup

Azure provides built-in monitoring and diagnostics tools to track CPU usage, network traffic, disk activity, and memory usage. You can set up a dashboard in Azure Monitor to visualize these metrics.

✅ Step 1: Enable Monitoring for Your Azure VM
	1.	Go to Azure Portal: https://portal.azure.com
	2.	Navigate to “Virtual Machines”: Select your VM.
	3.	Click on “Monitoring” → “Insights”:
	•	This will show CPU, network, disk, and memory usage in real-time.
	4.	Enable Diagnostics (if not enabled):
	•	Go to “Monitoring” → “Diagnostic settings”.
	•	Click “Enable guest-level monitoring”.
	•	Choose “Send to Log Analytics” (to store and query logs).
	•	Select metrics such as CPU usage, network in/out, disk IOPS.


✅ Step 2: Create an Azure Dashboard
	1.	Go to “Azure Monitor” from the search bar.
	2.	Click on “Dashboards” → “New Dashboard”.
	3.	Click “Add Tile” and select “Metrics Chart”.
	4.	Choose your VM as the resource and select:
	•	CPU Utilization (%)
	•	Network In & Out (Bytes)
	•	Disk Read & Write Ops
	5.	Click “Apply” and save the dashboard.


✅ Step 3: Set Alerts for High CPU or Network Usage
	1.	Go to “Monitoring” → “Alerts” in your VM.
	2.	Click “New alert rule”.
	3.	Set:
	•	Condition: Select “Percentage CPU” > 80%.
	•	Action Group: Choose Email, SMS, or Webhook.
	•	Severity: Choose Warning or Critical.
	4.	Click “Create” to enable alerts.

Load Testing with Apache Benchmark (ab Command)

Apache Benchmark (ab) is a tool for testing server performance by sending a large number of HTTP requests to a server.

Basic Syntax
ab -n 1000 -c 100 http://172.167.12.36/

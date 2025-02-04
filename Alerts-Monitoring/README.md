	1.	Login to Azure Portal:
	•	Go to Azure Portal and log in with your Azure credentials.
	2.	Navigate to Monitor:
	•	In the left-hand sidebar, click on Monitor.
	3.	Create a New Alert:
	•	Under Monitoring, click on Alerts.
	•	Click on + New alert rule at the top of the page.
	4.	Select a Resource:
	•	Click Select a resource.
	•	Choose your Virtual Machine (the one where your app is hosted).
	5.	Set the Condition:
	•	Click on Add Condition.
	•	In the search bar, type CPU usage and select Percentage CPU from the available metrics.
	•	Set the condition to greater than and enter a threshold value. For example:
	•	Threshold: 80 (this will trigger the alert when CPU usage exceeds 80%).
	•	Select the time aggregation as Average to check the CPU usage every minute.
	6.	Configure the Action:
	•	Click on Add action group to create an action group if you haven’t already.
	•	Under Action type, select Email and enter the recipient email address where you want to receive the alert.
	7.	Define Alert Details:
	•	Give your alert rule a Name (e.g., “High CPU Usage Alert”).
	•	Optionally, set the Severity (e.g., 3 for warning or 2 for critical).
	8.	Review and Create:
	•	After configuring the alert, review the settings, and click Create.


install stress tools:
  sudo apt-get update
  sudo apt-get install stress

run stress test:
stress --cpu 1 --timeout 300s

check monitoring tools to see CPU usage, see if alert is triggered, see if alert email receieved.



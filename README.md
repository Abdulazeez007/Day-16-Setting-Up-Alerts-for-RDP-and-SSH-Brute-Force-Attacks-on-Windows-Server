# Day-16-Setting-Up-Alerts-for-RDP-and-SSH-Brute-Force-Attacks-on-Windows-Server

Monitoring and protecting your Windows Server from brute-force attacks is essential for ensuring system security. One of the most common attack vectors is brute-forcing login credentials, particularly for protocols like Remote Desktop Protocol (RDP) and Secure Shell (SSH). In this blog, I’ll walk you through how to observe authentication logs from your Windows server using Elastic, and create custom alerts that notify you when brute force attempts are detected.

Let’s get started!

## Step 1: Monitoring RDP Brute Force Activity
To begin, we will set up an alert for RDP brute force attempts by analyzing authentication logs. We’ll use Event ID 4625, which is triggered for every failed login attempt on the Windows Server.

### 1.1 Access the Elastic Discover Tab
- Open the Elastic GUI and click the hamburger icon on the top left corner.
- Select **Discover** from the menu.

### 1.2 Filter for RDP Failed Logins
To narrow down the logs to only show activity from your Windows server, filter by the server’s name. 

In this case, I’m selecting **aurora-WIN-aurora** as the server to monitor.

Now, since we’re looking for brute force attempts, let’s focus on Event ID 4625, which logs every failed login attempt. In the search bar, type the following query:

```bash
event.code: 4625
```
Hit **Update** to apply the filter.

### 1.3 Fields to Track for Brute Force Detection
To detect brute force attacks, we want to focus on three key fields:
- **Failed Attempts:** The number of failed login attempts.
- **User:** The account that is being targeted.
- **Source IP:** The IP address where the attack is coming from.

Add these fields to your view and save your work:
- Click the **Save** button in the top-right corner.
- Name the view **RDP Failed Activity**.

Now that you’ve configured the log monitoring, let’s set up an alert!

## Step 2: Create an Alert for RDP Brute Force Attempts
### 2.1 Create a Search Threshold Rule
- Click the **Alert** button at the top and select **Create search threshold rule**.
- Name the alert something descriptive, such as **RDP Brute Force Detection**.
- Scroll down to the query section, ensuring that your query is still set to:

```bash
event.code: 4625
```
Once you’re done, click **Save**. Your alert rule is now active!

## Step 3: Set Up a Custom Rule for SSH Brute Force Detection
While the RDP brute force alert is now live, we also need to monitor SSH brute force attempts, especially targeting the root user. In this case, we’ll create a custom detection rule since the default rules don’t give us all the details we need.

### 3.1 Navigate to the Detection Rules
- From the left-hand menu, go to the **Security** tab and click on **Detection Rules (SIEM)**.
- Ignore Elastic’s prebuilt rules for now. Instead, we’ll create our own by clicking **Create New Rule** at the top-right corner.

### 3.2 Define the SSH Brute Force Rule
- Select **Threshold** as the rule type.
- For the index pattern, leave it as is. In the custom query field, enter the following query:

```bash
system.auth.ssh.event: * AND agent.name: aurora-Linux-aurora AND system.auth.ssh.event: Failed AND user.name: root
```
- For the group by fields, select both **user.name** and **source.ip**.
- Set the threshold to 5, meaning if 5 failed login attempts happen within a specified time frame, the rule will trigger.

### 3.3 Name and Configure the Rule
- Name the rule something descriptive, like **SSH Brute Force Attempt**.
- In the description field, write:

  > This rule detects failed authentication attempts targeting the root account.
  
- Set the Severity to **Medium**. For Risk Score, you can leave it blank.
- Under **Advanced Settings**, scroll down to **Custom Highlighted Fields** and select **source.ip**.
- Set the rule to run every 5 minutes, with an additional look-back period of 5 minutes.
- Once configured, hit **Create & Enable Rule**.

## Step 4: Apply the Same Settings for RDP Failed Authentication
You can follow the same steps used for the SSH brute force rule to create an RDP brute force alert with more advanced custom settings. Afterward, ensure that both rules are active and monitoring for any suspicious login activity.

## Step 5: Monitor Alerts for Brute Force Activity
With both the RDP and SSH brute force detection rules in place, you’ll now be able to receive alerts whenever multiple failed authentication attempts are detected. To view these alerts:
- Head over to the **Alerts** section from the left-hand menu.
- From there, you’ll see all recent alerts, enabling you to quickly investigate suspicious login attempts.

## Conclusion
By following the steps outlined in this blog, you’ve successfully configured custom alerts for both RDP and SSH brute force attacks using Elastic. This proactive approach ensures you’ll be notified whenever someone attempts to brute-force their way into your Windows server, giving you the insights you need to take timely action.

Now, your server is more secure, and you’re better equipped to handle potential threats. Stay vigilant, and keep your systems safe!




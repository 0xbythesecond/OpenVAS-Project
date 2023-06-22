# Azure Vulnerability Management Lab: Assessing and Remediating Security Risks (In Process)

## Introduction:
In this lab, we will guide you through the process of setting up a vulnerability management environment in Azure. You will learn how to configure and utilize OpenVAS, a vulnerability scanner, to identify and assess security vulnerabilities in a Windows virtual machine (VM). Additionally, you will perform credentialed scans, apply remediations, and verify the effectiveness of the remediation measures.

## Prerequisites:
- Computer with Internet access
- Azure Account (Free subscription may be sufficient)
- Access to Azure Portal
  - Sign up: `https://azure.microsoft.com/en-us/free/`
  - Login: `https://portal.azure.com`

Lab Steps:

<details> 
  
<summary>
  
### Task 1: Prepare the Vulnerability Management Scanner
  
</summary>

Access the Azure Portal and navigate to the Marketplace.
Search for "OpenVAS secured and supported by HOSSTED" and select it.
Choose a pre-set configuration and create the VM with specified settings.

| Name | Input|
|---|---|
| Resource Group:| Vulnerability-Management|
| VM Name:| OpenVAS (Take note of the region and Vnet–consider East US )|
| Region: | East US (`make sure to note the region and Vnet`)|
| Authentication: | Password (Provide Username/Password)|

- On the Disk Tab, Networking, Management, and Advanced we will let them remain as their default Settings.
- For the Monitoring tab, we will disable boot diagnostics as it will not be needed in the lab. You can learn more about boot diagnostics [here](https://learn.microsoft.com/en-us/azure/virtual-machines/boot-diagnostics).
- Review Create → Create


- Connect to the VM via SSH using PowerShell (Windows) or Terminal (MacOS).
- Wait until the OpenVAS deployment completes and access the web app URL.
- Log in with provided credentials or try admin/admin if needed.
- Reset the admin password to a password of your choosing in the example I will be using "GoneAgain123!".

</details>

#

<details>
  
<summary>
  
### Task 2:Create a Client Virtual Machine and Make it Vulnerable

</summary>

Create a new Virtual Machine in Azure Portal, following specified settings
Ensure you can RDP into the VM after it's created.
Disable the Windows Firewall and install outdated software.
Restart the VM and leave it for now.
</details>

#

<details>
  
<summary> 
  
### Task 3: Configure OpenVAS for Unauthenticated Scan against Vulnerable VM

</summary>

Log in to OpenVAS and add the Client VM's private IP address as a new host.
Create a new target named "Azure Vulnerable VMs" using the host information.
Create a new task named "Scan - Azure Vulnerable VMs" with the target.
Start the scan and review the results once it's completed.
</details>

#

<details>
  
<summary>
  
  ### Task 4: Configure Credentialed Scans within VM and OpenVAS

</summary>

Make necessary configurations within the vulnerable VM (Windows settings).
Make corresponding configurations in OpenVAS for credentialed scans.
</details>

#

<details>
 <summary> 
   
### Task 5: Execute Credentialed Scan against Vulnerable VM

</summary>

Clone the previous scan task and edit it for credentialed scanning.
Launch the new credentialed scan and wait for it to finish.
Observe the differences in findings compared to the unauthenticated scan.
</details>

#

<details>

<summary>
  
### Task 6: Remediate Vulnerabilities

</summary>

Log back into the Win10-Vulnerable VM and uninstall outdated software.
Restart the VM to apply the changes.
</details>

#

<details>

<summary>
    
### Task 7: Verify Remediations

</summary>

Re-initiate the credentialed scan and observe the updated results.
</details>

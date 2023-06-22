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

| Setting | Value|
|---|---|
| Resource Group:| Vulnerability-Management|
| VM Name:| OpenVAS (Take note of the region and Vnet–consider East US )|
| Region: | East US (`make sure to note the region and Vnet`)|
| Authentication: | Password (Provide Username/Password)|

- On the Disk Tab, Networking, Management, and Advanced we will let them remain as their default Settings.
- For the Monitoring tab, we will disable boot diagnostics as it will not be needed in the lab. You can learn more about boot diagnostics [here](https://learn.microsoft.com/en-us/azure/virtual-machines/boot-diagnostics).
- Review Create → Create


- Connect to the VM via SSH using PowerShell (Windows) or Terminal (MacOS).
- Wait until the OpenVAS deployment completes and access the web app URL (For this example: `https://172.190.177.16.c.hossted.com`).

<p align="center"><img src="https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/f96c5464-ada1-4817-8d84-84705873ea64" height="70%" width="70%" alt="SSH Login to OpenVAS VM"/></p>

- Log in with provided credentials or try admin/admin if needed (admin/admin was used below).  
  <br />
![Open VAS Login (admin)](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/75af9a9d-d250-465c-92e1-47defa898c99)

- Reset the admin password to a password of your choosing in the example I will be using "incorrect".
  - To change the password, you will go to the person icon at the top right of the page, then select the pencil/note icon near the top left of the page. You will then be shown a pop-up to make the change of the old password to the new password.
  - If for some reason there is no default value shown for rows per page, you can enter 10 then click save. If this doesn't note and it doesn't accept your input, you can try a different browser using the webapp url to change the password. 

</details>

#

<details>
  
<summary>
  
### Task 2:Create a Client Virtual Machine and Make it Vulnerable

</summary>

- Create a new Virtual Machine in Azure Portal, following specified settings
  
| Setting | Value|
|---|---|
| Resource Group:| Vulnerability-Management (Same as Previous)|
| VM Name:| Win10-Vulnerable|
| Region: | Same as the OpenVAS VM (East US)|
| Virtual Network: | Same as OpenVAS (this is important)|
| Image: | Windows 10 Pro|
| Size: | Any size with 2 vCPUs|
| Username: | Labuser / incorrect! (whichever you prefer and easy to remember)|
| Networking: | Same Vnet as OpenVAS|

- The other tabs that are associated with the VM were left as default and no additional changes were made. 
- Review Create → Create the VM

  - Ensure you can RDP into the VM after it's created.
  - While inside the vulnerable VM, Disable the Windows Firewall and install [outdated software](https://drive.google.com/drive/folders/1n83ilCjZWZulbDdYnUe9wQPK2buY47_U)(be sure that this is done inside the virtual machine).
  - You will install each of them with the defaults as there will be no changes/adjustments necessary here. 
  - Restart the VM and leave it for now.
</details>

#

<details>
  
<summary> 
  
### Task 3: Configure OpenVAS for Unauthenticated Scan against Vulnerable VM

</summary>

- Log in to OpenVAS and add the Client VM's (Win-10 Vulnerable) private IP address as a new host.
  - Hover over Assets → Host → New Host Icon at the top left.
- Create a new target named "Azure Vulnerable VMs" using the host information.
  
![Create a new target from the host](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/db7970bb-8241-4d01-977f-47b224a20061)

  >**Note**: Take note of the credentials as you scroll down the page below and leave them as their default. We will add SMB credentials later.
![New target azure vulnerable vms](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/f340338d-e4ce-4f03-b97e-40d3e4c714ac)

  
- Create a new task named "Scan - Azure Vulnerable VMs" with the target.
  - Hover over Scans → Task → New Task Icon at the top left
  - Scan Targets → “Azure Vulnerable VMs” (This is the target that we created previously)
![Create a new scan task](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/3f0a2585-b5f8-4c2d-acce-21c1b14d74d4)

- Start the scan and review the results once it's completed.
  - The status will change from Requested  → Queued →  Percentage Loaded → Done
  ![Start uncredentialed vulnerability scan](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/d89246f1-b32f-4cf3-ba69-acc87896ee02)
  - After pressing the ▶️ button, the Status reflects to be loading
  ![Uncredentialed scan status loading](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/d888d64b-2a5f-40c8-ae3a-2cacb9d21daa)
  - Status is now Done and you can select the report to review the vulnerabilities for the uncredentialed scan. 
  ![Uncredentialed scan done](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/1e2d7022-2ba6-40a0-a94c-20f52c2fb09c)

- To remove the filtered results of the report, you can select the `X` near the top of the page and it will display more vulnerabilities.
  >**Note**: Once the filter is removed here, it will display those that include a `0.0` as their severity level as well. 
![Filtered results of non-credentialed scan](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/3c9aeaa6-1d41-4ad5-a6b0-835aed0a605c)



</details>

#

<details>
  
<summary>
  
  ### Task 4: Configure Credentialed Scans within VM and OpenVAS

</summary>

- Make necessary configurations within the vulnerable VM (Windows settings).
- Disable Firewall (done in Task 2 if you happen to have missed it there)
 - Disable User Account Control
![disable user account control](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/7565fd04-bc78-4ad1-8090-e149fb53e525)
 - Enable Remote Registry
 - Set Registry Key
 - Launch Registry Editor (regedit.exe) in “Run as administrator” mode and grant Admin Approval, if requested
 - Navigate to HKEY_LOCAL_MACHINE hive
 - Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key
 - Create a new DWORD (32-bit) value with the following properties:  Name: LocalAccountTokenFilterPolicy  Value: 1
 - Close Registry Editor  
 - Restart the VM

- Make corresponding configurations in OpenVAS for credentialed scans.
  - Go to Configuration → Credentials → New Credential
  - Name / Comment → “Azure VM Credentials”
| Settings | Value |
|----|----|
|Allow Insecure Use: | Yes|
| Username: | azureuser |
| Password: | incorrect!|
Save
Go to Configuration → Targets → CLONE the Target we made before
NEW Name / Comment: “Azure Vulnerable VMs - Credentialed Scan”
Ensure the Private IP is still accurate
Credentials 

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

# Azure Vulnerability Management Lab: Assessing and Remediating Security Risks (In Process)

![Azure Vulnerability Management Lab Assessing and Remediating Security Risks](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/e58df21b-9f53-4ba4-b6a9-9a31d5b9ea94)


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

- Access the Azure Portal and navigate to the Marketplace.
- Search for "OpenVAS secured and supported by HOSSTED" and select it.
- Choose a pre-set configuration and create the VM with specified settings.
  ![Choose General Purpose Virtual Machine](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/bf4d0933-1263-4113-86ae-46a83ca26455)
- Virtual Machine Settings

| Setting | Value|
|---|---|
| Resource Group:| Vulnerability-Management|
| VM Name:| OpenVAS (Take note of the region and Vnet–consider East US )|
| Region: | East US (`make sure to note the region and Vnet`)|
| Authentication: | Password (Provide Username/Password)|

<img src="https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/188732d1-c712-4167-bc04-6653212c3400" height="60%" width="60%" alt="Open VAS VM Basic Settings"/>


- On the Disk Tab, Networking, Management, and Advanced we will let them remain as their default Settings.
- For the Monitoring tab, we will disable boot diagnostics as it will not be needed in the lab. You can learn more about boot diagnostics [here](https://learn.microsoft.com/en-us/azure/virtual-machines/boot-diagnostics).
  ![Open VAS VM Monitoring Settings](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/8c88a1a5-352a-449d-99c0-71af1b8c05bd)

- Review Create → Create


- Connect to the OpenVAS VM via SSH using PowerShell (Windows) or Terminal (MacOS).
- Wait until the OpenVAS deployment completes and access the web app URL (For this example: `https://172.190.177.16.c.hossted.com`).

<p align="center"><img src="https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/f96c5464-ada1-4817-8d84-84705873ea64" height="70%" width="70%" alt="SSH Login to OpenVAS VM"/></p>

- Log in with provided credentials or try admin/admin if needed (admin/admin was used below).  
  <br />
![Open VAS Login (admin)](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/75af9a9d-d250-465c-92e1-47defa898c99)

- Reset the admin password to a password of your choosing in the example I will be using "incorrect".
  - To change the password, you will go to the person icon at the top right of the page, then select the pencil/note icon near the top left of the page. You will then be shown a pop-up to make the change of the old password to the new password.
  ![Change Open VAS Password](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/f64d79d6-4250-4f98-8bb4-e2823299cbd1)

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

Make necessary configurations within the vulnerable VM (Windows settings).
- Disable Firewall (done in Task 2 if you happen to have missed it there)
 - Disable User Account Control
![disable user account control](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/7565fd04-bc78-4ad1-8090-e149fb53e525)
 - Enable Remote Registry
   - Do a quick search for `Services.msc` at the bottom left of the Windows machine.
   - Scroll down to Remote Registry  → Double click (to select)  → Startup Type set to `Automatic` → Start → Apply → OK. 
   <img src="https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/289f6e00-1fe1-44be-99db-8749c8ef0a62" height="70%" width="70%" alt="Enable remote registry"/>

 - Set Registry Key
 - Launch Registry Editor (regedit.exe) in “Run as administrator” mode and grant Admin Approval, if requested
 - Navigate to HKEY_LOCAL_MACHINE hive
 - Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key
 - Create a new DWORD (32-bit) value with the following properties:
 - ![create secret key](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/ca128c76-a593-4f7a-8470-62414b97f54d)
 - Name: LocalAccountTokenFilterPolicy
   - After DWORD (32-bit) is selected you will add `LocalAccountTokenFilterPolicy` without any spaces added.  
 - Value: 1
   - Double click → LocalAccountTokenFilterPolicy → Change Value from 0 to 1 → OK
   ![Edit dword value 1](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/29fc67a5-9e64-4fe7-b278-08035ca2356b)

 - Close Registry Editor  
 - Restart the VM

Make corresponding configurations in OpenVAS for credentialed scans.
  - Go to Configuration → Credentials → New Credential
  - Name / Comment → “Azure VM Credentials”
    
| Settings | Value |
|----|----|
|Allow Insecure Use: | Yes|
| Username: | azureuser |
| Password: | password (same password as vulnerable VM)|

- Save
![create new credentials openvas](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/35277a1e-6587-45c7-8b4c-99c71307fb66)

- Go to Configuration → Targets → CLONE the Target we made before
  ![Clone Targets](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/5bbf9b60-25d3-4dbe-853f-d15f067c1678)
- NEW Name / Comment: “Azure Vulnerable VMs - Credentialed Scan”
- Ensure the Private IP is still accurate
- Credentials → SMB → Select the Credentials we just made: Azure VM Credentials
 ![New Name for Target Credentialed Scan](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/4e616870-1c7d-41b3-8e14-ad27ab0d3c19)

- Save


</details>

#

<details>
 <summary> 
   
### Task 5: Execute Credentialed Scan against Vulnerable VM

</summary>

Clone the previous scan task and edit it for credentialed scanning.
- CLONE the “Scan - Azure Vulnerable VMs” Task, then Edit it:
- Name / Comment → “Scan - Azure Vulnerable VMs - Credentialed”
- Targets: Azure Vulnerable VMs - Credentialed Scan
![Create New Name for Credentialed Scan Clone](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/907e1986-717f-4ad5-9448-4df94d851f1c)
- Save
- Click the ▶️ button to launch the new Credentialed Scan, and wait for it to finish
 ![Start Credentialed Scan](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/1e41a8ff-96dd-44d5-bda9-17851d2f53f7)

  >**Note**: Since this is a credentialed scan, it will take longer than the last one. Wait for it to finish


Launch the new credentialed scan and wait for it to finish.
Observe the differences in findings compared to the unauthenticated scan.
![completed credentialed scan](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/cea21d19-fafe-462d-877b-727f1561117a)

Results of the credentialed scan
![credentialed scan results](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/dc952cc3-f93e-4361-9e13-0b6fe516d7fc)


</details>

#

<details>

<summary>
  
### Task 6: Remediate Vulnerabilities

</summary>

Log back into the Win10-Vulnerable VM and uninstall outdated software.
- Search Control Panel → Uninstall Programs → Select Each Outdated of the Programs (VLC media player 1.1.7, Mozilla Firefox (x64 en-US), Adobe Reader X)
![Unistall Programs](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/4f548d41-bb62-4eac-a832-d5bfb8c96357)
- Restart the VM to apply the changes.
</details>

#

<details>

<summary>
    
### Task 7: Verify Remediations

</summary>

Re-initiate the credentialed scan (“Scan - Azure Vulnerable VMs - Credentialed") and observe the updated results.
 >**Note**: In the trend column, we can notice that there is a downward trend now that we have removed the outdated programs.
![Credentialed scan results (removed outdated programs)](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/c26d47f1-7da6-4eb7-aa40-d3b578f7cc25)

- We can drill down into the results of the report to view the impact of the vulnerabilities that are remaining and the available solutions.
  ![expanded result of windows iexpress](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/8fc279e4-1355-4c05-9b4e-703ad0ffaeb6)

### Clean Up Resources

- Upon the completion of the lab, the resources can not be deleted and the easiest way for this to be done is by deleting the resource group as it contains all the resources.
![Delete resource group](https://github.com/0xbythesecond/OpenVAS-Project/assets/23303634/86c72d5b-876e-4c84-b54b-f2f13ec10528)

</details>

## Reflection:
This lab provided hands-on experience in setting up and using a vulnerability management scanner with Azure and OpenVAS. It highlighted the importance of proactive vulnerability management and the impact of misconfigurations and outdated software on system security.

Configuring OpenVAS for unauthenticated scans and performing the scans allowed me to identify vulnerabilities and understand the need for regular scanning to detect security risks.

Implementing credentialed scans and comparing the results with unauthenticated scans demonstrated the value of using proper credentials for accurate vulnerability identification.

Remediating vulnerabilities by uninstalling outdated software and verifying the changes through subsequent scans reinforced the importance of timely actions to reduce the attack surface.

## Conclusion:
This lab enhanced my understanding of vulnerability management and the continuous effort required for maintaining a secure environment. It emphasized the significance of proactive security practices, timely remediation, and the value of comprehensive scanning approaches.

I now possess practical knowledge and skills in vulnerability management using Azure and OpenVAS, ready to apply them in real-world scenarios and contribute to effective system protection.

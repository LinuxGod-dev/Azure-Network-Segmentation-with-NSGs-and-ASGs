# Azure-Network-Segmentation-with-NSGs-and-ASGs
This repository details the implementation of a secure virtual networking infrastructure in Microsoft Azure using Network Security Groups (NSGs) and Application Security Groups (ASGs).

# Project Overview
This repository details the implementation of a secure virtual networking infrastructure in Microsoft Azure using Network Security Groups (NSGs) and Application Security Groups (ASGs). The primary objective is to enforce the principle of least privilege by strictly controlling inbound traffic to different classes of Virtual Machines (VMs) deployed within the same subnet.

# Goals

- The project infrastructure is designed to meet the following security and functional requirements:
  - Web Servers (myVmWeb): Must be accessible via HTTP (Port 80) and HTTPS (Port 443) from the internet to display the IIS web page.
  - Management Servers (myVMMgmt): Must be accessible via Remote Desktop Protocol (RDP - Port 3389) from the internet for administrative access.
  - Isolation: External RDP access to the Web Servers is strictly denied.

 # Architecture

- The entire infrastructure is deployed in the East US region. All resources reside in a single Virtual Network (myVirtualNetwork) and its single subnet (default). Security filtering is applied at the subnet level via the NSG, with traffic rules targeting specific ASGs.
- Component	Type	Configuration
Virtual Network (VNet)	myVirtualNetwork	

Address Space: 10.0.0.0/16 
Subnet	default	
Address Range: 10.0.0.0/24 

Network Security Group	myNsg	
Associated with the default subnet 

Web Server ASG	myAsgWebServers	
Groups Web Server VMs (myVmWeb) 

Management Server ASG	myAsgMgmtServers	
Groups Management Server VMs (myVMMgmt)

# Inbound Security Rules in myNsg
  -The NSG rules use ASGs as their destination, allowing precise control over which traffic reaches which server group:
   1. Rule: Allow-Web-All
-Priority: 100 
-Source: Any (Internet) 
-Destination: myAsgWebServers 
-Protocol/Ports: TCP 80, 443 
-Action: Allow
   2. Rule: Allow-RDP-All
- Priority: 110 
- Source: Any (Internet) 
- Destination: myAsgMgmtServers 
- Protocol/Ports: TCP 3389 (RDP) 
- Action: Allow

# Prerequisites
1. Azure Subscription: An active Azure subscription where you have the Owner or Contributor role.
2. Resource Group: A resource group named AZ500LAB07 (or a name of your choice) to contain all resources.
3. Credentials: A Windows Server 2022 image is used for the VMs. You will need a username (e.g., Student) and a personal password for the VMs.

# Deployment Steps (Azure Portal Walkthrough)
The following steps mirror the manual configuration performed in the lab environment:

Exercise 1: Create the virtual networking infrastructure 
Estimated timing: 20 minutes 
For all the resources in this lab, we are using the East (US) region. Verify with your instructor this is the 
region to use for your class. 
In this exercise, you will complete the following tasks: 
• Task 1: Create a virtual network with one subnet. 
• Task 2: Create two application security groups. 
• Task 3: Create a network security group and associate it with the virtual network subnet. 
• Task 4: Create inbound NSG security rules to all traffic to web servers and RDP to the management 
servers. 
Task 1: Create a virtual network 
In this task, you will create a virtual network to use with the network and application security groups. 
1. Sign-in to the Azure portal https://portal.azure.com/. 
Sign in to the Azure portal using an account that has the Owner or Contributor role in the Azure 
subscription you are using for this lab. In this Cloudslice lab, this account is LabUser
56791286@LODSPRODMCA.onmicrosoft.com with password W7c6KeqK.

<img width="1366" height="768" alt="Screenshot (792)" src="https://github.com/user-attachments/assets/9142f081-9f73-4428-a7d7-33d44b2c0883" />

2. In the Azure portal, in the Search resources, services, and docs text box at the top of the 
Azure portal page, type Virtual networks and press the Enter key.

<img width="1366" height="768" alt="Screenshot (793)" src="https://github.com/user-attachments/assets/fb10883f-afdb-4391-8892-3b7b129d048a" />

3. On the Virtual networks blade, click + Create.

<img width="1366" height="768" alt="Screenshot (794)" src="https://github.com/user-attachments/assets/20fc8ff4-6a79-4681-be80-ddef930e93c5" />

4. On the Basics tab of the Create virtual network blade, specify the following settings (leave 
others with their default values) and click Next: IP Addresses: 

<img width="1366" height="768" alt="Screenshot (795)" src="https://github.com/user-attachments/assets/f1beef51-b526-496f-b3b6-9adb1ab4d5f1" />

5. On the IP addresses tab of the Create virtual network blade, set the IPv4 address 
space to 10.0.0.0/16, and, if needed, in the Subnet name column, click default, on the Edit 
subnet blade, specify the following settings and click Save:

<img width="1366" height="768" alt="Screenshot (797)" src="https://github.com/user-attachments/assets/58f6c9e3-8e4c-4dc6-9f1f-a2fa75c511f3" />

6. Back on the IP addresses tab of the Create virtual network screen, click Review + create. 

<img width="1366" height="768" alt="Screenshot (799)" src="https://github.com/user-attachments/assets/72b5f37d-1d3c-4236-aede-9dcb11f73396" />

Task 2: Create application security groups 
In this task, you will create an application security group. 
1. In the Azure portal, in the Search resources, services, and docs text box at the top of the 
Azure portal page, type Application security groups and press the Enter key.

<img width="1366" height="768" alt="Screenshot (800)" src="https://github.com/user-attachments/assets/efa787f3-dd5c-445b-ba41-793bd3ce9b99" />

2. On the Application security groups blade, click + Create.

<img width="1366" height="768" alt="Screenshot (802)" src="https://github.com/user-attachments/assets/b56f7546-1bb8-495c-a2a8-ccf870a6195b" />

3. On the Basics tab of the Create an application security group blade, specify the following 
settings:

<img width="1366" height="768" alt="Screenshot (803)" src="https://github.com/user-attachments/assets/8e1bf182-3852-478f-a5ed-0a384f1a3061" />

4. Click Review + create and then click Create. 

<img width="1366" height="768" alt="Screenshot (804)" src="https://github.com/user-attachments/assets/ecc88106-b0cb-40cc-b166-b9f4c0a17cdc" />

5. Navigate back to the Application security groups blade and click + Create.

<img width="1366" height="768" alt="Screenshot (805)" src="https://github.com/user-attachments/assets/45472009-d503-44f8-9023-c08365413030" />

6. On the Basics tab of the Create an application security group blade, specify the following 
settings: 
Setting 
Resource group 
Name 
Region 
Value 
AZ500LAB07 
myAsgMgmtServers 
East US 
This group will be for the management servers.

<img width="1366" height="768" alt="Screenshot (806)" src="https://github.com/user-attachments/assets/59bab02d-e798-4835-9380-f51a12d9a0ea" />

7. Click Review + create and then click Create.

<img width="1366" height="768" alt="Screenshot (807)" src="https://github.com/user-attachments/assets/c935b23e-f0a2-4076-8ea3-1f174a37202d" />

Task 3: Create a network security group and associate the NSG to the subnet 
In this task, you will create a network security group. 
1. In the Azure portal, in the Search resources, services, and docs text box at the top of the 
Azure portal page, type Network security groups and press the Enter key.

<img width="1366" height="768" alt="Screenshot (808)" src="https://github.com/user-attachments/assets/6fb55ee9-1eba-4ace-9970-61c82ee77e81" />

2. On the Network security groups blade, click + Create.

<img width="1366" height="768" alt="Screenshot (809)" src="https://github.com/user-attachments/assets/1a0cf92f-e7c4-4728-b66a-165b26cbc874" />

3. On the Basics tab of the Create network security group blade, specify the following 
settings:

<img width="1366" height="768" alt="Screenshot (810)" src="https://github.com/user-attachments/assets/6391658d-7823-4b69-aa6f-2ff4c7ad8fbe" />

4. Click Review + create and then click Create.

<img width="1366" height="768" alt="Screenshot (811)" src="https://github.com/user-attachments/assets/7a7f71b9-bf92-4e9f-af2b-06a18fd64b41" />

5. In the Azure portal, navigate back to the Network security groups blade and select 
the myNsg entry. Or select Go to resource if available.

<img width="1366" height="768" alt="Screenshot (812)" src="https://github.com/user-attachments/assets/f565094d-56ed-4b03-87c5-49259084d7ef" />

6. On the myNsg blade, in the Settings section, click Subnets and then select + Associate. 

<img width="1366" height="768" alt="Screenshot (813)" src="https://github.com/user-attachments/assets/e2ce6232-9e26-4125-8779-0fcde9a98a51" />

7. On the Associate subnet blade, specify the following settings and select OK:

<img width="1366" height="768" alt="Screenshot (814)" src="https://github.com/user-attachments/assets/969070bf-f083-491a-a186-4e2f9e87609d" />

Task 4: Create inbound NSG security rules to all traffic to web servers and RDP to the servers. 
1. On the myNsg blade, in the Settings section, click Inbound security rules. 

<img width="1366" height="768" alt="Screenshot (815)" src="https://github.com/user-attachments/assets/467a2ca8-8b1b-47b4-9619-6646d6e6168e" />

2. Review the default inbound security rules and then click + Add.

<img width="1366" height="768" alt="Screenshot (816)" src="https://github.com/user-attachments/assets/16210bed-7287-489a-9b49-cb87fd290da9" />

3. On the Add inbound security rule blade, specify the following settings to allow TCP ports 
80 and 443 to the myAsgWebServers application security group (leave all other values with their 
default values):

<img width="1366" height="768" alt="Screenshot (817)" src="https://github.com/user-attachments/assets/57eaeb47-7fee-4ed1-9125-381bc323bd55" />

<img width="1366" height="768" alt="Screenshot (818)" src="https://github.com/user-attachments/assets/30e9d576-5ce5-4470-9276-46f4ebb13a4f" />

4. Select the Add button on the Add inbound security rule page, to create the new inbound 
rule.

<img width="1366" height="768" alt="Screenshot (819)" src="https://github.com/user-attachments/assets/e3d2f99e-41e6-4dd9-a0ab-c9b7b6a0f9d8" />

5. On the myNsg blade, in the Settings section, click Inbound security rules, and then click + 
Add.

<img width="1366" height="768" alt="Screenshot (820)" src="https://github.com/user-attachments/assets/d8e72556-816f-4a7b-9672-b9e0bd7e7308" />

6. On the Add inbound security rule blade, specify the following settings to allow the RDP 
port (TCP 3389) to the myAsgMgmtServers application security group (leave all other values 
with their default values):

<img width="1366" height="768" alt="Screenshot (821)" src="https://github.com/user-attachments/assets/d4e50fba-efab-4784-8f15-af831d2b0b93" />

<img width="1366" height="768" alt="Screenshot (822)" src="https://github.com/user-attachments/assets/588850a2-9dc3-4b32-9361-9ea42801b1ec" />

7. Select Add on the Add inbound security rule page, to create the new inbound rule. 
Result: You have deployed a virtual network, network security with inbound security rules, and two 
application security groups.

<img width="1366" height="768" alt="Screenshot (823)" src="https://github.com/user-attachments/assets/e1b9ac9d-9f54-46f9-b245-510363ab42fd" />

Exercise 2: Deploy virtual machines and test network filters 
Estimated timing: 25 minutes 
In this exercise, you will complete the following tasks: 
• Task 1: Create a virtual machine to use as a web server. 
• Task 2: Create a virtual machine to use as a management server. 
• Task 3: Associate each virtual machines network interface to it's application security group. 
• Task 4: Test the network traffic filtering. 
Task 1: Create a virtual machine to use as a web server 
In this task, you will create a virtual machine to use as a web server. 
1. In the Azure portal, in the Search resources, services, and docs text box at the top of the 
Azure portal page, type Virtual machines and press the Enter key.

<img width="1366" height="768" alt="Screenshot (824)" src="https://github.com/user-attachments/assets/ad85548a-c99a-4e98-8004-2dffa22493f5" />

2. On the Virtual machines blade, click + Create and, in the dropdown list, click Virtual 
machine.

<img width="1366" height="768" alt="Screenshot (825)" src="https://github.com/user-attachments/assets/1bba548e-d1be-4502-87ae-3a6be4023ce7" />

3. On the Basics tab of the Create a virtual machine blade, specify the following settings 
(leave others with their default values):

<img width="1366" height="768" alt="Screenshot (826)" src="https://github.com/user-attachments/assets/c8154c72-5a4f-42a3-8697-d92797940e79" />

<img width="1366" height="768" alt="Screenshot (827)" src="https://github.com/user-attachments/assets/c7338b6d-b28f-4e9b-a4a9-c882c19747c3" />

<img width="1366" height="768" alt="Screenshot (828)" src="https://github.com/user-attachments/assets/ea729c9d-fb37-4dd1-b2f2-a3f2f50b5736" />

4. Click Next: Disks > and, on the Disks tab of the Create a virtual machine blade, set the OS 
disk type to Standard HDD and click Next: Networking >.


<img width="1366" height="768" alt="Screenshot (830)" src="https://github.com/user-attachments/assets/8ed3f807-3e35-4a49-a294-ed8e6d05b668" />

5. On the Networking tab of the Create a virtual machine blade, select the previously created 
network myVirtualNetwork and the default (10.0.0.0/24) subnet.


<img width="1366" height="768" alt="Screenshot (831)" src="https://github.com/user-attachments/assets/2444355b-fad1-4157-9136-c67f20a9da30" />

6. Under NIC network security group select None.

<img width="1366" height="768" alt="Screenshot (832)" src="https://github.com/user-attachments/assets/f320bd93-c3ae-499d-9fe2-3c6179f8e93d" />

7. Click Next: Management >, then click Next: Monitoring >. On the Monitoring tab of 
the Create a virtual machine blade, verify the following setting:

<img width="1366" height="768" alt="Screenshot (833)" src="https://github.com/user-attachments/assets/e9077664-6193-47aa-bd7e-cd639b21afc9" />

8. Click Review + create, on the Review + create blade, ensure that validation was successful 
and click Create. 

<img width="1366" height="768" alt="Screenshot (834)" src="https://github.com/user-attachments/assets/5e9e3119-e946-4325-82dd-f611d9bfeb3a" />

Task 2: Create a virtual machine to use as a management server. 
In this task, you will create a virtual machine to use as a management server. 
1. In the Azure portal, navigate back to the Virtual machines blade, click + Create, and, in the 
dropdown list, click Virtual machine.

<img width="1366" height="768" alt="Screenshot (835)" src="https://github.com/user-attachments/assets/55f5ea6c-a367-47c1-b910-42cb8a138553" />

2. On the Basics tab of the Create a virtual machine blade, specify the following settings 
(leave others with their default values):

<img width="1366" height="768" alt="Screenshot (836)" src="https://github.com/user-attachments/assets/56cdd337-43bb-4a8f-8a33-f062dd568eae" />


<img width="1366" height="768" alt="Screenshot (837)" src="https://github.com/user-attachments/assets/6700203f-7d8a-49d5-b607-ab7b7b3b0f01" />


<img width="1366" height="768" alt="Screenshot (838)" src="https://github.com/user-attachments/assets/b542eab3-6622-4114-932c-0aa5598888ba" />

For public inbound ports, we will rely on the precreated NSG. 
3. Click Next: Disks > and, on the Disks tab of the Create a virtual machine blade, set the OS 
disk type to Standard HDD and click Next: Networking >.

<img width="1366" height="768" alt="Screenshot (839)" src="https://github.com/user-attachments/assets/7fbef7c5-8a17-4d07-8c6f-46edff6dd266" />

4. On the Networking tab of the Create a virtual machine blade, select the previously created 
network myVirtualNetwork and the default (10.0.0.0/24) subnet.

<img width="1366" height="768" alt="Screenshot (840)" src="https://github.com/user-attachments/assets/6f26237d-dd99-444f-8722-6ca32dca30f0" />

5. Under NIC network security group select None.

<img width="1366" height="768" alt="Screenshot (841)" src="https://github.com/user-attachments/assets/90e77f64-ac7b-4bfe-a276-e1627cf45f80" />

6. Click Next: Management >, then click Next: Monitoring >. On the Monitoring tab of 
the Create a virtual machine blade, verify the following setting:

<img width="1366" height="768" alt="Screenshot (842)" src="https://github.com/user-attachments/assets/f7dce0aa-f01e-48d9-892e-ded832a291e5" />

7. Click Review + create, on the Review + create blade, ensure that validation was successful 
and click Create. 
Wait for both virtual machines to be provisioned before continuing.

<img width="1366" height="768" alt="Screenshot (843)" src="https://github.com/user-attachments/assets/f935a1a9-1ede-4729-bc9c-cd09194aa03d" />

Task 3: Associate each virtual machine's network interface to its application security group. 
In this task, you will associate each virtual machines network interface with the corresponding application 
security group. The myVMWeb virtual machine interface will be associated to the myAsgWebServers 
ASG. The myVMMgmt virtual machine interface will be associated to the myAsgMgmtServers ASG. 
1. In the Azure portal, navigate back to the Virtual machines blade and verify that both virtual 
machines are listed with the Running status. 

<img width="1366" height="768" alt="Screenshot (844)" src="https://github.com/user-attachments/assets/ebf05625-34c8-4b44-91cc-52a6718726eb" />

<img width="1366" height="768" alt="Screenshot (845)" src="https://github.com/user-attachments/assets/e0f7dea8-e29b-4195-b037-76e712c0b037" />

2. In the list of virtual machines, click the myVMWeb entry.

<img width="1366" height="768" alt="Screenshot (846)" src="https://github.com/user-attachments/assets/d77168f2-b285-42d4-af5b-d7ed36234fb6" />

3. On the myVMWeb blade, in the Networking section, click Network settings and then, on 
the myVMWeb | Networking settings blade, click the Application security groups tab.

<img width="1366" height="768" alt="Screenshot (847)" src="https://github.com/user-attachments/assets/5cd56089-6ed4-4bc7-9a5d-a8161f9de7f0" />

4. Click + Add application security groups, in the Application security group list, 
select myAsgWebServers, and then click Save.

<img width="1366" height="768" alt="Screenshot (848)" src="https://github.com/user-attachments/assets/ccd31bab-5938-42da-a9f7-2733784a7e47" />

5. Navigate back to the Virtual machines blade and in the list of virtual machines, click 
the myVMMgmt entry.

<img width="1366" height="768" alt="Screenshot (849)" src="https://github.com/user-attachments/assets/18a17cb5-2cca-4e38-b871-31bb3f75cea3" />

6. On the myVMMgmt blade, in the Networking section, click Networking settings and then, 
on the myVMMgmt | Networking settings blade, click the Application security groups tab.

<img width="1366" height="768" alt="Screenshot (850)" src="https://github.com/user-attachments/assets/e2fc0518-23f1-4d97-b889-1cf6df2fd854" />

7. Click + Add application security groups, in the Application security group list, 
select myAsgMgmtServers, and then click Add.

<img width="1366" height="768" alt="Screenshot (851)" src="https://github.com/user-attachments/assets/4bef176f-1414-4459-9d43-9a74633a6a36" />

Task 4: Test the network traffic filtering 
In this task, you will test the network traffic filters. You should be able to RDP into the myVMMgmnt 
virtual machine. You should be able to connect from the internet to the myVMWeb virtual machine and 
view the default IIS web page. 
1. Navigate back to the myVMMgmt virtual machine blade.

<img width="1366" height="768" alt="Screenshot (852)" src="https://github.com/user-attachments/assets/a021fd1d-63bb-44d6-8e58-e5b6ebea020e" />

2. On the myVMMgmt Overview blade, click Connect and, in the drop down menu, 
click Connect.

<img width="1366" height="768" alt="Screenshot (853)" src="https://github.com/user-attachments/assets/9ff62f9f-52b4-43f1-9b4d-e6981d807768" />

3. Download the RDP file and use it to connect to the myVMMgmt Azure VM via Remote 
Desktop. When prompted to authenticate, provide the following credentials:

4. Verify that the Remote Desktop connection was successful. At this point you have confirmed you 
can connect via Remote Desktop to myVMMgmt.

<img width="1366" height="768" alt="Screenshot (854)" src="https://github.com/user-attachments/assets/f6362a00-3fc0-496c-86e3-f616d43aeec3" />

5. In the Azure portal, navigate to the myVMWeb virtual machine blade.

<img width="1366" height="768" alt="Screenshot (855)" src="https://github.com/user-attachments/assets/59de8c16-940b-43c2-83fa-ed2d6de7d8dc" />

6. On the myVMWeb blade, in the Operations section, click Run command and then 
click RunPowerShellScript.

<img width="1366" height="768" alt="Screenshot (856)" src="https://github.com/user-attachments/assets/d2126b69-1ea3-47f1-854b-7796a897fca2" />

7. On the Run Command Script pane, run the following to install the Web server role 
on myVmWeb: 
powershellTypeCopy 
Install-WindowsFeature -name Web-Server -IncludeManagementTools 
Wait for the installation to complete. This might take a couple of minutes. At that point, you can 
verify that myVMWeb can be accessed via HTTP/HTTPS.

<img width="1366" height="768" alt="Screenshot (857)" src="https://github.com/user-attachments/assets/2b355183-b532-4ba5-b479-bf5ece33d4d3" />

8. In the Azure portal, navigate back to the myVMWeb blade.

<img width="1366" height="768" alt="Screenshot (858)" src="https://github.com/user-attachments/assets/f76e248a-4ed9-4f3d-971e-b19aa9de010c" />

9. On the myVMWeb blade, identify the Public IP address of the myVmWeb Azure VM.

<img width="1366" height="768" alt="Screenshot (859)" src="https://github.com/user-attachments/assets/ae571cc4-ef27-4392-b09f-26e73b7b4463" />

10. Open another browser tab and navigate to IP address you identified in the previous step. 
The browser page should display the default IIS welcome page because port 80 is allowed inbound 
from the internet based on the setting of the myAsgWebServers application security group. The 
network interface of the myVMWeb Azure VM is associated with that application security group. 
Result: You have validated that the NSG and ASG configuration is working and traffic is being correctly 
managed.

<img width="1366" height="768" alt="Screenshot (860)" src="https://github.com/user-attachments/assets/9645f2ab-0a39-4ba7-a8d8-ac261fa06260" />

# Conclusion
In summary, the deployed infrastructure is functionally correct and adheres to the principle of least 
privilege, providing a secure and fully operational environment for both production web services and 
administrative tasks.


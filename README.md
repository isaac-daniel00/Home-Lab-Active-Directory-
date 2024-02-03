# HomeLab using Active Directory with added users via Powershell

## Introduction

In the course of this project, I've constructed a homelab that makes use of active directory in a virtual environment. The architecture consists of:

- Oracle Virtual Box (2 VM's)
- Oracle Virtual Box extension pack
- Windows 10 ISO
- Windows Server 2019 ISO

## Downloading the required software and ISO files

The Oracle Virtual Box software can be downloaded from the Virtual Box website along with the extension pack. The ISO files can be found on the Microsoft website. Once they are downloaded the install process can begin and the lab can start.


## Creating the Domain Controller VM

The first virtual machine that will be created is the DC or Domain Controller virtual machine which will be used for setting up the Active Directory server environment in the lab. The name can be either DC or Domain Controller so that it is easy to differentiate between each VM since we will have 2. The OS version from the dropdown menu that will be used is "Other Windows (64-bit)".

![001](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/a1639e09-1cce-42fe-ad3f-49a1fd2a6430)

The only other setting that will be changed is the memory, so this will be changed to 2048MB. Once it is created then the next step would be to change the following settings on the VM via the settings option:
- General - in the advanced tab change the "Shared Clipboard" and "Drag'n'Drop" options to Bidirectional
- System - change processor count from 1 to 2
- Network - add a second adapter on the "internal network" and it should say "intnet"

![002](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/f8d1161c-a2db-4821-858a-ac2c236b9b18)

Once this is completed we can go ahead and start the VM. A message should pop up on the screen showing that an ISO is needed to proceed. The Microsoft Server 2019 ISO will then be chosen from the computer's files via the dropdown icon.

![003](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/a0ebd29e-bd35-4c08-a9ea-11e38374187e)

Follow the on screen commands when they pop up and complete the windows setup. Use a custom install when you get the option and once the setup finishes there should be an administrator password that will have to be made and can be anything. Once this is completed the next step can begin.

## Installing the Guest Additions

This step is optional but is recommended for the lab since it will make it more convienient. Guest Additions is a set of software drivers and utilities that are designed to improve the integration between the host system (the physical machine where VirtualBox is installed) and the guest operating system running inside a virtual machine. To complete this we can do the following:

1. Navigate to the "Devices" tab at the top of the screen
2. Select "Insert Guest Additions CD"
3. Open the file explorer and go to "This PC"
4. Open the "Virtual Guest Additions" drive
5. Run "VBoxWindowsAddition-amd64"
6. Complete the setup wizard and reboot

## IP Addressing Setup

First we will open the Network and Internet settings, then navigate to the the Ethernet tab and open "change adapter options".

![003](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/ee5d25a4-be37-4a02-883a-8eba42ffdd2b)

They will have to be checked to see which one has an IP already and which one will need an IP. Right click the first one and check the status details to see if it has an IP address then do the same with the other one. The one with an IP will be renamed to "--INTERNET--" and the one without an IP will be renamed to "--INTERNAL--".

![004](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/7b69325c-a12a-49ce-b53b-528e78672e7d)

Next we will have to add an IP to the network connection that doesn't have an IP. Right click the "--INERNAL--" connection and go to properties then click on IPv4. Manually add the IP address "172.16.0.1" and the subnet mask will be "255.255.255.0". The DNS server will also have an IP address manually entered which will be "127.0.0.1". After that is done we will rename the PC to DC in the system settings and restart the VM.

![005](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/197462a7-8215-4bc0-9e76-6cd401f4acf0)

## Install Active Directory Domain Services

Next Domain services will be installed by running the Service Manager and adding a role to "Add roles and features". Complete the wizard by clicking next until you get to "server roles" and then check the box named "active directory". Continue through the wizard and click on install at the end.

## Creating a Domain

This next step is similar to the previous one which was completeing a setup wizard, but the setup wizard is slightly different. This setup wizard can be accessed by clicking on the flag icon at the top and there should also be a warning symbol under the flag. We will then click on the "Promote this server to a domain controller" option.

![006](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/627ab644-ad88-418d-a8ac-c1273ac9ade9)

The first set of options we will change is in the "Deployment Configuration" and it will be "Add a new forest". The root name can be anything but make sure it is relevant to the lab so it makes things easier. Once that is completed then you will be prompted to make a password so we can use any password to complete this. After that is done we can go ahead and advance through the wizard and install the Domain. The VM should restart and bring you to the login screen with the newly created Admin account. Go ahead an login then the next step can be done.

## Create a Dedicated Domain Admin Account

Navigate to the start button on the bottom right and open the "Windows Administrative Tools" folder then run the "Active Directory Users and Computers" application.

![007](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/76119bfe-c424-42ef-a166-f97e7d36ccec)

Once we get here then we can go ahead and right click the newly created domain and make a new organizational unit. Organizational Units (OUs) are like digital folders that help organize and manage different parts of a company's computer network. They make it easier for IT administrators to group and handle users, computers, and other things. OUs are useful for applying specific rules, managing security, and organizing the network in a more efficient way. We can name, uncheck the box below,and then click "OK". We will then do the same thing again but this time we will create a new user. We can use our own name or a random name for this step and then for the logon name we can use the naming convention "a-FirstInitialLastName". We will be prompted for a password, so we can use the same 

![008](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/e5cc3986-02bc-4c6c-9439-646d69b45a20)

The next thing to do is to make the new account a domain admin. In the properties of the account we can navigate to the tab "Member Of" and click "Add" to add the "Domain Admin" group to the user. Apply the changes and sign out of the account when you are done. Log in using the credentials that were made and then we can proceed to the next step.


## RAS/NAT Installation

RAS stands for Remote Access Service. It refers to a set of protocols and services that enable users to connect to a network remotely. RAS allows individuals to access resources on a network as if they were physically present at the network's location. It will be used to allow the windows client to have access to the internet via the domain controller while simultaneously remaining on the private virtual network. NAT stands for Network Address Translation. This is a techniqued used to facilitate communicationm between a private network and the internet. Like the theprevious steps we will have to go through the "Add roles and feature" setup wizard to install RAS/NAT. The only options that will be changed will be:
- In Server Roles check "Remote Access"
- In Role Services check routing and add feature (DirectAccess and VPN will be checked automatically so this can be left as is)

![009](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/1b275051-5848-453e-b24a-af2b186b83a0)

Once that is done then we can go ahead and navigate to the Tools tab at the top right and click on the "Routing and Remote Access" tab. Right click the DC option and configure the routing and remote access. A setup wizard will pop up and we can complete it by:
- Choosing the NAT option which should be the second one and clicking next
- Choose the first option which should show the 2 IP's that were created earlier.
- Clicking finish to proceed

## Setup DHCP Server on Domain Controller

We will go throught the setup wizard again for "Add roles and features". The only option that will be checked is "DHCP Server" option then we can go ahead and install it. Once it is done then we can navigate to the Tools tab and click on DHCP to bring up the window. Right click the "IPv4" server and choose the "New Scope..." option. A DHCP scope is a range of IP addresses that the DHCP server can dynamically assign to devices on the network. When you right-click on a DHCP server in the DHCP Management Console, the "New Scope" option allows you to set up a new range of IP addresses to be leased to DHCP clients. When the setup wizard appears we can proceed by naming the scope "172.16.0.100-200" and clicking next. For the IP address range we can put the scope information from the screenshot below on the start and end IP address ranges.

![011](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/90ac6391-b7fb-4ef5-a407-5f07581b13ed)

We can proceed until we get to Router(Default Gateway) and enter the IP address from the Domain Controller that has NAT installed which should be 172.16.0.1 in this case. We can then proceed until we get to the end and finish. Once that is doen we can go ahead and right click the DHCP server and click "Authorize" then right click it again and click refresh. You should see green checs if this is done correctly.

![012](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/3b8228f4-e88d-4249-9f41-df238d6cfacc)

## Create a List of Names

For this step we will create a list of names to be used for creating users in a later step. The software that we want to install is called Anaconda Navigator. This will be the application that will be used to run the code. Once it is installed open the Anaconda prompt and use the command shown below:
```python
pip install faker
```
Once that is complete then you can open anaconda and the code you will use to create a list with 1000 first and last names then save it to a file on your computer: 
```python
#Import faker library
from faker import Faker

fake = Faker()

# Generate a list of 1000 fake names
names_list = [fake.name() for _ in range(1000)]

# Save the names to a text file
with open('list.txt', 'w') as file:
    for name in names_list:
        file.write(name + '\n')

print("Names saved to list.txt")
```
This list should be saved in a ".txt" file and can be viewed via notepad. 
* If you do this step on the host Machine you can drag and drop the ".txt" file into the file explorer since we enabled the setting "bidrectional" in an earlier step and the list will be copied into the Virtual Machine.

## Create users with Powershell Script

This step we will use the list that we created to make usernames for all the names in the list. The code that we will use can be found by using this link https://github.com/joshmadakor1/AD_PS/blob/master/1_CREATE_USERS.ps1. This code will be run using Windows Powershell ISE as an administrator. Before we run the code we will have to use the following command:
```bash
Set-ExecutionPolicy Unrestricted
```
Once we do that we can copy and paste the code from the link into the new script. Make sure that the name of your list is implemented into the code or it won't work. Also make sure that you change to the corresponding directory that you saved the list in then run the code. The code will take a min or two since there is 1000 names on the list. Once it is done we can check the "Active Directory Users and Computers" window then use the dropdown arrow on the domain controller to check and see if it worked. If it worked it should look like the screenshot below.

![014](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/a1f82aa5-bd59-40e2-af83-38a50dd5b4a1)

## Create Windows 10 client VM

This is the last step that we will do in the lab before we finish. We will Start by creating a New VM and naming it "Client" and it will use Widows 10 (64bit) as the OS. Proceed through the creation wizard until it is finished and then navigate to the settings so we can change the following settings:
- General - in the advanced tab change the "Shared Clipboard" and "Drag'n'Drop" options to Bidirectional
- System - change processor count from 1 to 2
- Network - change the "attached to:" option to  "internal network" and it should say "intnet"

Once this is completed we can go ahead and start the VM again. A message should pop up on the screen showing that an ISO is needed to proceed. The Windows 10 ISO will then be chosen from the computer's files via the dropdown icon. The Windows setup wizard should appear and we will proceed until we get the option to choose a version of Windows and we will choose Windows 10 pro. Click next then choose "custom installation" and click next again then wait until it finishes. Another screen should pop up and take you through the Windows setup.

![015](https://github.com/isaac-daniel00/HomeLab-ActiveDirectory-Oracle/assets/155948481/f1c60389-88f1-4483-b01d-8ad0abd72dd3)

Proceed though the setup until you get to the Network part and choose the option "I don't have internet" which should be in the lower right of the screen. Then In the same corner click on "Continue with limited setup". We will be prompted to give a name so we can just enter "user" and we won't use a password so just click next. Proceed until you are at the home screen. Once that is done we can go ahead and sign out then sign in as one of the created users that was created earlier and then the lab will be done.

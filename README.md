# Active-Directory-Homelab-In-Promox

## Project:
The goal of this homelab was to create an Active Directory enviroment using virtual Windows 10 and Windows Server machines. I will also create a Splunk server on a virtual Ubuntu Server machine as well as a virtual Kali Linux box to simulate attacks on the Windows 10 machine. With the Splunk Universal Forwarder and Sysmon installed on the Windows machines, these attacks will generate telemetry and create events in Splunk to be analyzed.  

MyDFIR's guide: https://www.youtube.com/watch?v=5OessbOgyEo

#


## Setup:
With a Lenovo ThinkCentre 710q (Intel i7-7700t, 32gb DDR3 ram, 1tb NVMe M.2 SSD), the first step was to install Proxmox with SSH enabled for remote configuration. After successful installation, I went ahead and logged into my home router and configured the machine to have a static IP address just to be safe. This was to be sure I would not have any issues logging in to machine if the DCHP lease ended. After setting my static IP, it was time to login to the Proxmox web interace via the machine's IP address through the default port of 8006.
#

Proxmox Web Interface

![Proxmox Home](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/ec1dd9e7-54bf-4ab5-955e-f684cb1b7a85)

#

Now it was time to create my virtual machines. After downloading Windows 10, Windows Server, Kali Linux, and Ubuntu Server iso files, I created my VM's. After deploying my VM's, they were configured and updated. Below I will show the configuration for my Windows 10 machine.

![Windows 10 install 1](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/0b370fe1-820a-449f-bb26-a0bbcecb3970)
![Windows 10 install 2](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/d2c06d6c-2179-4f77-8406-7aadca93f1c9)
![Windows 10 install 3](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/483c6868-5801-4984-ba61-80d09da6d2ab)
![Windows 10 install 4](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/a8496a62-e390-4e68-a7fb-4a70e343de76)
![Windows 10 install 5](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/bdbbdfe5-1ad4-46bb-9272-30b139e08ecf)
![Windows 10 install 6](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/04e6eb31-d096-4cda-999d-7e4d9ee5cd84)
![Windows 10 install 7](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/b357774c-673b-4cc1-8c0e-020c79348aee)

#

The next step was to create a new virtual Linux bridge. I did this to make sure that all of my VM's remained in their own virtual Lan and could not speak to other hosts on my home network. I left the default name for the bridge (vrmbr1) and set the IP address to 192.168.10.1/24. I then configured the network adapters in my machines to utilize my new Linux bridge (vmbr1). After this step, I needed a way to be sure the machines could still access the internet. To do this, I SSH'd back into Promox machine and configured the /etc/network/interfaces file to allow the new Limux Bridge to forward internet traffic to and from the Lenovo network adapter (vmbr0). 

![Virtual Linux Bridge](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/9d12d896-990f-499b-9f46-00384f79a2c8)
![vmbr1 Config](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/a523ed65-9dc8-4d6e-9e59-13b31f473b46)
![Set vmbr1 to machines](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/81877d7f-e2bd-44ef-914f-b8671e8c6849)

Quick note, I also went into each virtual machine and set static IP addresses and a gateway of 192.168.10.1. In the screenshot below, the DNS server of the Windows 10 machine is set to the IP address of the Windows Server. For now, leave the DNS set to obtain automatically (more on that later).

![Static Ip in W10](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/ab6ded7e-ec89-49a2-979f-06056963981f)

#

Now that all of my virtual machines were created, installed, and configured on the VLAN, it was time to install Splunk onto my Ubuntu Server machine. I went ahead to the Splunk website, set up an account, and installed Splunk enterprise on the Ubuntu Server machine using the wget command. I had a little trouble with setting the this but was able to get it up and running after fidining a guide here: https://www.youtube.com/watch?v=oHUXY7B83Ww&t=841s After setting up my admin account, I logged into the web interface on my Windows Server machine via the Ubuntu Server's IP address and the default port of 8000. I was then ready to start installing Splunk Universal Forwarder and Sysmon onto the Windows 10 and Windows Server Machines.

![Splunk Web Interface](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/7852dbcf-3cb5-4e8a-a2b3-f98c98a3e79f)

#

On both of the Windows machine, I logged back into the Splunk website and downloaded the Splunk Universal Forwarder. When installing Universal Forwarder, be sure to change Receiving Indexer to the IP address of the Splunk Server (Ubuntu Server IP). The default port can remain as 9997. 

![universal forwarder](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/259a5c26-983f-4232-b582-fc07c3a90b1d)
![universal forwarder receiving idexer](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/2c53b5e5-92ce-4a67-9c1d-75871fdcff8c)


#

To install Sysmon on both the Windows machines, I first downloaded Syson by Sysinternals from Microsoft's website. Next, I went to olafhartong's Github page and selected the sysmon-modular repository here: https://github.com/olafhartong/sysmon-modular. After scrolling down, I selected the sysmonconfig.xml file, clicked raw, right clicked the page, and clicked save as. The file was saved as sysmonconfig to the downloads directory. Next, I extrated the sysmon file from Sysinternals in the Downloads directory, opened Powershell as administrator, cd's to the Sysmon file and ran the executable with olafhartong's sysmonconfig.xml file as shown below. Now both Sysmon and Universal Forwarder were installed on my Windows Machines.

![sysmon sysinternals](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/5b2e7d35-79d4-434c-9dfc-f93c78ea49cb)
![sysmon file](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/26786b97-a9ae-478a-b1ea-72baf094a530)
![sysmon powershell exe](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/80a21b45-a8ea-46ad-ad17-c8cc204e58be)

# 

The next step was to intruct the Splunk Universal Forwarder on what to send over to the Splunk Server. This was achieved by editing the inputs.conf file of the Universal Forwarder on my Windows machines. The file was located at LocalDisk(C:)>ProgramFiles>SplunkUniversalForwarder>etc>system>default>inputs.conf. However, this was not the actual file I wanted to edit. I wanted to leave that default file there to ensure that if something went wrong, I could revert back to that default file. Instead, I opened up notepad as administrator and copied the config file provided by MyDFIR in his guide found here: https://github.com/MyDFIR/Active-Directory-Project. This created an index in Splunk named "endpoint" where all of the events from my Windows Machines could be seen via Splunk. The file was saved and store in LocalDisk(C:)>ProgramFiles>SplunkUniversalForwarder>etc>system>local.

![inputs conf file](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/26d9c45d-4887-4ff7-82a6-3665bc93b830)
![system local inputs conf](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/681b4c0a-257a-47a8-9cda-6f7f953b60a9)

#

Because I updated the inputs.conf file, I needed to restart the Splunk Universal Forwarder. To do this, I opened services as administrator and scrolled down to SplunkForwarder. After finding the SplunkForwarder service, I noticed the Log On was set to This account. By double clikcing SplunkForwarder, I tabbed over to Log On and checked off Local System account. This ensured that Splunk would be able to collect logs and not run into any problems due to permissions. Afterwards, I restarted the Splunk Forwarder service.

![Splunk log on](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/47ce6297-c9f5-4356-9833-eb13594e43cc)
![restart splunk forwarder](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/4c4dd469-45b7-439f-af39-358dc66541fb)

#

Next, it was time to log back into Splunk, create a new receiving port (9997), and create an index called "endpoint" to see the events from my Windows Machines. I logged back into the Splunk web interface on my Windows 10 machine, clicked settings at the top, and clicked forwarding and receiving > configure receiving > New Receiving Port, and added port 9997. Next I went back to the settings menu and clicked indexes, new index, and added the index name "endpoint". Going back to the Splunk dashboard, I could now search index=endpoint to see data being sent from my Windows machines.

![new receiving port](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/e04103a7-48c0-4f4a-9e1c-3ec8ce8e77d8)
![new index](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/9ec41d73-7ba9-44df-ad28-6e76e54029d3)
![endpoint search](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/822f7e2c-6810-4010-af68-966da4af1592)

#

My next step was to set up active directory. To do this, I opened up my Windows Server machine and opened the Server Manager. Here, I clicked Manage at the top and selected Add Roles and Features. I clicked next through the wizard until I reached Server Roles. I added the Active Directory Domain Services and clicked Add Features. Next I continued through the wizard until it was time to install. I clicked install and noticed a flag at the top of the Server Manager. After clicking the flag, I was prompted to Promote the server to a domain controller. After selecting that, I set the deployment configuration to add new forest and named my domain "homelab.local". On the next page I created a password and continued through the install wizard with the default settings left as is and finally the server restarted to complete the intallation.

![AD step 1](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/65271e88-1893-4e4b-8cbf-c5600ec0116e)
![AD step 2](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/eb433d8d-292d-42ae-ba17-f0992d088609)
![AD step 3](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/22ef4234-48a2-46c8-b5aa-75e20e9704a6)
![AD step 4](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/e324c8eb-c27a-4f3e-afde-3425579196ad)
![AD step 5](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/68ae0e08-80fc-4732-97d6-7e6e12d19f2b)

#

After logging backing into the server, it was time to add some users. To do this, I went to the top of the Windows Server Manager, selected Tools, then selected Active Directory Users and Computers. The window displayed there was where I was going to create two ogranizational units, IT and HR. Within those organizational units, I would create users, Bob Smith in IT and Jane Smith in HR. To do this, I right clicked "homelab.local", > New > Orgranizational Unit. I added IT and repeated the process for HR. I then right clicked IT > New > User and added Bob Smith with bsmith as the username. Clicking next, I added a password (unchecked "User must change password" to keep things simple)", and just like that, Bob Smith was now a member of the IT department in my lab. Finally, I continued the process for Jane Smtih in HR.

![users step 1](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/640a83e6-5ce1-48af-a5a8-b565780f8da0)
![users step 2](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/227d57e5-d438-47cd-b01f-81c612161108)
![users step 3](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/4ea1ecac-2724-448a-8031-3141d4ea7973)

#

Now that I have successfully created my active directory environment with groups IT and HR, as well as users Bob Smith and Jane Smith, the final step was to insatll Atomic Red Team onto my Windows 10 machine and simulate a brute force attack from my Kali Linux machine to generate telemtry and view the events in Splunk. The firt step was to download and install the Crowbar tool onto my Kali Linux machine using the "sudo apt install crowbar" command. 

![install crowbar](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/975bc9dc-7482-4401-a987-30794cc4c1dc)

#

Next I cd'd into the words lists file within kali linux located at /usr/share/wordlists and unzip the rockyou.txt.gz file using the "sudo gunzip rockyou.txt.gz" command. Now, this word list is massive and for the purpose of this project, I wanted to simulate a successful brute force attack without iterating through each word in the rockyou.txt file. So to create a smaller wordlists file I used the "head -n 20 rockyou.txt > passwords.txt" command to create a new word list file called "passwords.txt" containing only the first 20 passwords of the rockyou.txt file. THe final step was to edit the passwords.txt file using nano and add the passwords for users Bob Smith and Jane Smith to enrue the brute force attack on those users was successful.

![gunzip rockyou](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/b7b20b78-2510-4a96-bfc5-669c274880ff)
![create passwordstxt](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/cc44eab5-39ab-4784-a1c5-5fe7055cbab3)
![add password](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/d006bf55-1db6-4f3b-be8a-98b7625d2fbf)

#

The next step was to enable RDP on the Windows 10 machine. To do this, I went into My PC > Properties > Advanced system settings > Remote tab > and checked Allow Remote Assistance connections to this computer. Then I clicked Select Users and added users bsmith and jsmith, my two active directory users. 

![enable rdp](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/2244b7e1-3ef7-47a8-9a60-652331450461)
![rdp users](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/9f2fa1b1-0a6c-469b-b2ee-5bd1313041fc)

#

Now, it was finally time for the fun part. I logged back into the Kali Linux machine to create a brute force attack against Bob Smith. Using the crowbar tool, I ran "crowbar -b rdp -u bsmith -C passwords.txt -s 192.168.10.3/32". To break this down, I executed the crowbar tool using "crowbar", specified the service I would be attacking using the -b flag, chose user bsmith using the -u flag, the wordlist I wanted to use with the -C flag, and finally the IP address of the target machine using the -s flag. Note that I used the /32 cidr to specify that that was the only IP address within the network that I would be attacking.

![crowbar attack](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/b1f75a99-c901-4e9e-928f-87d048d0939c)

#


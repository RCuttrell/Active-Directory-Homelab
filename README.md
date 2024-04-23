# Active-Directory-Homelab-In-Promox

## Project:
The goal of this homelab was to create an Active Directory enviroment using virtual Windows 10 and Windows Server machines. I will also create a Splunk server on a virtual Ubuntu Server machine as well as a virtual Kali Linux box to simulate attacks on the Windows 10 machine. With the Splunk Universal Forwarder and Sysmon installed on the Windows machines, these attacks will generate telemetry and create events in Splunk to analyze.  

MyDFIR's guide: https://www.youtube.com/watch?v=5OessbOgyEo

#


## Setup:
With a Lenovo ThinkCentre 710q (Intel i7-7700t, 32gb DDR3 ram, 1tb NVMe M.2 SSD), the first step was to install Proxmox with SSH enabled for remote configuration. After successful installation and logging into the machine via SSH, I went ahead and configured the /etc/network/interfaces file for static IP. This was to be sure I would not have any issues logging in to machine if the DCHP lease ended. After setting my static IP, it was time to login to the Proxmox web interace via the machine's IP address through the default port of 8006.
#

Proxmox Config and Web Interface
![Static IP](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/1c2862c4-74e6-431a-988d-20778459ac4c)
![Proxmox Home](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/ec1dd9e7-54bf-4ab5-955e-f684cb1b7a85)

#
The next step was to create a new virtual Linux bridge. I did this to make sure that all of my VM's remained in their own virtual lan and could not speak to other hosts on my home network. I left the default name for the bridge (vrmbr1) and set the IP address to 192.168.10.1/24. I then set configured the network adapters in my machines to utilize my new Linux bridge (vmbr1). After this step, I needed a way to be sure the machines could still access the internet. To do this, I SSH'd back into Promox machine and configured the new Limux Bridge to forward internet traffic to and from the Lenovo network adaptor (vmbr0). 
![Virtual Linux Bridge](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/9d12d896-990f-499b-9f46-00384f79a2c8)
![vmbr1 Config](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/a523ed65-9dc8-4d6e-9e59-13b31f473b46)
![Set vmbr1 to machines](https://github.com/RCuttrell/Active-Directory-Homelab/assets/111534355/81877d7f-e2bd-44ef-914f-b8671e8c6849)

#

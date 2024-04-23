# Active-Directory-Homelab-In-Promox

## Project:
The goal of this homelab was to create an Active Directory enviroment using virtual Windows 10 and Windows Server machines. I will also create a Splunk server on a virtual Ubuntu Server machine as well as a virtual Kali Linux box to simulate attacks on the Windows 10 machine. With the Splunk Universal Forwarder and Sysmon installed on the Windows machines, these attacks will generate telemetry and create events in Splunk to analyze.  

MyDFIR's guide: https://www.youtube.com/watch?v=5OessbOgyEo

#


## Setup:
With a Lenovo ThinkCentre 710q (Intel i7-7700t, 32gb DDR3 ram, 1tb NVMe M.2 SSD), the first step was to install Proxmox with SSH enabled for remote configuration. After successful installation and logging into the machine via SSH, I went ahead and configured the /etc/network/interfaces file for static IP. This was to be sure I would not have any issues logging in to machine if the DCHP lease ended. After setting my static IP, it was time to login to the Proxmox web interace via the machine's IP address through the default port of 8006.
#

Proxmox Web Interface

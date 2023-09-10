# Ubuntu VPS Setup Guide

Welcome to the comprehensive Ubuntu VPS Setup Guide! This guide will walk you through the process of setting up and configuring an Ubuntu VPS for various applications, including game servers and web hosting. Whether you're new to VPS administration or seeking to optimize your server's performance and security, this guide has you covered.

## Installing on FiveM

### Prerequisites

Before you begin, ensure you have the following prerequisites:

- **An Linux-based VPS** (In this case, I'll demonstrate the setup using VirtualBox on your local machine).
- **Medium to advanced knowledge of the Linux command line** (Deploying a server from the terminal can be more challenging than on Windows).

### **Step 1:** *Setting Up Your VPS (SSH Handling + Localhost with VBox)*

If you are using VirtualBox for local development, there is a additional step to consider.

1-. **Port Forwarding**: *To allow VirtualBox to connect via SSH, it's recommended to set up port forwarding. This step is not strongly required but is highly recommended.*

- Right-click on your Virtual Machine (VM) > Settings > Network > Adapter 1 (usually) > Port Forwarding.
![Port Forwarding](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d7229cd7-591c-4634-8857-f323aed2a4fa)

- Add a port forwarding rule named SSH/TCP/../2222/../22. It should look something like this.
![Port Forwarding Rule](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d57c42ac-1603-4e69-bf1e-97bb0f735b01)

1.2-. **ON/OFF Handling**: *Since we are going to use SSH connections is very stupid to have the own VM window, so we can "disable"/unshow it.*
   
- Right-Click > Start > Headless Start; I strongly recommend do the same for Stop > ACPI Shutdown.
![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/618baada-35f9-415c-9d71-9cd5cfc9569a)

1.3-. **Enabling and checking SSH**

1.4-. **SSH Connection**: *Before we are able to login through SSH, we will need a SSH client of course. For Windows I strongly recommend using PuTTY. Windows RDP is completely unsafe and trash. After installing the prefered SSH client we need to put our own IP and port 2222.*

- Login.
  ![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/b048e1d9-1d41-43cf-ab50-d7854533b679)
   
- Then we are inside,
  ![image](https://github..om/ferrnnaando/fivem-gameserver-setup/assets/77246868/54109be2-ae7c-44d2-9dce-49953ca42044)

2-. **Firewall && Services Handling**: *The most important step is the firewall without a doubt. Without a good firewall we can get hacked, issues opening the server and a bounch of stuff more.*

- Since we

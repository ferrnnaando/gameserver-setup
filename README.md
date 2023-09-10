# Ubuntu VPS Setup Guide

Welcome to the comprehensive Ubuntu VPS Setup Guide! This guide will walk you through the process of setting up and configuring an Ubuntu VPS for various applications, including game servers and web hosting. Whether you're new to VPS administration or seeking to optimize your server's performance and security, this guide has you covered.

## Installing on FiveM

### Prerequisites

Before you begin, ensure you have the following prerequisites:

- **A Linux-based VPS**: In this guide, we'll demonstrate the setup using VirtualBox on your local machine.
- **Medium to advanced knowledge of the Linux command line**: Deploying a server from the terminal can be more challenging than on Windows.

### Step 1: Setting Up Your VPS (SSH Handling + Localhost with VBox)

If you are using VirtualBox for local development, there is an additional step to consider.

**1. Port Forwarding**: To allow VirtualBox to connect via SSH, it's recommended to set up port forwarding. This step is not strictly required but highly recommended.

   - Right-click on your Virtual Machine (VM) > Settings > Network > Adapter 1 (usually) > Port Forwarding.
   ![Port Forwarding](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d7229cd7-591c-4634-8857-f323aed2a4fa)

   - Add a port forwarding rule named SSH/TCP/../2222/../22. It should look something like this.
   ![Port Forwarding Rule](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d57c42ac-1603-4e69-bf1e-97bb0f735b01)

**1.2. ON/OFF Handling**: Since we are going to use SSH connections, it's a good idea to disable or hide the VM window.

   - Right-Click > Start > Headless Start; I strongly recommend doing the same for Stop > ACPI Shutdown.
   ![Disable Default Look](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/618baada-35f9-415c-9d71-9cd5cfc9569a)

**1.3. SSH Connection**: Before we can log in through SSH, we will need an SSH client. For Windows, we strongly recommend using PuTTY. After installing the preferred SSH client, enter your IP and port 2222.

- Login.
- ![Login in SSH](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/b048e1d9-1d41-43cf-ab50-d7854533b679)

- Then we are inside.
- ![Inside VPS 1.4](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/54109be2-ae7c-44d2-9dce-49953ca42044)

**2. Firewall and Services Handling**: The most critical step is the firewall. Without a good firewall, we risk security issues and server instability.

   - To handle the firewall, we will use UFW (Uncomplicated Firewall) and IPTables.

   ### UFW Configuration

   ```bash
   # Install UFW and enable it.
   sudo apt-get install ufw
   sudo ufw enable

   # Allow necessary ports.
   sudo ufw status
   sudo ufw allow OpenSSH    # Ensure allowing SSH connections
   sudo ufw allow 22/tcp     # Ensure allowing SSH connections
   sudo ufw allow 80/tcp     # Allow outgoing HTTP traffic
   sudo ufw allow 443/tcp    # Allow outgoing HTTPS traffic
   sudo ufw allow 3016/tcp   # Allow outgoing MYSQL traffic
   sudo ufw allow 30110/tcp  # FiveM Protocol (specified in server.cfg)
   sudo ufw allow 30110/udp  # FiveM Server (modification of GTA V) uses TCP ports 30120 and 30110.
   sudo ufw allow 30120/tcp  # FiveM Protocol (specified in server.cfg)
   sudo ufw allow 30120/udp  # FiveM Server (modification of GTA V) uses TCP ports 30120 and 30110.

   # Set default policies.
   sudo ufw default deny incoming
   sudo ufw default allow outgoing

   # Display current status.
   sudo ufw status

   # Reload UFW and restart SSH.
   sudo ufw reload
   sudo systemctl restart ssh

   # Reboot the system to ensure changes was made.
   sudo reboot
```

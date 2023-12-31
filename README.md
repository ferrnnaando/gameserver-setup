## Ubuntu VPS Setup Guide
Welcome to the comprehensive Ubuntu VPS Setup Guide! This guide will walk you through the process of setting up and configuring an Ubuntu VPS for various applications, including game servers and web hosting. Whether you're new to VPS administration or seeking to optimize your server's performance and security, this guide has you covered.

- I don't provide a Windows guide because configuring port forwarding on Windows is relatively straightforward. Linux is more commonly used for server management, hence the focus on Linux in this guide.

Before you begin, ensure you have the following prerequisites:
- **A Linux-based VPS**: *In this guide, I'll demonstrate the setup using VirtualBox on your local machine.*
- **Medium to advanced knowledge of the Linux command line**: *Deploying a server from the terminal can be more challenging than on Windows.*
- **Basic to medium knowledge of Git commands**.

## Common configuration
**1-. [Enabling SSH:](https://ubuntu.com/server/docs/service-openssh)** *To use SSH in order you have to do a few things before; This step is not strictly required but highly recommended.*

```bash
# Enable SSH.
sudo apt install openssh-client
sudo apt install openssh-server
sudo systemctl enable ssh

# Enable MYSQL.
sudo apt install mysql-server
sudo systemctl enable ssh
sudo mysql_secure_installation # This step is not strongly required but highly recommended.

#For my case, that I losed the password and MYSQL doesnt gives me the power of create a account I will recover it.
sudo systemctl stop mysql

sudo mysqld_safe --skip-grant-tables &
fernando@gamehost:~$ 2023-09-11T11:53:13.520630Z mysqld_safe Logging to '/var/log/mysql/error.log'.
2023-09-11T11:53:13.524782Z mysqld_safe Directory '/var/run/mysqld' for UNIX socket file don't exists.

# Lets fix that creating the required directories.
sudo mkdir -p /var/log/mysql
sudo mkdir -p /var/run/mysqld
sudo chown -R mysql:mysql /var/log/mysql
sudo chown -R mysql:mysql /var/run/mysqld

# Then we are inside.
fernando@gamehost:~$ 2023-09-11T11:54:12.615982Z mysqld_safe Logging to '/var/lo           g/mysql/error.log'.
2023-09-11T11:54:12.657518Z mysqld_safe Starting mysqld daemon with databases fr           om /var/lib/mysql

fernando@gamehost:~$ mysql -u root
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 8.0.34-0ubuntu0.23.04.1 (Ubuntu)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

>

# Personally i don't really need to recover the root password, so it can still being losed, whe know how to recover it. Instead using root I will create a new user.
mysql> CREATE USER 'fivem/ragemp'@'localhost' IDENTIFIED BY 'password';
mysql> GRANT PRIVILEGE ON database.table TO 'fivem/ragemp'@'host';
mysql> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES
mysql> exit

# Now we have a new account to use for our gameserver.
mysql -u fivem/ragemp -p
```

**1.2-. Port Forwarding**: *To allow VirtualBox to connect via SSH, it's recommended to set up port forwarding.*
- Right-click on your Virtual Machine (VM) > Settings > Network > Adapter 1 (usually) > Port Forwarding.
> ![Port Forwarding](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d7229cd7-591c-4634-8857-f323aed2a4fa)

- Copy these ports rules, it should appear to something like that. Discourage about the protocol defined names.
> ![Port Forwading Rules](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/3da77abd-c506-4943-9515-13258faa55bf)


**1.3-. ON/OFF Handling**: *Since we are going to use SSH connections, it's a good idea to disable or hide the VM window.*
- Right-Click > Start > Headless Start; I strongly recommend doing the same for Stop > ACPI Shutdown.
> ![Disable Default Look](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/618baada-35f9-415c-9d71-9cd5cfc9569a)

**1.4-. SSH Connection**: *Before we can log in through SSH, we will need an SSH client. For Windows, we strongly recommend using PuTTY. After installing the preferred SSH client, enter your IP and port 2222.*

- Login.
> ![Login in SSH](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/b048e1d9-1d41-43cf-ab50-d7854533b679)
> ![Inside VPS 1.4](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/54109be2-ae7c-44d2-9dce-49953ca42044)

## Installing on FiveM
### Step 2: Setting up Firewall
**2-. Firewall and Services Handling**: *Another of the most critical step is the firewall. Without a good firewall, we risk security issues and server instability.*
- To handle the firewall, we will use UFW (Uncomplicated Firewall) and IPTables.

   ### UFW
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
   sudo ufw allow 40120/tcp  # FiveM server txAdmin Protocol
   sudo ufw allow 40120/udp  # FiveM server txAdmin Protocol
   sudo ufw allow 40110/tcp  # FiveM server txAdmin Protocol
   sudo ufw allow 40110/udp  # FiveM server txAdmin Protocol 

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

   ### IPTables
   ```bash
   # Similar to UFW, it's a good practice to deny all incoming traffic by default and only allow the specific services and ports that you need. You can do this with the following rules:
   sudo iptables -P INPUT DROP
   sudo iptables -P FORWARD DROP
   sudo iptables -P OUTPUT ACCEPT
   
   # Block the amount of login attempts. This can help to mitigate brute-force password attacks and other else. Why your VPS should have more than 3 persons on it? This is very confusing, bad-practice. Just remember to have smart workers that doesnt have to relogin each minute.
   sudo iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m limit --limit 3/min --limit-burst 3 -j ACCEPT
   
   # You can log denied packets to monitore what's happening.
   sudo iptables -A INPUT -j LOG --log-prefix "IPTABLES-DROP: "
   
   # You can block specifics IP's
   sudo iptables -A INPUT -s 192.168.1.100 -j DROP
   
   # And a lot more, but this is more than enough, this can consideer a good firewall configuration
   ```

### Adittional considerations
**Consideer using Fail2ban:** *Consider using Fail2ban to protect against brute-force login attempts. It can automatically block IP addresses that repeatedly fail login attempts.*

### Step 3: Setting up server core
**3-. Keymaster** *Once we have a good securitty we can continue.*
- Go to [FiveM keymaster](https://keymaster.fivem.net/) and create one key. To fill in out you will need to know your public IP that can be found on webs like [whatismyippadr](https://whatismyipaddress.com/es/mi-ip)    and the host type, for this case Home Host &&     
Vbox Ubuntu Server.*
> ![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/e423ce7e-844f-4617-a493-e6d8209beebe)
> ![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/53734d98-8bca-4863-855a-6b57571d4074)

**3.2-. Installation**:
- For this step, we can install FileZilla and download the core from the FXServer main branch in our OS and transfer it to our VBox via Filezilla or directly doing it with git clone. I like to use the resources that      I have instead creating dumbs and slow roads 
of traffic, then lets use git.

- Install Git `sudo apt install`. You can apply your best logic, for my case my logic will be that the server will be allocated on my user and nobody more will have permission to access.
Once allocated on my user folder `~/fernando/` execute `mkdir -p FXServer/server` and install the recommended branch build from the [Linux server build listing](https://runtime.fivem.net/artifacts/fivem/build_proot_linux/master/). After this, if you didnt found 
nothing like was my case. You can use this command `wget https://runtime.fivem.net/artifacts/fivem/build_proot_linux/master/6624-81fd97f8ee7def9f89fb2aafa99a13aadc045d10/fx.tar.xz && tar xf fx.tar.xz` in the parent 
dir `FXServer`.

-  Now your parent dir should appear to something like this.
> ![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/b1849b51-ad71-4d60-ba57-3b0809932dfe)

- Clone the official [cfx-server-data](https://github.com/citizenfx/cfx-server-data) with `git clone ` to your server dir (wich I decided to call it server-data to mantain a code understable).
Then go to `server-data/` and create a `server.cfg`. You can do it with `touch server.cfg` and use this [template from pastebin](https://pastebin.com/VKrAfXmt). Remember to change the last `sv_licenseKey changeme` 
to a `sv_licenseKey ""` with your key generated in the keymaster.

**3.3-. Running**:
- Once you execute in the `server-data/` dir the `bash ../run.sh +exec server.cfg` command. It can appear really ugly, and is it, but only who have knowledge in FiveM programming knows that powerful things can be made.
> ![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/1b5de63f-9aed-459e-9616-4b39b6e72430)
> ![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/41e3a058-2f32-40da-a977-27435481388c)
> ![image](https://github.com/ferrnnaando/gameserver-setup/assets/77246868/f5ac515b-7dac-4827-b851-965a1b5cfc22)
> ![image](https://github.com/ferrnnaando/gameserver-setup/assets/77246868/382709d1-2dab-4d30-8ed9-72ba6bf0cb12)


### Post-installation Misc
- **You have two options to create your FiveM server**: You can run the `run.sh` file that includes a txAdmin web-support dashboard and a bunch of server templates or making it vanilla as the explained way, if you 
decide to do it from txAdmin you don't need to load    artifacts since txAdmin handles all this process. I strongly recommend to use txAdmin, as is a official FiveM server client manager.

- **You can setup MYSQL**: Its very easy to make your FiveM server uses a MYSQL schema.
```bash
# Add this before the endpoint connect definition.
set es_enableCustomData 1
set mysql_connection_string “server=127.0.0.01;database=essentialmode;userid=root;”
```

- **Openning to the public**: In case that you wanna join someones to your server you can use *ngrok*. I don't like router port forwarding because this is creating a vulnerability on the router, so I just would do port forwarding in external services. You can install ngrok and do this: Execute your server > Expose tcp 30120 port. Something like this:
> ![image](https://github.com/ferrnnaando/gameserver-setup/assets/77246868/07324d73-9129-411e-b851-d032a83d444d)
> ![image](https://github.com/ferrnnaando/gameserver-setup/assets/77246868/8ccc9919-46ec-4742-8838-7d610a42f490)


### - **Facing errors**:
> - *[ citizen-server-impl] Server list query returned an error: System.Threading.Tasks.TaskCanceledException: A task was canceled. <- System.TimeoutException: A task was canceled. <- 
> System.Threading.Tasks.TaskCanceledException: The request was canceled due to the configured HttpClient.Timeout of 30 seconds elapsing*; Uncomment `#sv_master1 ""` from `server.cfg`.

## Installing on RageMp
### Step 2: Setting up Firewall

# Ubuntu VPS Setup Guide

This comprehensive guide will walk you through the process of setting up and configuring an Ubuntu VPS for various applications, including game servers and web hosting. Whether you're new to VPS administration or looking to optimize your server's performance and security, this guide has got you covered.

## Installing on FiveM

### Prerequisites

Before you begin, make sure you have the following prerequisites:
- An Ubuntu-based VPS with SSH access (For this case I will be hosting the server on localhost with VBox)
- Medium/Advanced knowledge of the Linux command line (Deploy a server from the terminal is not easier than do it on Windows)

### Step 1: Setting Up Your VPS

# Running Localhost

If you are running VBox you will have to make a few extra steps.

1. We will need to port forward to let VBox able to connect via SSH. This step is not strongly required but is highly recommended.

Right-click on your VM > Settings > Network > Adapter 1 (Generally is this) > Port Forwardaing
![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d7229cd7-591c-4634-8857-f323aed2a4fa)

Then add a rule called SSH/TCP/../2222/../22. It should appear somethingl like this.
![image](https://github.com/ferrnnaando/fivem-gameserver-setup/assets/77246868/d57c42ac-1603-4e69-bf1e-97bb0f735b01)

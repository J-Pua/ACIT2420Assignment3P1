# Setting Up a Debian 12 Server on DigitalOcean

## Creating SSH Key Pair and Setting Up DigitalOcean Account

1. **Generate SSH Key Pair:**
   - Ensure the existence of the `.ssh` directory or create one if absent:
     ```bash
     mkdir -p ~/.ssh
     ```
   - Generate an SSH key pair using the Ed25519 algorithm:
     ```bash
     ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "your-email-address"
     ```

2. **Add SSH Key to DigitalOcean Account:**
   - Log in to your DigitalOcean account and go to **"Settings"** > **"Security"**.
   - Click **"Add SSH Key"** and copy the public SSH key:
     - For Windows:
       ```powershell
       Get-Content C:\Users\user-name\.ssh\do-key.pub | Set-Clipboard
       ```
     - For macOS:
       ```bash
       pbcopy < ~/.ssh/do-key.pub
       ```
   - Paste the key into the designated field, name it, and click **"Add SSH Key"**.

## Creating and Configuring a Droplet

1. **Create a Droplet:**
   - Go to **"Droplets"** > **"Create Droplet"** in your DigitalOcean account.
   - Select your preferred server (e.g., Debian 12) and click **"Create Droplet"**.

2. **Login Using Your SSH Key:**
   - Access your server via SSH using the private key:
     ```bash
     ssh -i ~/.ssh/do-key root@your-droplet-ip
     ```

## Creating a New Regular User and Granting Permissions

1. **Create a New User:**
   - Add a new user with bash as the default shell and a home directory:
     ```bash
     sudo useradd -m -s /bin/bash <user-name>
     ```

2. **Set the Password for the New User:**
   - Set a password for the new user:
     ```bash
     sudo passwd <user-name>
     ```

3. **Grant Administrative Privileges:**
   - Add the user to the sudo group:
     ```bash
     sudo usermod -aG sudo <user-name>
     ```

4. **Enable SSH Access for the New User:**
   - Copy the `.ssh` directory from the root user to the new user's home directory:
     ```bash
     sudo cp -r /root/.ssh /home/<user-name>/
     ```

   - Adjust ownership of the directory and files:
     ```bash
     sudo chown -R <user-name>:<user-name> /home/<user-name>/.ssh/
     ```

   - Test SSH access for the new user:
     ```bash
     ssh -i ~/.ssh/do-key <user-name>@your-droplet-ip
     ```

## Securing SSH and Installing Nginx

1. **Prevent Root SSH Access:**
   - Edit the SSH configuration:
     ```bash
     sudo vim /etc/ssh/sshd_config
     ```
   - Set `PermitRootLogin` to `no`, then save and exit.

   - Restart the SSH service:
     ```bash
     sudo systemctl restart ssh.service
     ```

   - Test root SSH access (should result in permission denied):
     ```bash
     ssh -i ~/.ssh/do-key root@your-droplet-ip
     ```

2. **Install Nginx:**
   - Update packages:
     ```bash
     sudo apt update
     ```
   - Install Nginx:
     ```bash
     sudo apt install nginx
     ```
   - Start and check Nginx status:
     ```bash
     sudo systemctl start nginx
     sudo systemctl status nginx
     ```

## Configuring Nginx to Serve a Sample Website

1. **Create a Sample Website:**
   - Make a directory for the website:
     ```bash
     sudo mkdir -p /var/www/my-site
     ```
   - Create an `index.html` file in the directory with sample content.

2. **Configure Nginx Server Block:**
   - Create a configuration file for the server block:
     ```bash
     sudo vim /etc/nginx/sites-available/my-site
     ```

   - Define the server block configuration.

3. **Enable Configuration and Restart Nginx:**
   - Create a symbolic link:
     ```bash
     sudo ln -s /etc/nginx/sites-available/my-site /etc/nginx/sites-enabled/
     ```

   - Remove the default symbolic link:
     ```bash
     sudo unlink /etc/nginx/sites-enabled/default
     ```

   - Test Nginx configuration:
     ```bash
     sudo nginx -t
     ```

   - Restart Nginx:
     ```bash
     sudo systemctl restart nginx
     ```

4. **Verify the Website:**
   - Send an HTTP GET request:
     ```bash
     curl <your-droplet-ip>
     ```


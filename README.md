# Setting Up a Debian 12 Server on DigitalOcean

### Creating SSH Key Pair and Setting Up DigitalOcean Account

1. **Generate an SSH Key Pair:**
   - Ensure you have a `.ssh` directory or create one if absent:
     ```
     mkdir .ssh
     ```
   - Generate an SSH key pair using the Ed25519 algorithm:
     ```
     ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "your-email-address"
     ```

2. **Add SSH Key to Your DigitalOcean Account:**
   - Log in to your DigitalOcean account and navigate to **"Settings"** > **"Security"**.
   - Click **"Add SSH Key"**, then copy the public SSH key:
     - For Windows:
       ```
       Get-Content C:\Users\user-name\.ssh\do-key.pub | Set-Clipboard
       ```
     - For macOS:
       ```
       pbcopy < ~/.ssh/do-key.pub
       ```
   - Paste the key into the designated field, provide a name, and click **"Add SSH Key"**.

### Creating and Configuring a Droplet

1. **Create a Droplet:**
   - Access **"Droplets"** > **"Create Droplet"** in your DigitalOcean account.
   - Select your preferred server (e.g., Debian 12) and click **"Create Droplet"**.

2. **Logging In Using Your SSH Key:**
   - Access your server via SSH using the private key:
     ```
     ssh -i ~/.ssh/do-key root@your-droplet-ip
     ```

### Creating a New Regular User and Granting Permissions

1. **Create a New User:**
   - Add a new user with bash as the default shell and a home directory:
     ```
     sudo useradd -m -s /bin/bash <user-name>
     ```

2. **Set the Password for the New User:**
   - Set a password for the new user:
     ```
     sudo passwd <user-name>
     ```

3. **Grant Administrative Privileges:**
   - Add the user to the sudo group:
     ```
     sudo usermod -aG sudo <user-name>
     ```

4. **Enable SSH Access for the New User:**
   - Copy the `.ssh` directory from the root user to the new user's home directory:
     ```
     sudo cp -r /root/.ssh /home/<user-name>/
     ```

   - Adjust ownership of the directory and files:
     ```
     sudo chown -R <user-name>:<user-name> /home/<user-name>/.ssh/
     ```

   - Test SSH access for the new user:
     ```
     ssh -i ~/.ssh/do-key <user-name>@your-droplet-ip
     ```

### Securing SSH and Installing Nginx

1. **Prevent Root SSH Access:**
   - Edit the SSH configuration:
     ```
     sudo vim /etc/ssh/sshd_config
     ```
   - Set `PermitRootLogin` to `no`, then save and exit.

   - Restart the SSH service:
     ```
     sudo systemctl restart ssh.service
     ```

   - Test root SSH access (should result in permission denied):
     ```
     ssh -i ~/.ssh/do-key root@your-droplet-ip
     ```

2. **Install Nginx:**
   - Update packages:
     ```
     sudo apt update
     ```
   - Install Nginx:
     ```
     sudo apt install nginx
     ```
   - Start and check Nginx status:
     ```
     sudo systemctl start nginx
     sudo systemctl status nginx
     ```

### Configuring Nginx to Serve a Sample Website

1. **Create a Sample Website:**
   - Make a directory for the website:
     ```
     sudo mkdir -p /var/www/my-site
     ```
   - Create an `index.html` file in the directory with sample content.

2. **Configure Nginx Server Block:**
   - Create a configuration file for the server block:
     ```
     sudo vim /etc/nginx/sites-available/my-site
     ```

   - Define the server block configuration.

3. **Enable Configuration and Restart Nginx:**
   - Create a symbolic link:
     ```
     sudo ln -s /etc/nginx/sites-available/my-site /etc/nginx/sites-enabled/
     ```

   - Remove the default symbolic link:
     ```
     sudo unlink /etc/nginx/sites-enabled/default
     ```

   - Test Nginx configuration:
     ```
     sudo nginx -t
     ```

   - Restart Nginx:
     ```
     sudo systemctl restart nginx
     ```

4. **Verify the Website:**
   - Send an HTTP GET request:
     ```
     curl <your-droplet-ip>
     ```


# Linux_Server_Configuration
Practicing a Linux Server Configuration using Amazon Lightsail

### Step 1: Accessing Ubuntu AWS Server on Local Machine:
1. I downloaded the Private Key pair from the AWS module.
2. I moved that private key pair into the .ssh folder on my local machine
3. I used the SSH command to remote log into my server
```
ssh -i ~/.ssh/AWSLightsailPrivateKey.pem ubuntu@54.186.68.156
```
### Step 2: Add 'grader' as a user and give sudo permissions
1. `sudo adduser grader` to create the 'grader' user
2. `sudo nano /etc/sudoers.d/graders` 
3.  Added `grader ALL=(ALL:ALL) ALL` to the sudoers.d/graders file

### Step 3: Generate a key pair for the 'grader' user
1. On the local machine use `ssh-keygen` in the terminal
2. Used the path `~/.ssh/udacity_key`
3. Did not require a password so the grader can have access
4. Go into the 'grader' user directory in the VM
5. `mkdir .ssh` to make a directory for .ssh
6. `touch .ssh/authorized_keys` to make a file called 'authorized_keys'
7. `sudo nano .ssh/authorized_keys` to edit the file
8. Copy and paste the udacity_key.pub contents into the .ssh/authorized_keys file
9. Save and Exit
10. Log into the remote server using `ssh -i ~/.ssh/udacity_key grader@54.186.68.156`

### Step 4: Update and Install Packages on Remote Server
1. `sudo apt-get update`
2. `sudo apt-get upgrade`

### Step 5: Change ssh port to 2200 
1. Add 2200 as a customport on the Amazon Lightsail console
2. `sudo nano /etc/ssh/sshd_config` to open up the sshd_config file
3. Change the 'Port 22' to 'Port 2200'
4. Save and Quit

### Step 6: Configure Uncomplicated Firewall
Configure the firewall to only allow incoming connections from SSH, HTTP, and NTP

### Step 7: Configure Time Zone
`sudo dpkg-reconfigure tzdata`

### Step 8: Install and Configure Apache to serve a Python mod_wsgi App
1. 

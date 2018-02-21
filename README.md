# Linux_Server_Configuration
Practicing a Linux Server Configuration using Amazon Lightsail

### Credentials:

- The EC2 URL: http://ec2-54-186-223-185.us-west-2.compute.amazonaws.com/
- Local IP Address: http://54.186.223.185
- SSH Port: 2200
- Login with: ssh -i ~/.ssh/udacity_key grader@54.186.223.185 -p 2200

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
1. Install the Apache Server Package using `sudo apt-get install apache2`
2. Check your browser to see if the Ubuntu and Apache2 Basic page shows up at the Public IP Address
3. Install #####mod_wsgi for serving python applications with Apache, and install the python helper packages
`sudo apt-get install python-setuptools libapache2-mod-wsgi`
4. Configure Apache to handle requests using the WSGI module by editing the `/etc/apache2/sites-enabled/000-default.conf`
5. Add the line `WSGIScriptAlias / /var/www/catalog/catalog.wsgi`
6. Restart the Apache server using `sudo service apache2 restart`

### Step 9: Install Git
1. Install git using `sudo apt-get install git`

### Step 10.1: Deploy Flask Application
1. Run `sudo apt-get install python-dev`
2. Run `sudo a2enmod wsgi`
3. Move to the '/var/www' directory
4. Run the `sudo mkdir catalog` to make a folder to hold your git repository
5. `cd catalog` to move into the new directory
6. Clone the repository into this directory by using `git clone https://github.com/chanoscar0/catalog.git`
7. Rename the 'application.py' to '__init__.py' by using `sudo mv application.py __init__.py`
8. Now we need to create the virtual environment for our Flask app
9. Install pip to help set-up virtual environment `sudo apt-get install python-pip`
10. Install virtualenv `sudo pip install virtualenv`
11. Create an environment in the '/var/www/catalog/catalog' folder by using `sudo virtualenv venv`
12. Now we need to install Flask within this venv, start by running `source venv/bin/activate`
13. Install Flask using `sudo pip install Flask`
14. Install all of the other packages we need to run the app
    - `pip install httplib2`
    - `pip install requests`
    - `sudo pip install --upgrade oauth2client`
    - `sudo pip install sqlalchemy`
    - `pip install Flask-SQLAlchemy`
15. Update the catalog.conf file using `sudo nano /etc/apache2/sites-available/catalog.conf`
16. Add in your Servername, ServerAdmin into the file
17. Enable it my running `sudo a2ensite catalog`
18. Create the catalog.wsgi file in the '/var/www/catalog' directory
19. `sudo nano catalog.wsgi` and add the code
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")
  
from catalog import app as application
application.secret_key = 'Add your secret key'
```

20. Restart apache `sudo service apache2 restart`

### Step 10.2: Installing Postgre SQL
1. Install the python PostgreSQL adapter psycopg2: `sudo apt-get install python-psycopg2`
2. Install Postgre SQL 
3. Check that no remote connections are allowed in the pg_hba.conf file
4. Edit the database_setup.py, lotsofitems.py, and __init__.py files to read `engine = create_engine('postgresql://catalog:catalog@localhost/catalog')`
5. Also edit the client_secrets to the absolute path `CLIENT_ID = json.loads(open('/var/www/catalog/catalog/client_secrets.json','r').read())['web']['client_id']`

6. Login into Postgresql using command `sudo -u postgres psql`
7. Create a user catalog and password `CREATE USER catalog WITH PASSWORD 'catalog'`
8. Give catalog rights to CreateDB `ALTER USER catalog CREATEDB`
9. Create the database `CREATE DATABASE catalog WITH OWNER catalog`
10. Connect to the database `\c catalog`
11. Revoke rights and grant access to catalog
```
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
```
12. Restart postgresql `sudo service postgresql restart`

### Step 10.3: Run the Application
1. Run the 'database_setup.py' and 'lotsofitems.py' programs
2. Restart Apache
3. Run `python __init__.py` and report to http://ec2-54-186-223-185.us-west-2.compute.amazonaws.com/ to see your application up and running.
### Step 11: Fixing Oauth for Google Login:
1. Go to the google developer console
2. Navigate to credentials and click 'edit'
3. Add your hostname, and ec2 amazon name to the javascript origins area
4. Add ec2 amazon name to the redirect URIs along with the ec2 for gconnect,disconnect,login, and logout

### I also had to make some changes to my own application to get the application running correctly over the server

- I made some edits to the database schema so that each Item will have a unique User ID instead of Username
- I made an edit to the schema to ensure each username was unique for each User
- I made some edits to my application.py file so that when a user connects and is logged in, that user will be created, unless already created within the database
- I edited the create,edit, and delete permission to check for UserID rather than username
- I edited the client_secrets.json file to include all of the redirect URIs and Javascript Origins we added on the google developer console




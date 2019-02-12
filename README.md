# Linux_Server

This Project is a part of Nano Degree based on Full Stack Development by Udacity. This Project This page explains how to secure and set up a Linux server configuration on a virtual machine, install and configure a web and database server to host a web application.

- The Linux distribution is Ubuntu 18.04 LTS.
- The virtual private server is Amazon EC2.
- The web application is my Item Catalog project created earlier in this Nanodegree program.
- The database server is PostgreSQL.
- You can visit http:http://54.208.142.142.xip.io/ compute-1.amazonaws.com/ for the website deployed.
#Steps to connect to Amazon EC2:
- Firstly, open your AWS Educate and login to your account.
- After logging in, go to your lab which is activated for you and Click on start lab. Now your lab is running, and it should run till the end of your deployment process.
- Click on Open Console and then go to All Services. Select EC2.
- Now Change the server to N. Virginia for creating an instance.
- Click on Launch Instance and select your preferred instance. In this project, the instance used is Ubuntu Server 18.04 LTS.
- After selecting, Click on Review and Launch and again select Launch.
- Choose “Create a new Pair” and edit the key pair name and Download it. The downloaded file is of the format “.pem”.
- Now Click on Launch Instance and give it a name. It shows that instance is running.
- Click on Description which is at the end of the page and Copy your Public DNS and Public IP
- Click on view Launch log to check whether it is running successfully.
- Now, click on launch wizard-2 and Click on Inbound. In this you must add the rules for incoming connections i.e., add port for SSH (2200), HTTP (80) and NTP (123) and save.
- To use privatekey link

Public IP address is54.208.142.142.xip.io/

#Linux Server Configuration steps:
- Firstly, create a new folder and place the downloaded .pem file in it.
- open folder in Git Bash by the following command:

 'ssh -i Linux_Server_6.pem ubuntu54.208.142.142'
- you need to update the packages

	  'sudo apt-get update'
	  'sudo apt-get upgrade'
- After installing packages, open the file:

  sudo vi /etc/ssh/sshd_config
In this file, Change the port number from 22 to 2200 and save it (Esc and :wq)

- Restarting the service using :

  'sudo service ssh restart'
- Now open the file with ssh port 2200. It is to check whether the port 2200 is working or not.

'ssh -i linux_Server_6.pem -p 2200 ubuntu@3.84.54.212 address'
- Now add the following commands to configure the Firewall (UFW):

              'sudo ufw default deny incoming'
	       'sudo ufw default allow outgoing'
	       'sudo ufw default allow 2200/TCP'
              'sudo ufw default allow www'
	       'sudo ufw default allow 123/NTP'
	       'sudo ufw default deny 22'
	       'sudo ufw default enable'
After Proceed Option Y/N - Y

-To check status:
		'sudo ufw status'

	''
	To                         Action      From
   	--                         ------      ----
   	2200/tcp                   ALLOW       Anywhere                  
   	80/tcp                     ALLOW       Anywhere                  
   	123/udp                    ALLOW       Anywhere                  
   	22                         DENY        Anywhere                  
   	2200/tcp (v6)              ALLOW       Anywhere (v6)             
   	80/tcp (v6)                ALLOW       Anywhere (v6)             
   	123/udp (v6)               ALLOW       Anywhere (v6)             
   	''22 (v6)                    DENY        Anywhere (v6) 
After Completing the above steps, now you need to create a User.

#Creating a new User grader
- Firstly, login using Ubuntu user. Now, add user using :

  'sudo adduser grader'
Enter password.

- Giving premissions to grader in the sudoers file:

  'sudo visudo'
- Now edit the file by adding below the line: root ALL=(ALL:ALL) ALL Add the following line to give sudo privileges to grader user:

  	'grader  ALL=(ALL:ALL) ALL'
- Save and exit using ctrl+x and confirm with Y.

- To verify the grader as sudo permission:

  'su -grader'
'Enter password'.

- Now, create SSH key-pair for grader. Cofigure keypairs for grader. Create .ssh folder by:

'mkdir /home/grader/.ssh'

-Change to User grader

'su grade'

RUN this command:

  'udo cp /home/ubuntu/.ssh/authorized_keys /home/grader/.ssh/authorized_keys.'
- change ownership:

 'chown grader.grader /home/grader/.ssh.'
Now add grader to sudogroup. sudo su usermod -aG sudo grader

change permissions for .ssh folder:

-chmod 0700 /home/grader/.ssh/'

-For authorized Keys:

  chmod 644 authorized_keys
Now go to vi /etc/ssh/ssh_config. In this you have to edit some authentications

  Change permit root login: no
  Change pubkey authentication: yes
Save and Exit.

Resatrt the Services:

  sudo service ssh restart.
After restarting, open the server with grader:

ssh -i Linux_Server_6.pem -p 2200 grader@54.208.142.142

Setting Time Zone for grader
Cofigure the local time zone to UTC Logged on grader Account

sudo dpkg-reconfigure tzdata
Installing Apache and PostgreSql:
Now install apache software as grader.

sudo apt-get install apache2

Enter public IP of the Amazon EC2 instance into browser (Installation process success or not) --- If success, it displays the APACHE PAGE.

Now again install library functions of apache

 sudo apt-get install libapache2-mod-wsgi-py3
Enable mod_wsgi using:

 sudo a2enmod wsgi
After enable of wsgi, install some libraries of python development:

 sudo apt-get install libpq-dev python-dev
Install And Configure PostgreSQL
To Install postgresql:

sudo apt-get install postgresql postgresql-contrib.

After installing postgresql, change to postgresql from grader user sudo su - postgres psql

After entering to psql Creating User Catalog:

CREATE USER catalog WITH PASSWORD 'catalog';

Now alter the user

ALTER USER catalog CREATEDB;

create a Database:

  create database catalog with owner catalog;
Change to catalog datbase:

  \c catalog.
Revoke all the schemas:

  REVOKE ALL ON SCHEMA public FROM public;
Now grant all the public schemas to catalog:

  GRANT ALL ON SCHEMA public TO catalog;
Exit from the database and switch back to grader user: exit Steps to Insatll Git:

Step1. To install git:

sudo apt-get install git.
step2. Now change the directory to www:

cd /var/www.
step3. Clone your git project here:

sudo git clone ‘path(https://github.com/DARLAVENKATASAIKRISHNA/Item-Catlog)’.
step4.Now change the owner permissions:

sudo chown -R grader:grader catalog. 
step5. Now rename the main Project File:

sudo mv MainFile.py __init__.py.
step6. change the database sqllite engine to postgres engine. In Python Files

engine = create_engine('postgresql://catalog:catalog@localhost/catalog')
Creating Google Oauth Credentials:
Go to your Project page in the Google APIs Console

Click on Credentials from the menu and Create OAuth CLIENT_ID

Now it prompts to configure the Consent Screen.

After that choose Web Application from the list.

Set your Application name.

Go to Authorized JavaScript Origins specify the path

http://54.208.142.142.xip.io/

Setting Redirect URl's

     	http://54.208.142.142.xip.io/login
     
     	http://54.208.142.142.xip.io/gconnect
     
     	http://54.208.142.142.xip.io//callback
Click on Create and now a JSON FILE and CLIENT_ID os created.

Download JSON FILE and copy the file in your folder.

In login.html, replace the new CLIENT_ID.

Replace old client_secrets.json with new client_sectets Enable and Configure a New Virtual Host

To Configure a new Virtual Host

sudo nano /etc/apache2/sites-available/Project.conf

It shows

 '<VirtualHost *:80>
    ServerName http://54.208.142.142.xip.io/
    	ServerAlias ec2-3-84-54-212.compute-1.amazonaws.com
    	ServerAdmin ubuntu@54.210.140.47
    	WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/catalog/venv3/lib/python3.6/site-packages
    	WSGIProcessGroup catalog
    	WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    	<Directory /var/www/catalog/catalog/>
        	Order allow,deny
        	Allow from all
    	</Directory>
    	Alias /static /var/www/catalog/catalog/static
    	<Directory /var/www/catalog/catalog/static/>
        	Order allow,deny
        	Allow from all
   	    </Directory>
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	LogLevel warn
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>'
'Now Enable the virtual host by using the command:

 ' sudo a2ensite catalog'
- After enabling site catalog, to activate the new configuration you need to run:

  'sudo service apache2 reload'
- Steps to set Flask Application

- create file and copy below content

 ''/var/www/catalog/catalog.wsgi
 	import sys
  	import logging
  	logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0, "/var/www/catalog/")
     	from catalog import app as application
     ''	application.secret_key = 'supersecretkey'
- After that perform Reload and restart Apache using: sudo service apache2 reload sudo service apache2 restart

- Now create Virtual Environment.

  - sudo virtualenv -p python venv
- Now change the ownership permissions:

  -  sudo chown -R grader:grader venv.`
- Finally, activate virtual environment:

  ''. venv/bin/activate
#After activating Virtual environment, you need to install the following packages:

	''sudo apt-get install pip3
	 pip3 install flask 
	 pip3 install sqlalchemy 
	 pip3 install httplib2 
	 pip3 install requests 
	 pip3 install --upgrade oauth2client 
	 ''pip3 install psycopg2-binary sudo apt-get install libpq-dev
#By using above commands you successfully install all packages. If you found error no module found then try this command:

 -  sudo -H pip3 install packagename(pip3,flask,sqlalchemy...)
- Now disable the default Apache site using following command:

  - sudo a2dissite 000-default.conf
- The following prompt will be returned:

  - Site 000-default disabled
- To activate the new configuration, you need to run:

  -  service apache2 reload
- Reload Apache:

  'sudo service apache2 reload'
- You need to edit the python source file:

  'sudo vi python __init__.py'
- At database import statement add:

- from catalog.database import *

- At specification of client_secrets.json, replace with /var/www/catalog/catalog/client_secrets.json

- Now save the file and again reload apache.

- Run database file and Sample Items file

		'python database_setup.py'
	 	 'python lots_of_menus.py'
- Now reload and restart the apache
- Security Updates and package updates use these commands.
  	    'sudo apt-get upgrade'
       	    'sudo apt-get dist-upgrade'
- To check any errors found while running the web application, check the error logs by using

  'sudo tail -f /var/log/apache2/error.log'
- After resolving the errors, run the application in the web browser:

http://54.208.142.142.xip.io/

http://ec2-3-84-54-212.compute-1.amazonaws.com

Resources from online for this Project
Udacity: https://in.udacity.com/nanodegree

You Tube: https://www.youtube.com/results?search_query=fullastack+videos+udacity

Stack Overflow: https://stackoverflow.com/questions/18110033/getting-first-row-from-sqlalchemy

Digital ocean: https://www.digitalocean.com/

Reference Readme file from --Prasannarajmallipudi

https://github.com/Prasannarajmallipudi/Linux-Server-Configuration#linux-server-configuration

Special Thanks to our mentors and supporters. Without their help I could not done this project.

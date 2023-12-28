# First Project - Web Stack (LAMP) Implementation in AWS
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. 

Login to AWS MAnagement console.

Parameters when choosing an AWS region which factors should you consider:

* Latency and proximity
* Security and regulatory compliance
* Service Level Agreements (SLA)
* And finally, the most important is the **Cost**

Create a new EC2 instance. It should have a new key-pair to login using SSH and a security group configuration allowing SSH protocol.

Connect to the EC2 instance using any SSH Client. (e.g. GitBash)

For a web application to work smoothly, it has to include an operating system, a web server, a database, and a programming language. The name LAMP is an acronym of the following programs:

* Linux Operating System
* Apache HTTP Server
* MySQL database management systeM
* PHP programming language

### In this project, I will implement a web solution based on LAMP stack on a Linux server by implementing the steps below:

# Step 1 - Installing Apache and Updating the Firewall

* We need to Install Apache using Ubuntu’s package manager, apt:


```
$ sudo apt update

$ sudo apt install apache2
```

**To verify that apache2 is running as a Service in our OS, use following command**

```
 sudo systemctl status apache2
 ```

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet

Add the inbound rules in the security group of the instance. Open port 80 fot http and port 443 for https.

## Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).
 
* To verify and check that we can access it locally in our ubuntu shell and from the internet, run:

```
$ curl http://localhost:80
or
$ curl http://127.0.0.1:80
```


**As an output you can see some strangely formatted test, do not worry, we just made sure that our Apache web service responds to ‘curl’ command with some payload.**

Open a web browser of your choice and try to access following url


```
http://<Public-ip-address>:80
```

*  **Another way to retrieve your Public IP address, other than to check it in AWS Web Console, is to use the following command.**

```
$ curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

If you see Apache 2 Ubuntu Default Page, then it means web server is correctly installed and accessible through firewall 


# STEP 2 - Installing MySQL

### We need to install the database system to be able to store and manage data for our website. MySQL is a popular database management system used within PHP environments.

* To install mysql-server, run:

```
sudo apt -y install mysql-server
```

* After the installation it is recommended that we run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lockdown access to our database system.
* Start the interactive Script by running:


```
sudo mysql_secure_installation
```

* This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

## Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.



Answer Y for yes, or anything else to continue without enabling.


```
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
```

* If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words.



### <p> If we enabled password validation, we’ll be shown the password strength for the root password we just entered and our server will ask if we want to continue with that password. If we are happy with our current password, enter Y for “yes” at the prompt:</p>


### For the rest of the questions, We press Y and hit the ENTER key at each prompt. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes we have made.

* When you're finished, test if you're able to login to the MySQL Console by typing:

```
sudo mysql
```


* To exit the MySQL Console, type: exit

```
mysql> exit
```

### Setting a password for the root MySQL account works as a safeguard, in case the default authentication method is changed from unix_socket to password. For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if we plan on having multiple databases hosted on our server.


### *Note*: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, we’ll need to make sure they’re configured to use mysql_native_password instead. We’ll demonstrate how to do that later.


### Our MySQL server is now installed and secured.


* Next, we will install PHP, the final component in the LAMP Stack.


## Step 3 — Installing PHP


### We have Apache installed to serve our content and MySQL installed to store and manage our data. PHP is the component of our setup that will process code to display dynamic content to the final user. In addition to the php package, we’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. We’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.


* To install these 3 packages at once, run:


```
$ sudo apt -y install php libapache2-mod-php php-mysql
```

* Once the installation is finished, you can run the following command to confirm your PHP version:

```
php -v
```


* At this point, your LAMP stack is completely installed and fully operational.
* To test our setup with a PHP Script, it's best to setup a proper Apache Virtual Host to host our website's file and folders.
Virtual Host allows you to have multiple websites located on a single machine and users of the websites will not even notice it.


* We will configure our first Virtual Host in the next step.

## Step 4 — Creating a Virtual Host for your Website using Apache

In this project, you will set up a domain called projectlamp, but you can replace this with any domain of your choice.


Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory. We will leave this configuration as is and will add our own directory next next to the default one.


Create the directory for projectlamp using ‘mkdir’ command as follows:

```
sudo mkdir /var/www/projectlamp
```

Next, assign ownership of the directory with the $USER environment variable, which will reference your current user.

```
$ sudo chown -R $USER:$USER /var/www/projectlamp
```

* We can now open a new configuration file in Apache’s sites-available directory using our preferred command-line editor. Here, we’ll be using vi

```
$ sudo vi /etc/apache2/sites-available/projectlamp.conf
```

This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:


<img width="719" alt="vi file" src="https://user-images.githubusercontent.com/85270361/123552786-a71d7780-d76f-11eb-9be1-01aeca3c9b70.PNG">


To save and close the file, simply follow the steps below:

* Hit the esc button on the keyboard
* Type :
* Type wq. w for write and q for quit
* Hit ENTER to save the file


#### You can use the *ls* command to show the new file in the sites-available directory

```
$ sudo ls /etc/apache2/sites-available
```


* see my output
  
<img width="596" alt="conf" src="https://user-images.githubusercontent.com/85270361/123552945-9a4d5380-d770-11eb-80b5-cf6061818d71.PNG">


### With this VirtualHost configuration, we’re telling Apache to serve propitixhomes.local using */var/www/projectlamp* as the web root directory. If we like to test Apache without a domain name, we can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.



You can now use a2ensite command to enable the new virtual host:

```
$ sudo a2ensite projectlamp
```


* We might want to disable the default website that comes installed with Apache. This is required if we are not using a custom domain name, because in this case Apache’s default configuration would overwrite our virtual host. To disable Apache’sdefault website use a2dissite command, type:


```
$ sudo a2dissite 000-default
```


* To make sure your configuration file doesn't contain syntax errors, run :

```
$ sudo apache2ctl configtest
```


* Finally, reload Apache so these changes take effect:

```
$ sudo systemctl reload apache2
```


Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:


* Type and run :

```
$ sudo vi /var/www/projectlamp/index.html
```


Now go to your browser and try to open your website URL using IP address:

```
http://<public-ip-address>:80
```
or you can also browse using your public dns. the result is the same

```
http://<Public-DNS-Name>:80
```

You should see your content from index.html file.



You can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once you do that, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.


* To check your Public IP from the Ubuntu shell, run :

```
$(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 
$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)
```


## Step 5 — Enable PHP on the website


With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

```
sudo vim /etc/apache2/mods-enabled/dir.conf
```

<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>


After saving and closing the file, you will need to reload Apache so the changes take effect:

```
$ sudo systemctl reload apache2
```


* Finally, we will create a PHP Script to test the PHP is correctly installed and configured on our Server.
* Now that we have a custom location to host our website's files and folders, we'll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.
* Create a new file named index.php inside our custom web root folder:

```
$ vim /var/www/projectlamp/index.php
```

This will open a blank file. Add the following text, which is valid PHP code, inside the file:

```
<?php
phpinfo();
```


<img width="744" alt="p" src="https://user-images.githubusercontent.com/85270361/123558938-0d66c200-d791-11eb-8ee2-b87069d183c1.PNG">


## When you are finished, save and close the file, refresh the page and you will see a page similar to this :



<img width="943" alt="Capture p" src="https://user-images.githubusercontent.com/85270361/123559012-78b09400-d791-11eb-9fae-2baad31d34b0.PNG">


* This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.

* If you can see this page in your browser, then your PHP installation is working as expected.

* After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so:


```
$ sudo rm /var/www/projectlamp/index.php
```

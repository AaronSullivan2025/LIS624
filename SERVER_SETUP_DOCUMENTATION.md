# Server Setup Documentation  
  
The LAMP stack is a software combination used to create and support websites and their interacting applications. It’s built from four layers, namely **L**inux (the operating system), **A**pache (the web server), **M**ySQL (the database), and **P**HP (the programming language used to shuttle commands and resources between the components). It’s commonly used because it’s Open-Source (i.e., free), reliable, flexible, easy to deploy, and has a large community support system to use.  


# Installations 
  
*As with all our projects, I start each install by making sure my machine is fully updated by running through the necessary code in my BASH shell:*  
```  
sudo apt update  
sudo apt upgrade  
sudo apt autoremove  
sudo apt clean  
```

## Installing APACHE
Searched for and installed Apache:

`apt search apache2 | head`  
`apt show apache2`  
`sudo apt install apache2`  

Checked the status info with “systemctl” for output showing “enabled” and “active.”  — `systemctl status apache2`

Created HTML page to verify everything worked and webpage showed HTML output as expected. 


## Installing PHP  
My first step was to install php and its modules: `sudo apt install php libapache2-mod-php`  

I then restarted Apache with:  `sudo systemctl restart apache2`  

and checked the version of php with:  `php -v`  

Before using systemctl to confirm Apache is doing what it’s supposed with:  ` systemctl status apache2`  

After php was installed, I created a small php file in the web document root by navigating there and using nano to create a file called info.php:  

`cd /var/www/html/`  
`sudo nano info.php`  

In nano, I placed the following php code before saving and exiting:   
  
```   
<?php  
phpinfo();  
?>
```  

Going to my external IP and checking — `http://34.171.47.114/info.php`  — I saw the PHP install page I was supposed to see. I then deleted that file as it was a system vulnerability at that point.
  
`sudo rm /var/www/html/info.php`     

I configured everything to work properly with Apache (first navigating to the proper directory, making a backup copy in case I screwed up), using nano:  

```
cd /etc/apache2/mods-available/  
sudo cp dir.conf dir.conf.bak  
sudo nano dir.conf  
```  

I modified the DirectoryIndex to hit index.php first, save and exit nano: `DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`

I then verified the configuration with apachectl (getting the “Syntax Ok” message I wanted to see), reloaded Apache and checked its status.  

```  
apachectl configtest  
sudo systemctl reload apache2  
systemctl status apache2  
```  

Finally, I modified my index.php file with nano to show an HTML message after running some php code to detect what browser is being used by whoever opens the page on the web.

`cd /var/www/html/`  
`sudo nano index.php`  
  
I verified by navigating my browser to my page— `http://34.171.47.114/`  — and Apache now returns the index.php.

  
  

## Installing MySQL  
I installed MySQL server and after installing, confirmed the version:  

```
sudo apt install mysql-server  
mysql --version
```

I checked if it's running and enabled using the `systemctl` command. I looked for and found the lines beginning with *Loaded* and *Active*. I then ran the post installation script for creating a secure, baseline configuration of MySQL.  

```  
systemctl status mysql   
sudo mysql_secure_installation
```  

I went through a series of prompts for configuring authentication:

```   
Validate passwords: Y  
Password validation policy: 0 (zero) for LOW  
Remove anonymous users: Y  
Disallow root login remotely: Y  
Remove test database and access to it: Y  
Reload privilege tables now: Y
```

I logged in to the new database using the following command:  `sudo mysql -u root`  

The machine now added a new cursor prompt to indicate that we moved to MySQL, notably and unsurprisingly:   
  
`mysql>`  

I moved on to creating a regular MySQL user account for my normal use cases.  

`mysql> create user 'opacuser'@'localhost' identified by 'XXXXXXXXX';`  
  
I created a password (replacing the Xs) and checked that the prompt returned a Query OK message, which it did.  I moved on to create a new database for the user account I had. As per my instructions, I named the database `opacdb` and set the character encoding to UTF-8 to support international characters. Then I ran the show command to view the new database, followed by granting all privileges on the database to the user account `opacuser`.

```  
mysql> create database opacdb default character set utf8mb4 collate utf8mb4_0900_ai_ci;  
mysql> show databases;  
mysql> grant all privileges on opacdb.* to 'opacuser'@'localhost';
```
  
I modified the MySQL command line client as instructed by opening  ~/.bashrc file, scrolling to the bottom of the code to add a new “export,” and then saving, exiting, abnd sourcing the file:  

```  
nano ~/.bashrc  
export MYSQL_PS1="[\d]> "  
source ~/.bashrc  
```
  
I logged back into MySQL and use the -p option when doing so as opacuser. This instructs MySQL to request the password for this user.  
  
`mysql -u opacuser -p`  
  
I listed the available databases and used the use command to switch to the new opacdb database:  
```  
mysql> show databases;  
mysql> use opacdb;
```
  

Then, I created and defined a new table for the opacdb database.  The table I called *books* and kept it simple using only four fields: *id*, *author*, *title*, and *copyright*. 

```  
mysql> create table books (  
        id int unsigned not null auto_increment,  
        author varchar(150) not null,  
        title varchar(150) not null,  
        copyright year not null,  
        primary key (id)  
);  
```

I confirmed all of this by running the following two commands.   
```  
mysql> show tables;  
mysql> describe books;  
```

Then I added data using the MySQL *insert* command to add records into the books table. I 
viewed all the new records with the MySQL select command:  

`mysql> select * from books;`  
  
I then proceeded to test the table by running a number of MySQL, and having done this, I then created the connection between PHP and MySQL so that I can use both for my websites.  
  
First, I installed PHP support for MySQL:  `sudo apt install php-mysql`  
  
And then restarted Apache and MySQL:  
```  
sudo systemctl restart apache2  
sudo systemctl restart mysql  
```
  
I then created a *login.php* file for self-authentication in my document root's parent directory: */var/www*. I also changed the group ownership of the file and its permissions so the file could be read by the Apache web server but not by the world.   

  ```
cd /var/www  
sudo touch login.php  
sudo chmod 640 login.php  
sudo chown :www-data login.php  
ls -l login.php  
sudo nano login.php  
```
  
In the file, I added the following credentials (using my password in place of the X’s):  

```
<?php // login.php  
$db_hostname = "localhost";  
$db_database = "opacdb";  
$db_username = "opacuser";  
$db_password = "XXXXXXXXX";  
?>
```


Next was a new PHP file for the website: 

```
cd /var/www/html  
sudo nano opac.php  
```

Once nano was open, I transcribed code to present an HTML display with my table data.  I then saved the file and exited out of nano.   I finished by first testing the PHP syntax. All was fine as the expected HTML was outputted:  

```  
sudo php -f /var/www/login.php  
sudo php -f /var/www/html/opac.php  
```
  
I viewed the site from my browser by opening the public IP address for my server. It showed me the data in my opacdb database and books table rendered in my webpage.  


## Issues  

As my install steps have shown, I have encountered few if any errors. 

(1) With the Apache Server install, I had to go back and adjust my local host navigation as "elinks http://localhost" having stupidly omitted the "http://" originally.  
  
(2) With the MySQL install I ran into a memory problem where, for some unfathomable reason, the machine kept telling me I had a full virtual disc (96% full, anyway) and couldn't continue with the install once I reached the point where I was adding the php-MySQl connections. To fix it, I eventually had to uninstall the whole MySQL arrangement and then reinstall from the beginning. This appeared to have fixed the problem as I encountered no such hassles the second time through.  
  

## Links
[Link to index.php page on Ubuntu Server](http://34.41.8.169/index.php)  
  
[Link to opac.php page on Ubuntu Server](http://34.41.8.169/opac.php)  
  

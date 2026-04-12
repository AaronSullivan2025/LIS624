## 2026-04-08 – Installing Omeka  
**Goal:** Learn commands and usage surrounding the installation of Omeka  
**Context:** Meet the listed PASS requirements for the related assignment  
  
**Steps:**  
I start with this session's material the way I always start in the shell. I do the update routine. 

`sudo apt update ; sudo apt upgrade -y ; sudo apt autoremove -y ; sudo apt clean`  

With this out of the way, I turn to the session documentation.  I make sure ythat my installed versions of PHP and MySQL meet 
Omeka's system requirements. I then install the required PHP extensions in case they aren't already installed:  
  
```  
sudo apt install mysqli  
sudo apt install exif
```
      
Then I install the ImageMagick suite of utilities to work with photo files.   
  
`sudo apt install imagemagick`  
  
Following this I enable the Apache mod_rewrite module to rewrite URLs. 
  
`sudo a2enmod rewrite`
  
Then it's time to restart Apache:  
  
`sudo systemctl restart apache2`  

At this point, it's amatter of reviewing steps from earlier parts of the project as I'm going to be repeating a lot it in parts.

(1) I create a new user and a new database in MySQL for the Omeka installation:  

```  
sudo mysql -u root  
  
mysql> create user 'omeka'@'localhost' identified by '[Password]';  
mysql> create database omeka default character set utf8mb4 collate utf8mb4_0900_ai_ci;  
mysql> show databases;  
mysql> grant all privileges on omeka.* to 'omeka'@'localhost';
```

Exit MySQL — `mysql> /q`  
   
(2)  I download the latest Omeka Classic release as a Zip file and extract it in /var/www/html:  
  
```
cd /var/www/html   
sudo wget https://omeka.org/classic/download/v3.2/omeka-3.2.zip  
sudo unzip omeka-3.2.zip  
```  

I rename it simply omeka: `mv omeka-3.2 omeka`  
  

In the new omeka directory, I find the db.ini file and add my new database credentials, replacing all values containing XXXXXX 
(host, user, password, and dbname) with the appropriate information.  
  
```  
cd /var/www/html/omeka  
sudo nano db.ini  
```


I use the chown command to give Apache group write access on all files:  
    
`sudo chmod -R g+w *`    
  
I restart Apache2: `sudo systemctl restart apache2`

In my browser, I go to `http://34.41.8.169/omeka/` and complete the setup through Omeka. 
After setting everything up, I add a couple of items through the dashboard (same URL, but add "admin" after last slash).  
  
  
[Omeka Link](http://34.41.8.169/omeka/) 




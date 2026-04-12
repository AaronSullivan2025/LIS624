## 2026-04-02 – Installing Wordpress  
**Goal:** Learn commands and usage surrounding the installation of Wordpress  
**Context:** Meet the listed PASS requirements for the related assignment  
  
**Steps:**  

I began by starting and restarting my computer a few times — despite getting the shell back up and working earlier this week, 
I've been getting some latency issues where it seems to take forever between inputting a command and the system responding. I 
have also started getting into the habit of using the "RESET" function on Google dashboard to clear out memory "vagrants" (a 
term I cam across online). Having done that, everything starts working again t the speeds I am expecting.  


Now, diving into this session's material, I start the way I always start in the shell. I do the update routine. However, I now see
that I can join the commands together in a single line and do so:  

`sudo apt update ; sudo apt upgrade -y ; sudo apt autoremove -y ; sudo apt clean`  

With this out of the way, I turn to the session documentation. I verify I have the necessary iterations of the software I'll be using,
install the new php modules I'll need, and then restart Apache and MySQL:  

```
php --version  
mysql --version  
cat /etc/issue.net  
sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl. 
sudo systemctl restart apache2  
sudo systemctl restart mysql  
```

From this week's video, I see I'll need to install "unzip" and I do: `sudo apt install unzip` and then I download and unzip Wordpress:  

```
cd /var/www/html. 
sudo wget https://wordpress.org/latest.zip  
sudo unzip latest.zip
```
Then I create the new Wordpress user and database. Through out, I keep the paths and use as simple of passwords as the system will let me, 
because I am both boring and lazy. This means I go in as root user to MySQL and then create the user, create the database, establish privileges,
and verify:

```  
sudo mysql -u root  
  
create user 'wordpress'@'localhost' identified by 'XXXXXXXXX';  
create database wordpress;  
grant all privileges on wordpress.* to 'wordpress'@'localhost';  
show databases;  
```  
Having verfied the DB's existence, I then quit MySQL — `\q`  

Now I go about modifying my config file as needed. I change directory to my new "Wordpress" one, copy the config file, change the name of the copy,
and open it in my text editor nano. Scrolling down in nano, I look for the fields (DB_NAME, DB_USER, and DB_PASSWORD)I need to update to my chosen 
names and password and update them:   

```  
cd /var/www/html/wordpress  
sudo cp wp-config-sample.php wp-config.php  
sudo nano wp-config.php  
```  
  
There are steps given in the doumentation for what to do if Wordpress can;t write directly to file, but that doesn;t come up as an issue for me, so I
simply note the instruction's existence and copy them here for future reference if a problem pops up in the future:  

"add the following line at the end of wp-config.php:" `define('FS_METHOD','direct');`  

Since I stuck to the base naming, I don't need to go throught the optional material referring to what to do if you get creative. 
I move on to working through the online install of the Wordpress software proper, naming my page "AaronWordpressLibrary" and the new user
as "AaronLIS624" because eponymous should be easiest to deal with moving forward.  I use the system provided password, writing it down in my notes 
and saving it on my computer.  

Finally, I verify my new page at `http://34.41.8.169/wordpress/wp-admin/` and everything works as the documentation says to expect.

[Wordpress Page](http://34.41.8.169/wordpress/wp-admin/)  


    


 




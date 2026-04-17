## 2026-04-16 – Installing Koha  
**Goal:** Learn commands and usage surrounding the installation of Omeka  
**Context:** Meet the listed PASS requirements for the related assignment  
  
**Steps:**  
This time around, before I ever start with my BASH shell, I have to create a new VM. I go to the Google Cloud Console and
create a new VM based on the instructions:  
  
**Machine Configuration**: e2-medium (2 vCPU, 1 core, 4 GB memory)  
**OS and storage**: Ubuntu 24.04 LTS, 20GB Hard Disk  
**Networking**: Allow HTTP traffic  
**Network Tag**: koha-staff-8080, koha-opac-8081   
  
I move on to the Firewall rules—  
I go back to the Google Cloud Console.  
  
Firewall Rule 2: Port 8080  
—Click on VPC Network.  
—Click on Firewall.  
—At the top of the page, choose Create a firewall rule (Do not choose Create a firewall policy):  
—Add name: koha-staff-8080  
—Add description: Open port 8080 for the Koha staff interface  
—Next to Targets, click on Specified target tags.  
—In Target tags, add our tag name: koha-staff-8080.  
—In the Source IPv4 ranges, we allow access from anywhere (0.0.0.0/0) to simplify setup. In a real deployment, we might want to restrict administrative access to port 8080 to specific IP addresses.  
—Click on Specified protocols and ports  
—Click on TCP  
—Add 8080 in the Ports box  
—Click on Create  

Firewall Rule 2: Port 8081
—Create a second firewall rule  
—Click on Firewall.  
—At the top of the page, choose Create a firewall rule (Do not choose Create a firewall policy):  
—Add name: koha-opac-8081  
—Add description: Open port 8081 for the Koha opac interface  
—Next to Targets, click on Specified target tags.  
—In Target tags, add our tag name: koha-opac-8081.  
—In the Source IPv4 ranges, we allow access from anywhere (0.0.0.0/0) to simplify setup. In a real deployment, we may or may not want to restrict public access to port 8081 to specific IP addresses.  
—Click on Specified protocols and ports  
—Click on TCP  
—Add 8081 in the Ports box  
—Click on Create  


With this done, I start with this session's BASH material. Again, there's a small change this time around from the way the way I always start in the shell.  
Before starting anything else I start up `tmux` < - This is a multiplexer. Because Koha is a big install and might take a while, If I'm disconnected while 
working, tmux re-establishes my connection to my virtual machine, and I can simply run the following command to re-open my session: `tmux attach`  

  
**NOW** I do my general update routine. 

`sudo apt update ; sudo apt upgrade -y ; sudo apt autoremove -y ; sudo apt clean`  

Then, I turn to the remaining session documentation. 
I add repositories to sync with and to use to download software, and this includes the Koha ILS. First, I use the following three commands to set up
the signing keys to ensure that we're downloading the authentic software.  
  
```  
sudo apt install apt-transport-https ca-certificates curl  
sudo mkdir -p --mode=0755 /etc/apt/keyrings. 
sudo curl -fsSL https://debian.koha-community.org/koha/gpg.asc -o /etc/apt/keyrings/koha.asc  
```
  
Next, I become the root user with the following command: `sudo su`  
  
And paste the following into my terminal.   

```    
tee /etc/apt/sources.list.d/koha.sources <<EOF
Types: deb. 
URIs: https://debian.koha-community.org/koha/  
Suites: 25.05  
Components: main. 
Signed-By: /etc/apt/keyrings/koha.asc  
EOF  
```

I use `cat /etc/apt/sources.list.d/koha.sources` to view and verify that the contents match the input above. Then I exit the root Linux user account and return to 
my regular user account: `exit`  

### I Install MariaDB ###   
  
I install it first in case it helps to let Koha know, so to speak, that it's using MariaDB as Koha is installed.  

```    
sudo apt update. 
sudo apt install mariadb-server
```
  

### I Install Koha ###
  
I to update/sync the new repository with the Koha remote repository. This just means that we use apt update again.

```    
sudo apt update  
apt show koha-common  
sudo apt install koha-common
```

I then configure Koha and Apache to use different ports for the staff and public interfaces. Specifically, I 
configure Koha to access the staff interface through port 8080 and the public interface through port 8081. 
These match the firewall rules I set up earlier.  

To do this I need to edit the file at `/etc/koha/koha-sites.conf`. Since this is an important configuration file, I make a copy of it first:  
  
`sudo cp /etc/koha/koha-sites.conf /etc/koha/koha-sites.conf.backup`  
  
Then I use my text editor to open `/etc/koha/koha-sites.conf`. At the end of the INTRAPORT= line, I add 8080. At the end of the OPACPORT= line, I add 8081:  
```  
INTRAPORT="8080"  
OPACPORT="8081"  
```

### Apache2 Setup ###  

Koha requires that we enable a few Apache modules with the a2enmod command:  

```    
sudo a2enmod rewrite cgi headers proxy_http  
sudo systemctl restart apache2
```  

I've already configured Koha to listen on ports 8080 and 8081, but I also need to configure Apache2 to do so also. 
Again, since this is an important configuration file, I make a copy:  
  
`sudo cp /etc/apache2/ports.conf /etc/apache2/ports.conf.backup`
  
Then I use my text editor to open `/etc/apache2/ports.conf`. Under "Listen 80" at the top of the file, I add the following two lines:  
  
```. 
Listen 8080  
Listen 8081  
```

    
### Create Koha Instance ###  

I use the koha-create command to create a Koha library called bibliolib.  Then I restart Apache2:  
  
```  
sudo koha-create --create-db bibliolib  
sudo systemctl restart apache2
```

    

### Additional Apache2 Configurations ###

The /var/www/html directory normally serves as the web document root on fresh Apache2 installs. The a`2dissite 000-default` command turns this off. 
The `a2enmod deflate` command turns on network compression. This means that data from the Koha server is compressed before it's sent to a client machine. 
The `a2ensite bibliolib` command enables the new bibliolib library, which has already been configured by Koha:  
   
```  
sudo a2dissite 000-default   
sudo a2enmod deflate   
sudo a2ensite bibliolib   
```
  
I reload Apache2's new configurations and restart it:  
  
```  
sudo systemctl reload apache2  
sudo systemctl restart apache2
```
  
  
### Koha Web Installer ###

I now can complete the installation through a web installer. First, I get the user name and password using the `koha-passwd` command. I save this info:  
  
`sudo koha-passwd bibliolib`  
  
Example output:  

```. 
Username for bibliolib: koha_bibliolib  
Password for bibliolib: M(x_d40k.{;_=;zG  
Press enter to clear the screen...  
```

I go to the public IP address for my machine in order to visit the Koha site and complete the installation. I add ":8080" to the end of my IP address to access
the site using the staff port. I go through the onboarding process. To visit the OPAC section of the site, I add ":8081" to the end of my IP address.  
  
[Koha Staff Link](http://34.172.11.249:8080/cgi-bin/koha/mainpage.pl)    
[Koha OPAC Link](http://34.172.11.249:8081/) 


      
  

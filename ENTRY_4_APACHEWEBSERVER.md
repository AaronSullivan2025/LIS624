# 2026-02-28 – Installing Apache Server

**Goal**: Learn commands and usage surrounding the installation of the Apache server  

**Context**: Meet the listed PASS requirements for the related assignment  

**Steps**:    

As is the procedure, I started by opening my BASH shell and checked for updates, running   

`sudo apt update`  
`sudo apt -y upgrade`  


After updating, I sued apt to find and install the necessary package. That means I searched for the specific package, piping the result through the head command to minimize the verbiage I would have to parse. Finally, with sudo apt, I actually did the installation itself:  

`apt search apache2 | head`  
`apt show apache2`  
`sudo apt install apache2`  

Following, it was necessary to check if the server was enabled and running, which I did by checking the status info with “systemctl” for output showing “enabled” and “active.”  

`systemctl status apache2`    

In order to verify the server functioned properly, I first installed a Text-Based Browser. I chose elinks as the alternative, w3m, defaults to Vim keybindings and I earlier in the program chose against Vim.    

`sudo apt install elinks`  

The loopback address I verified against was 127.0.0.1 because “localhost” didn’t work at first.     

`elinks 127.0.0.1`   


After looking online, I found that I needed to use differnt command text then just "localhost" following the elinks leader:    

`elinks http://localhost`   

This returned the anticipated confirmation page and message:   

**Apache2 Ubuntu Default Page** <br>
**It works!,** <br>

I then went about creating a web page myself for this web server. I navigated to the directory where the default file was housed (index.html), backed it up by copying it to a differently named new file, and then created a new default file with the old one’s name:

```
cd /var/www/html/
sudo mv index.html index.original.html
```  

To change the contents, I opened it in nano:

`sudo nano index.html`


…and then played around with some HTML code.

```
<html>
<head>
<title>My first web page using Apache</title>
</head>

<body>
<h1>Welcome</h1>
<p>Welcome to my web site. <br><br>
I created this site using the<u><b><i> Apache HTTP server</i></b></u>.</p>
</body>
</html>
``` 

I also tested the continuing existence of the previous saved copy by navigating to its new IP.  

`http://34.171.47.14/index.original.html`  

Which still shows the default Apache2 “It works!” page. After that, I finished the assignment with the forum post including the link to my test page’s location:  

`http://34.171.47.114/`  


**Results:**  
As in previous assignments, I encountered little difficulty. In this case, I did have to force the localhost navigation with elinks using the http:// preface, but that’s small beans.  


**Reflection:**  
I learned that web services default to a specific html resource file called “index.html” and where it is stored, I learned how to start using elinks as a Text-Based Browser, and I now know some IP address resolution for my personal resources.  

     
**Conclusion:**  
Apache web server now up and running and verified.  
  
  





# 2026-02-28 – Installing PHP

**Goal:** Learn commands and usage surrounding the installation of PHP 

**Context:** Meet the listed PASS requirements for the related assignment  

**Steps:**    

My first step is to install php and its modules. I did that and simultaneously installed the module in question using:    

`sudo apt install php libapache2-mod-php`  

I then restarted Apache with:  

`sudo systemctl restart apache2`  

and checked the version of php with:  

`php -v`  

Before using systemctl to confirm Apache is doing what it’s supposed with:  

` systemctl status apache2`  


Now that php was installed, I created a small php file in the web document root by navigating there and using nano to create a file called info.php:  

`cd /var/www/html/`  
`sudo nano info.php`  

In nano, I placed the following php code before saving and exiting:   
  
```   
<?php  
phpinfo();  
?>
```  
  

Going to my external IP and checking.   

`http://34.171.47.114/info.php`  
  
I see the PHP install page I’m supposed to see. I then delete the file as it’s a system vulnerability at that point.
  
`sudo rm /var/www/html/info.php`     

I now need to configure everything to work properly with Apache, so I navigate to the proper directory, make a backup copy in case I screw up, and then open the old version with nano:  

```
cd /etc/apache2/mods-available/  
sudo cp dir.conf dir.conf.bak  
sudo nano dir.conf  
```  

I then modify the DirectoryIndex to hit index.php first, save and exit nano.   

`DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`


I then verify the configuration with apachectl (getting the “Syntax Ok” message I want to see), reload Apache and check its status.  

```. 
apachectl configtest  
sudo systemctl reload apache2  
systemctl status apache2  
```  

Finally, I modify my index.php file with nano to show an HTML message after running some php code to detect what browser is being used by whoever opens the page on the web.

`cd /var/www/html/`  
`sudo nano index.php`  


```  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Browser Detector</title>
</head>
<body>
    <h1>Browser & OS Detection</h1>
    <p>You are using the following browser to view this site:</p>

    <?php
    $user_agent = $_SERVER['HTTP_USER_AGENT'];

    // Browser Detection
    if (stripos($user_agent, 'Edg') !== false || stripos($user_agent, 'Edge') !== false) {
        $browser = 'Microsoft Edge';
    } elseif (stripos($user_agent, 'Firefox') !== false) {
        $browser = 'Mozilla Firefox';
    } elseif (stripos($user_agent, 'Chrome') !== false && stripos($user_agent, 'Chromium') === false) {
        $browser = 'Google Chrome';
    } elseif (stripos($user_agent, 'Chromium') !== false) {
        $browser = 'Chromium';
    } elseif (stripos($user_agent, 'Opera Mini') !== false) {
        $browser = 'Opera Mini';
    } elseif (stripos($user_agent, 'Opera') !== false || stripos($user_agent, 'OPR') !== false) {
        $browser = 'Opera';
    } elseif (stripos($user_agent, 'Safari') !== false && stripos($user_agent, 'Chrome') === false) {
        $browser = 'Safari';
    } else {
        $browser = 'Unknown Browser';
    }

    // OS Detection
    if (stripos($user_agent, 'Windows') !== false) {
        $os = 'Windows';
    } elseif (stripos($user_agent, 'iOS') !== false || stripos($user_agent, 'iPhone') !== false || stripos($user_agent, 'iPad') !== false) {
        $os = 'iOS';
    } elseif (stripos($user_agent, 'Android') !== false) {
        $os = 'Android';
    } elseif (stripos($user_agent, 'Mac') !== false || stripos($user_agent, 'Macintosh') !== false) {
        $os = 'Mac';
    } elseif (stripos($user_agent, 'Linux') !== false) {
        $os = 'Linux';
    } else {
        $os = 'Unknown OS';
    }

    // Output Result
    echo "<p>Your browser is <strong>$browser</strong> and your operating system is <strong>$os</strong>.</p>";
    ?>

</body>
</html>
```

Having done this, I verify it works by navigating my browser to my IP:
`http://34.171.47.114/`  

Apache now returns the index.php instead of index.html, but if there was a problem the system would move on to the html file as it’s next in the DirectoryIndex line of the dir.conf file.

**Results:**  
Again, following the documentation provided produced no errors when my clumsy fingers didn’t mistype.  


**Reflection:**  
I’d never worked with php before so I consider this a nice start. I’ll very likely be following up by adding php to my study list.


**Conclusion:**  
PHP is installed and appears to be working the way it should with my Apache install on my web server.


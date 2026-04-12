## 2026-03-13 – Installing MySQL
**Goal:** Learn commands and usage surrounding the installation of MySQL
**Context:** Meet the listed PASS requirements for the related assignment

**Steps:**

As with all our projects, I started by making sure my machine was fully updated by running through the necessry code in my BASH shell:  

```
sudo apt update  
sudo apt upgrade  
sudo apt autoremove  
sudo apt clean  
```

I then installed MySQL server and after installing, confirmed the version:  

```
sudo apt install mysql-server  
mysql --version
```

I checked then to determine if it's running and enabled using the `systemctl` command. I looked for and found the lines beginning with *Loaded* and *Active*. I then ran the post installation script for creating a secure, baseline configuration of MySQL.  

```  
systemctl status mysql   
sudo mysql_secure_installation
```  

This took me through a series of prompts to which I used the responses given in our class materials:  

```   
Validate passwords: Y  
Password validation policy: 0 (zero) for LOW  
Remove anonymous users: Y  
Disallow root login remotely: Y  
Remove test database and access to it: Y  
Reload privilege tables now: Y
```

Then I logged in to the new database using the following command:  

`sudo mysql -u root`  

The machine now added a new cursor prompt to indicate that we moved to MySQL, notably and unsurprisingly:   
  
`mysql>`  

My first command in the database was to list the existing structures (and I also need to keep in mind that all MySQL commands end with a semicolon):  

`mysql> show databases;`
  
The database structure shown were — *information_schema*, *mysql*, *performance_schema*, and *sys*. This was as expected.  
  
I moved on to creating a regular MySQL user account for my normal use cases.  

`mysql> create user 'opacuser'@'localhost' identified by 'XXXXXXXXX';`  
  
I created a password (replacing the Xs) and checked that the prompt returned a Query OK message, which it did.  

I moved on to create a new database for the user account I had. As per my instructions, I named the database `opacdb` and set the character encoding to UTF-8 to support international characters.   

Then I ran the show command to view the new database, followed by granting all privileges on the database to the user account `opacuser`.

```  
mysql> create database opacdb default character set utf8mb4 collate utf8mb4_0900_ai_ci;  
mysql> show databases;  
mysql> grant all privileges on opacdb.* to 'opacuser'@'localhost';
```
  
     
>**Notes on the `create` database command**  
(1) The term collate refers to the rules used to compare and sort strings of text.  
(2) The character set is set to utf8mb4, which supports the full range of Unicode (e.g., includes emojis, and more).  
(3) The 0900 refers to Unicode 9.0.  
(4) The ai refers to accent insenitive, which means that characters like é and e are treated the same for searching and sorting.  
(5) The ci refers to case insentivie, which means that a search for *Shakespeare* also finds *shakespeare*.      
>     
    
I then exited out of the MySQL database as the root MySQL user. 
  
`mysql> \q`  
  
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

-The id field will function as a primary key and restrict it to an integer id (or whole number) 
--should only be a positive number unsigned
--should not be empty not null,
--should with each record increment by a single integer auto_increment.   

-The author and title fields can have a maximum length of 150 characters and should not be empty
-The copyright field should be limited it to the YEAR data type, which stores four-digit years in YYYY format, and should not be empty

```  
mysql> create table books (  
        id int unsigned not null auto_increment,  
        author varchar(150) not null,  
        title varchar(150) not null,  
        copyright year not null,  
        primary key (id)  
);  
```

I then confirm all of this by running the following two commands.   
```  
mysql> show tables;  
mysql> describe books;  
```

Then I added data using the MySQL *insert* command to add records into the books table. I had to specify three fields when entering data: author, title, and copyright.   

  ```
mysql> insert into books (author, title, copyright) values  
('Jennifer Egan', 'The Candy House', '2022'),  
('Imbolo Mbue', 'How Beautiful We Were', '2021'),  
('Lydia Millet', 'A Children\'s Bible', '2020'),  
('Julia Phillips', 'Disappearing Earth', '2019');  
```
  
As per the teacher’s instructions, I added a few records of my own. I then viewed all the new records with the MySQL select command:  

`mysql> select * from books;`  
  
I then proceeded to test the table by running following commands, one at a time:  
  
```
mysql> select author from books;  
mysql> select copyright from books;  
mysql> select author, title from books;  
mysql> select author from books where author like '%millet%';  
mysql> select title from books where author like '%mbue%';  
mysql> select author, title from books where title not like '%e';  
mysql> select * from books;  
mysql> alter table books add publisher varchar(75) after title;  
mysql> describe books;  
mysql> update books set publisher='Simon & Schuster' where id='1';  
mysql> update books set publisher='Penguin Random House' where id='2';  
mysql> update books set publisher='W. W. Norton & Company' where id='3';  
mysql> update books set publisher='Knopf' where id='4';  
mysql> select * from books;  
mysql> delete from books where author='Julia Phillips';  
mysql> insert into books  
       (author, title, publisher, copyright) values  
       ('Emma Donoghue', 'Room', 'Little, Brown & Company', '2010'),  
       ('Zadie Smith', 'White Teeth', 'Hamish Hamilton', '2000');  
mysql> select * from books;  
mysql> select author, publisher from books where copyright < '2011';  
mysql> select author from books order by copyright;  
mysql> \q  
```  
  
Having done this, I then created the connection between PHP and MySQL so that I can use both for my websites.  
  
First, I installed PHP support for MySQL.   

`sudo apt install php-mysql`  
  
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

The `sudo touch login.php` command creates the file with root as the owner.   
  
The `chown :www-data login.php` command changes the group ownership to www-data. The www-data user is the Apache user.  
  
In the file, I added the following credentials (using my password in place of the X’s):  

```
<?php // login.php  
$db_hostname = "localhost";  
$db_database = "opacdb";  
$db_username = "opacuser";  
$db_password = "XXXXXXXXX";  
?>
```


Next was a new PHP file for the website. This file displays HTML but will primarily be PHP interacting with my opacdb database.  

```
cd /var/www/html  
sudo nano opac.php  
```

Once nano was open, I transcribed the following code:  

```  
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>MySQL Server Example</title>  
</head>  
<body>  
  
    <h1>A Basic OPAC</h1>  
    <p>We can retrieve all the data from our database and book table using a couple of different queries.</p>  
  
    <?php  
    // Load MySQL credentials securely  
    require_once '/var/www/login.php';  
  
    // Enable detailed MySQL error reporting  
    mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);  
  
    // Establish database connection  
    $conn = new mysqli($db_hostname, $db_username, $db_password, $db_database);  
  
    if ($conn->connect_error) {  
        die("Connection failed: " . $conn->connect_error);  
    }  
  
    echo "<h2>Query 1: Retrieving Publisher and Author Data</h2>";  
  
    // Query using prepared statement  
    $stmt = $conn->prepare("SELECT publisher, author FROM books");  
    $stmt->execute();  
    $result = $stmt->get_result();  
  
    while ($row = $result->fetch_assoc()) {  
        echo "<p>Publisher " . htmlspecialchars($row["publisher"]) .  
             " published a book by " . htmlspecialchars($row["author"]) . ".</p>";  
    }  
  
    $stmt->close();  
  
    echo "<h2>Query 2: Retrieving Author, Title, and Date Published Data</h2>";  
  
    $stmt2 = $conn->prepare("SELECT author, title, copyright FROM books");  
    $stmt2->execute();  
    $result2 = $stmt2->get_result();  
  
    while ($row = $result2->fetch_assoc()) {  
        echo "<p>A book by " . htmlspecialchars($row["author"]) .  
             " titled <em>" . htmlspecialchars($row["title"]) .  
             "</em> was released in " . htmlspecialchars($row["copyright"]) . ".</p>";  
    }  
  
    $stmt2->close();  
    $conn->close();  
    ?>  
  
</body>  
</html>  
```
  
I then saved the file and exited out of nano.  

Finally, I tested the PHP syntax. All was fine as HTML was outputted:  

```  
sudo php -f /var/www/login.php  
sudo php -f /var/www/html/opac.php  
```
  
I viewed the site from my browser by opening the public IP address for my server. It showed me the data in your opacdb database and books table rendered in my webpage.  
  


**Results:**  
For the most part, everything worked as the instructions said it should. However, I had a major issue pop up when I went to make the php-MySQL connection.  The machine kept on telling me I was out of space on my virtual disk. I went through quite a bit of investigating, removing files from caches, and checking one thing after another that I found guidance from the my online investigations telling me to check.

Nothing worked.

Finally, I uninstalled MySQL and started over from the nbeginning, exactly as before. The second time through, everything worked. I have no idea why.

**Reflection:**  
Another project done and a lot of this is becoming a bit more reflective.

**Conclusion:**  
I hope I never run into the same error as I did this time around, because I have no idea why it popped up, or any idead how to directly fix it without starting over again. Very aggravating.


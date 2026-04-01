## 2026-03-31 – Creating a Bare Bones OPAC and Cataloguing Module
**Goal:** Learn commands and usage surrounding the installation Bare Bones OPAC and Cataloguing Module  
**Context:** Meet the listed PASS requirements for the related assignment  
  
**Steps:**  

As with all our projects, I started by making sure my machine was fully updated by running through the necessary code in my BASH shell:    

```
sudo apt update  
sudo apt upgrade  
sudo apt autoremove  
sudo apt clean  
```

Diving in to the next steps, I first had to update the Books table to use the proper data type for Date so we could filter date-wise across the table entries.  So, 

```
mysql -u opacuser -p
  
mysql> use opacdb;
mysql> alter table books add publication_date date;
mysql> update books set publication_date = str_to_date(concat(copyright, '-01-01'), '%Y-%m-%d');
mysql> alter table books drop column copyright;
mysql> alter table books change publication_date copyright date not null;
```

With that done, I had to create the HTML page for the Search interface and install the PHP script for actual search function. This involved going to the correct Directory, opening
the nano text editor and then adding the proper code:  

```
cd /var/www/html/
sudo nano mylibrary.html
```

followed by:  
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>MySQL Server Example</title>
    </head>
<body>

    <h1>A Basic OPAC</h1>

    <p>In the form below, <b>optionally</b> enter text in the search field.
    Your search query will search by author, title, or publisher.
    Capitalization is usually not necessary on default case-insensitive MySQL collations.
    It's okay to enter partial information, like part of an author's, title's, or publisher's name.</p>

    <p>You can leave the search field empty and only enter dates.
    Regardless, both start and end dates are required for all searches.
    You can use the date fields to limit results, too.
    I added some extra records, which you can view to know what you can query:</p>

    <p><a href="opac.php">OPAC</a></p>

    <p>This is very much a toy, stripped down
    <a href="https://en.wikipedia.org/wiki/Online_public_access_catalog">OPAC</a>.
    The records are basic.
    Not only do they not conform to <a href="https://www.loc.gov/marc/">MARC</a>,
    they don't even conform to something as simple as <a href="https://www.dublincore.org/">Dublin Core</a>.</p>

    <p>I also don't provide options to select different fields, like author, title, or publisher fields.
    Instead the search field below searches key bibliographic fields (author, title, publisher) in our <b>books</b> table.</p>

    <p>The key idea is to get a sense of how an OPAC works, though.</p>

    <h2>My Basic Library OPAC</h2>

    <form method="post" action="search.php">
        <label for="search">Search Terms (optional):</label>
        <input type="text" name="search" id="search">
        
        <br>
        
        <label for="start_date">Start Date:</label>
        <input type="date" name="start_date" id="start_date" required>
        
        <br>
        
        <label for="end_date">End Date:</label>
        <input type="date" name="end_date" id="end_date" required>
        
        <br>
        
        <input type="submit" value="Search">
    </form>

</body>
</html>
```

Save and exit.  


Still in the right directory, I opened nano again to create the PHP script:  
  
```  
sudo nano search.php    
```  

followed by:  

```  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Search Results</title>
<style>
    table {
        border-collapse: collapse;
        width: 100%;
    }
    th, td {
        border: 1px solid black;
        padding: 8px;
        text-align: left;
    }
</style>
</head>
<body>

    <h1>Search Results</h1>

    <?php
    // Load MySQL credentials
    require_once '/var/www/login.php';

    // Enable MySQL error reporting
    mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);

    // Establish connection
    $conn = new mysqli($db_hostname, $db_username, $db_password, $db_database);
    if ($conn->connect_error) {
        die("Connection failed: " . $conn->connect_error);
    }

    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $search = trim($_POST['search']);
        $start_date = $_POST['start_date'];
        $end_date = $_POST['end_date'];

        // Prepared statement to prevent SQL injection
        $stmt = $conn->prepare("SELECT id, author, title, publisher, copyright FROM books 
                                WHERE (author LIKE ? OR title LIKE ? OR publisher LIKE ?) 
                                AND copyright BETWEEN ? AND ?");

        // Use wildcard search
        $search_param = "%$search%";
        $stmt->bind_param("sssss", $search_param, $search_param, $search_param, $start_date, $end_date);
        $stmt->execute();
        $result = $stmt->get_result();

        if ($result->num_rows > 0) {
            echo "<table>";
            echo "<tr><th>ID</th><th>Author</th><th>Title</th><th>Publisher</th><th>Copyright</th></tr>";

            while ($row = $result->fetch_assoc()) {
                echo "<tr>";
                echo "<td>" . htmlspecialchars($row["id"]) . "</td>";
                echo "<td>" . htmlspecialchars($row["author"]) . "</td>";
                echo "<td>" . htmlspecialchars($row["title"]) . "</td>";
                echo "<td>" . htmlspecialchars($row["publisher"]) . "</td>";
                echo "<td>" . htmlspecialchars($row["copyright"]) . "</td>";
                echo "</tr>";
            }

            echo "</table>";
        } else {
            echo "<p>No results found.</p>";
        }

        $stmt->close();
    }

    $conn->close();
    ?>

    <p><a href="mylibrary.html">Return to search page</a></p>

</body>
</html>

```  

Still folllowing class documentation, I went back into the Books table and made some modifications:  

```  
mysql -u opacuser -p  
  
use opacdb;  
  
insert into books  
(author, title, publisher, copyright) values  
('Emma Donoghue', 'Room', 'Little, Brown & Company', '2010-01-01'),  
('Zadie Smith', 'White Teeth', 'Hamish Hamilton', '2000-01-01');  
  
```

### Then it was time for the Cataloging module.   
I had to create a new directory called "Cataloguing" and then create an associated HTML page in that Directory
called "index.html" for the user interface.  

```  
cd /var/www/html  
sudo mkdir cataloging  
```  
  
```  
cd cataloging  
sudo nano index.html  
```
    
```
<!DOCTYPE html>
<html>
<head>
    <title>Enter Records</title>
</head>
<body>
    <h1>OPAC Library Administration</h1>

    <p>This is the library administration page for entering records into the OPAC.</p>
    <p>Please do not use this page unless you are an authorized cataloger.</p>

    <form action="insert.php" method="post">
        <label for="author">Author:</label>
        <input type="text" name="author" id="author" required><br><br>

        <label for="title">Book Title:</label>
        <input type="text" name="title" id="title" required><br><br>

        <label for="publisher">Publisher:</label>
        <input type="text" name="publisher" id="publisher" required><br><br>

        <label for="copyright">Copyright:</label>
        <input type="date" name="copyright" id="copyright" required>

        <input type="submit" value="Submit">
    </form>
</body>
</html>
```  

And our PHP script:  

```
sudo nano insert.php
```


 ```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cataloging: Data Entry</title>
</head>
<body>

<h1>Cataloging: Data Entry</h1>

<?php

// Load MySQL credentials
require_once '/var/www/login.php';

// Enable MySQL error reporting
mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);

// Establish connection
$conn = new mysqli($db_hostname, $db_username, $db_password, $db_database);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    $author = trim($_POST["author"] ?? "");
    $title = trim($_POST["title"] ?? "");
    $publisher = trim($_POST["publisher"] ?? "");
    $copyright = $_POST["copyright"] ?? "";

    if ($author === "" || $title === "" || $publisher === "" || $copyright === "") {
        echo "All fields are required.";
    } elseif (!preg_match('/^\d{4}-\d{2}-\d{2}$/', $copyright)) {
        echo "Copyright date must use YYYY-MM-DD format.";
    } else {
        // Prepare and bind SQL statement
        $stmt = $conn->prepare("INSERT INTO books (author, title, publisher, copyright) VALUES (?, ?, ?, ?)");
        $stmt->bind_param("ssss", $author, $title, $publisher, $copyright);

        if ($stmt->execute() === TRUE) {
            echo "New record created successfully";
        } else {
            echo "Error: " . $stmt->error;
        }
        $stmt->close();
    }
} else {
    echo "Please submit records using the cataloging form.";
}

// Close connection
$conn->close();
?>

<p><a href='index.html'>Return to Cataloging Page</a></p>
<p><a href='../mylibrary.html'>Return to Library Home Page</a></p>
</body>
</html>
```

Save and exit nano.  


Now it was time for some security, so I set up some authentication criteria.    

First, create an authentication file in my /etc/apache2 directory, which is where the Apache2 web server stores its configuration files. 
Set the username and password both to libcat because thi si just training wheels and I'm both lazy and boring.  

```    
sudo htpasswd -c /etc/apache2/.htpasswd libcat
```   

Then tell the Apache2 web server that I will use the htpasswd to control access to my cataloging module. I use a text editor to open the apache2.conf file.

```    
sudo nano /etc/apache2/apache2.conf
```


In the apache2.conf file, I add a more specific directory stanza for this module. I put this stanza directly below the existing **<Directory /var/www/>** block. 
This keeps overrides limited to the cataloging directory rather than enabling them for all of **/var/www/**.  
  
```  
<Directory /var/www/html/cataloging/>   
  Options Indexes FollowSymLinks   
  AllowOverride AuthConfig  
  Require all granted  
</Directory>  
```

Switch to the cataloging directory and use nano to create a file called .htaccess (*paying careful attention to the leading period in the file name*):  

```  
cd /var/www/html/cataloging  
sudo nano .htaccess  
```  
  
The add the following content to .htaccess:  
    
```  
AuthType Basic  
AuthName "Authorization Required"  
AuthUserFile /etc/apache2/.htpasswd  
Require valid-user
```  
  
I check that the configuration file is okay:  `sudo apachectl configtest`  

I see the "Syntax OK" message I want to see and then restart Apache2 and check its status:  

```  
sudo systemctl restart apache2  
sudo systemctl status apache2
```

Again, I follow class material documentation and with a little searching (Hi, grep!) learn that APache has an account name on the server — that account name is www-data, and 
it's account details are stored in the **/etc/passwd file**:  

```  
grep "www-data" /etc/passwd
```

Specifcally:  *www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin*  

Because Apache2 has a user account, we can limit file permissions and ownership to this user.  I initiate these guidelines with the chown and chmod commands:  

```  
sudo chown :www-data /var/www/html  
sudo find /var/www/html -type d -exec chmod g+s {} +
```
  
### And Finally I test  
  
I visit my cataloging module in my web browser using my server's IP address:  
  
[My Library Page](http://34.41.8.169/mylibrary.html)   

And I  also test the PHP page directly:  
  
[My Record Entry Page](http://34.41.8.169/cataloging/index.html)  

    




  


  
    

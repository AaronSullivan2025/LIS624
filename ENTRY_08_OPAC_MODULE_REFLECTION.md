## 2026-04-01 – Basic OPAC and Cataloging Module Documentation


OPAC stands for *Online Public Access Catalog* and it is a web-based interface allowing search-enabled access to a database containing information on a library’s entire holdings, both physical and digital. 
In the modern era, OPACs replace physical card catalogs and enable patrons 24-7 access to a library’s inventory and lending systems. It’s a crucial component in an **Integrated Library System** and connects 
to related systems handling cataloging and circulation.  OPACs rely on Relational Databases to handle the library’s bibliographic records. These databases reorganize complex data as entries in related 
tables connected via specific entries acting as keys between the tables, such that a single entry on one table relates to the primary entry of separate table’s records; this allows hierarchical data to 
be sorted, repeated data to be minimized (preferably eliminated altogether) by how the keys and table-to-table connections are normalized, and consistent information schemas to speed up efficient searching.  

  
The code we’ve used in this class has helped each of us set up a web-server through Google Cloud using Apache, install PHP methods to interact with that server, and establish a MySQL database for Apache to 
house and PHP to carry commands through to and output from back to HTML-derived web pages on our browser. Further, our web server now hosts web pages that allow us run PHP scripts to interact with the database 
we’ve built and started storing barebones bibliographic records in. The various PHP scripts we’ve set up take input from the HTML web pages we’ve added and inserts that data into SQL commands that either filter 
through previously entered records for matching criteria to return data regarding the target record, or that insert new records into the tables we’ve previously built.  

This structure is extremely limited, obviously. In a real -world construction, we would have a much prettier and professional looking front end set of web page schemas across an entire dedicated site. This would 
link to a vastly expanded set of databases designed around established cataloging and bibliographic protocols (MARC, Dublin Core, etc.) so that it reflected academic/industry/institutional standards, expectations, 
data integrity practices, and security structures; the system could then safely “talk” to other systems at similar facilities (other libraries, vendors, etc.), reliably and routinely update records in accordance 
with standardized procedures, and safely perform all the many associated tasks we would expect from a full LMS’s digital components.   

As with any digital set up, attention to detail is crucial. In building our barebones OPAC, we’ve had to make sure each component was configured to interact properly with each of the other components. That means 
the right IP observance, of course, but also verifying each Linux update, verifying each Apache addition or change, tracking database/table/user naming so that privileges matched up or that whatever criteria looked 
for specific names or data types actually found those names and data types without error.  

As this was my first time using many of these tools, I leaned HEAVILY on the documentation supplied by the instructor, keeping detailed notes that I reviewed regularly and repeatedly. When given code (HTML, PHP, SQL), 
I looked carefully at each section to make sure I understood what the code was doing, how it was doing it, and why it was doing it. The HTML and SQL were already familiar to me, but the PHP and Linux I constantly took 
to the internet to research whatever wasn’t already explained to me in the class materials. Thankfully, to this point I haven’t encountered any issues with the code or the documents regarding it. Unfortunately, the same 
can’t be said for Google. My few problems in this class thus far have been entirely with the Google Cloud instantiation of the Virtual Machine we’re using to house our web servers. I’ve had to work through substantial 
online documentation from Google and third-party troubleshooting websites in order to make the cloud VM stay accessible for me.    


      

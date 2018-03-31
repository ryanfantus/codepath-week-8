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

# codepath-week-8
Facebook Codepath CyberSecurity coursework

# Project 8 - Pentesting Live Targets

Time spent: **X** hours spent in total

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
It's worth mentioning that all sites seem to be vulnerable to this.
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

Vulnerability #1: __________________

Vulnerability #2: __________________


## Red

Vulnerability #1: IDOR (Insecure Direct Object Reference)
Steps to reproduce:
* Open "red" Globitek site
* Click the "Find a Salesperson" link
* Notice that the salesperson links all end in `<url>/salesperson.php?id=X` with X being a number from 1-9
* Change this number to 10 and reveal an account that should not be made public
<img src="Red 1 - IDOR.gif" width="800">

Vulnerability #2: __________________


## Notes

Describe any challenges encountered while doing the work

# Investigating code security with Copilot 
Material for the Microsoft Reactor event centred on Copilot and security

## Workshop Files

This repository contains two example files:

1. The vuln.ini file - an example of a .ini file containing secrets
2. A vuln.js file - a file with examples of OWASP Top 10 style vulnerabilities 


## Detecting OWASp Top 10 Issues

When we select all of the code and ask Copilot:

```console
List the security vulnerabilities in this file
```

We see a response similar to:

```console
The code provided contains several security vulnerabilities, each of which is commented with the corresponding OWASP Top 10 category. Here's a list of them:

A2:2021 – Cryptographic Failures: The User class stores passwords in plain text. This is a major security risk as anyone with access to the database can see the passwords.

A5:2021 – Security Misconfiguration: The User class has an insecure default configuration. It provides a static method to get a default user with a weak password.

A3:2021 – Injection: The BlogPost class is vulnerable to SQL Injection. It constructs an SQL query using string concatenation with user-provided input, which can be manipulated to execute arbitrary SQL commands.

A1:2021 – Broken Access Control: The Account class allows any user to change any password without verifying the current user's permission. This can lead to unauthorized access.

A7:2021 – Identification and Authentication Failures: The authenticateUser function uses a weak authentication mechanism. It only checks if the username and password are both 'admin', which is a very weak authentication check.

Insecure Direct Object References (IDOR): The changePassword function in the Account class allows changing the password of any user, which can lead to unauthorized access.

Insecure Data Exposure: The authenticateUser function logs the username and password when authentication fails. This can expose sensitive information in logs.

Insecure use of debugger: The authenticateUser function uses the debugger statement. This can expose sensitive information during debugging sessions.

Insecure Network Communication: The startApp function starts a server that simply echoes back whatever it receives. This can expose sensitive information if used in a production environment.

Insecure Cookie Handling: The authenticateUser function sets a cookie with the username when authentication is successful. This can expose the username to anyone who can read the cookies.
```

 


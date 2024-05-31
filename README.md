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

 
## Ignore files

Let's ask Copilot:

```console
Why would it be a mistake to commit this file?
```

Copilot should say something like:

```console

Committing this file to a public repository would be a mistake due to several reasons:

Sensitive Data Exposure: The file contains sensitive data such as database credentials (username and password), API keys, and server configuration details. If this file is committed to a public repository, anyone can access these details, leading to unauthorized access and potential data breaches.

Security Misconfiguration: The file contains several insecure configurations such as running the server as a superuser, enabling debug mode, and allowing anonymous access to API endpoints. These configurations can be exploited by attackers to gain unauthorized access or perform malicious actions.

Broken Authentication: The file contains weak session management configurations, which can be exploited for session hijacking.

Injection: The file allows unrestricted file uploads, which can lead to arbitrary code execution if an attacker uploads a malicious file.

Denial of Service (DoS): The file does not limit the log file size, which can lead to a DoS attack by filling up the disk space.

Broken Access Control: The file exposes sensitive API endpoints without proper authentication, which can lead to unauthorized access.

In general, it's a best practice to never commit sensitive data or insecure configurations to version control. Instead, use environment variables or secure secret management systems to handle sensitive data.

```

OK, let's ask it what a .gitignore file is

```console
What is a .gitignore file and how can it help prevent this file being committed to GitHub
```

OK great example from Copilot

```console
A .gitignore file is a text file that tells Git which files or folders to ignore in a project. It is typically placed at the root of your project directory. Each line in this file specifies a pattern. When Git sees a file that matches one of these patterns, it ignores it. This means it won't be included in commits or tracked by Git.

In the case of the vuln.ini file, you can add its path to the .gitignore file to prevent it from being committed to GitHub. This is especially important for files that contain sensitive information, such as passwords, API keys, or other secrets.

Here's an example of how to add vuln.ini to a .gitignore file:

# Ignore the vuln.ini file
ini
If the vuln.ini file is in a subdirectory, you would include the path to the file from the root of your project:

Remember, once a file is committed, it becomes part of the Git history and removing it later can be difficult. Therefore, it's best to add sensitive files to .gitignore before making any commits.
```

Open the directory. Add a new file.

Open the inline edit and ask Copilot

```console
Add a rule to exclude vuln.ini
```

SImple!

```python
# Exclude vuln.ini
vuln.ini
```





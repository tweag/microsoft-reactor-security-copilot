# Investigating code security with Copilot 
Material for the Microsoft Reactor event centred on Copilot and security

## Workshop Files

This repository contains three example files:

1. The vuln.ini file - an example of a .ini file containing secrets
2. A vuln.js file - a file with examples of OWASP Top 10 style vulnerabilities 
3. Finally the vuln.py file - used to demonstrate Autofix

In order to work through this workshop you can open the code in either CodeSpaces, or clone the repository locally and open up the code in your Copilot supported IDE.

## Introduction 

The following Reeactor workshop demonstrates how Copilot had been used to detect and fix security issues in example code bases.


## Detecting OWASP Top 10 Issues

We will now demonstrate how we can detect issues in the `vuln.js` file. 

Open up the cloned repository in your IDE, or in CodeSpaces. Next, select the `vun.js` file.


Now select/highlight all of the code and ask Copilot chat the following:

```console
List the security vulnerabilities in this file
```

You should see a response similar to the following:

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

So here we see some examples of both OWASP Top 10 issues (the ones we added comments to), plus other issues it detected. Copilot can actually help us to remediate these issues. In many ways we can use Copilot like an ad-hoc SAST scanner in our IDE.

Also, if we happened to be writing code Copilot has another benefit. Code it suggests shouldn't contain known insecure coding patterns. For example, it shouldn't recommend code that is vulnerable to an SQL injection attack! 

So let's look at how we can fix these issues now. 


## Remediating the Issue

We can now use Copilot to remediate the vulnerablilities in the code base, using a variety of features ranging from the `/fix` slash command to asking simple questions. 

Additionally as we use the Chat window, you may notice that Copilot will sugegst addiitonal quesitons. For example, Copilot may have asked you if you are curious on how to fix the SQL injection issue. A potential further quesiton you may see posed is:

```console
Can you explain how to prevent SQL Injection in JavaScript?
```

If you see this question click it. If not, you can paste it into Copilot chat.

You should see an extensive explanation on how to resolve this issue. Here is a snippet of what I saw when designing the talk:

```console
Sure, to prevent SQL Injection in JavaScript, you should use parameterized queries or prepared statements. This ensures that the data passed into your SQL statements are treated strictly as data and not part of the SQL command.

In the provided code, the saveToDatabase method is vulnerable to SQL Injection because it directly concatenates user-provided strings into an SQL query. Here's how you can fix it:
```

```javascript
// Assuming you're using a library like 'pg' for PostgreSQL
const { Client } = require('pg');
const client = new Client();

// Connect to your database
client.connect();

class BlogPost {
    constructor(title, content, author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }

    // A3:2021 – Injection
    // Now safe from SQL Injection
    static async saveToDatabase(blogPost) {
        const text = 'INSERT INTO blog_posts (title, content, author) VALUES ($1, $2, $3)';
        const values = [blogPost.title, blogPost.content, blogPost.author];

        // Parameterized query
        await client.query(text, values);

        console.log("Submission successful");
    }
}
```

```console
In this revised code, $1, $2, and $3 are placeholders for the actual values. The client.query function automatically escapes these values to prevent SQL Injection.
```

So if we wanted to, we could use this code as a starting point to refactor our JavaScript to be more secure. 

We can now try asking Copilot using inline chat how to fix the constructor. Select the constructor code, as ask it:

```console
How do I fix th security issues here
```

Copilot will now make a recommendation. You may notice, it is actually leveraging the fix functionality. The result I see is:

```console
To fix the security issues in the code, I would replace the plain text password storage with a secure hashing algorithm and implement proper input validation and sanitization to prevent SQL injection attacks.
```
So this is a pretty good starting point. We can `Accept` the fix as well if we want. 

Let's move onto the the broken access control statement and ask Copilot how to fix it. Select the whole of the `Account` class. And use the inline editor:

```console
How would i refactor this to be more secure?
```

Copilot will now come back with a suggestion. We can check this:

```javascript
class Account {
    constructor(user) {
        this.user = user;
    }

    // A1:2021 – Broken Access Control
    // Method allows any user to change any password
    changePassword(username, newPassword) {
        // Logic to change password without verifying the current user's permission
        if (this.user.username === username) {
            this.user.password = newPassword;
            console.log(`Password for ${username} changed successfully`);
        } else {
            console.log(`Failed to change password. Invalid username: ${username}`);
        }
    }
}
```

Ok so this was very much a toy function and the result might not be architectural the best approach in the grand scheme of things. We'd really want to add RBAC in here. You can `Accept` the change, and then in the Copilot chat window ask:

```console
How could I implement RBAC?
```

Copilot is now going to provide us with some basic steps and thoughts on how we could approach this. Pretty clever right!

We have a stray function called `authenticateUser`, let's see what Copilot thiks about this. Open the inline menu and select the `Explain this` menu option.

Here is a synopsis of what I see:

```console
The provided JavaScript function, authenticateUser, is designed to authenticate a user based on a username and password. It's a simple function that checks if the provided username and password match a hardcoded set of credentials: 'admin' for both username and password.
```

Ok with the function still highlighted, select the `/fix` option from the menu. It will now recommend we remove things such as the `debugger` statement e.g.

```console
GitHub Copilot
To fix the code, I would add the missing import statement for the Cookies module and remove the unnecessary Debug.write and debugger statements.
```

So we've just seen some simple examples there of how we can fix issues, let's move onto `.gitignore` files. 

## Ignore files

We can use .gitignore files to prevent commiting `.ini` and other configuration files to the Source Control management system (SCM). Let's open up the `vuln.ini` and ask Copilot about the configuration file:

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

OK, let's ask it what a `.gitignore` file is in the Chat window:

```console
What is a .gitignore file and how can it help prevent this file being committed to GitHub
```

Copilot should now give you a response similar to:

```console
A .gitignore file is a text file that tells Git which files or folders to ignore in a project. It is typically placed at the root of your project directory. Each line in this file specifies a pattern. When Git sees a file that matches one of these patterns, it ignores it. This means it won't be included in commits or tracked by Git.

In the case of the vuln.ini file, you can add its path to the .gitignore file to prevent it from being committed to GitHub. This is especially important for files that contain sensitive information, such as passwords, API keys, or other secrets.

Here's an example of how to add the `vuln.ini` to a `.gitignore` file:

# Ignore the vuln.ini file

If the vuln.ini file is in a subdirectory, you would include the path to the file from the root of your project:

Remember, once a file is committed, it becomes part of the Git history and removing it later can be difficult. Therefore, it's best to add sensitive files to .gitignore before making any commits.
```

Let's give thid a try then. Open the directory with the source code in the IDE and add a new file. Let's call this `.gitignore`

Open the inline editor and ask Copilot the following: 

```console
Add a rule to exclude vuln.ini
```

Copilot will now suggest some code e.g. 

```python
# Exclude vuln.ini
vuln.ini
```

We could now rotate the secrets in our existing `.ini` file. Delete it/purge it from the repo, and then moving forward thse `.ini` files will not be committed. 

So this was a nice simple approach. It's not bullet proof, but it's a layer of defense we can use!

## GHAS and Copilot

Before we wrap up, let's take a look at one of GitHub's newest features - Autofix.

Autofix combines the power of GHAS and OpenAI to detect security issues and recommend fixes, which are turned into pull requests.

In order to complete this part of the workshop your company must have GHAS licenses and you should have the ability to switch on CodeQL and Autofix on your repository.

You can check if it is enabled via the `Settings > Cod`e security and analysis screen under the Code scanning section.

Select the dropdown next to CodeQL analysis and select the Advanced option to add the CodeQL configuration file to the repository.

On the screen that appears, commit the new file to the repository.

Return back to the previous screen, and then check that Autofix for CodeQL (Beta) is enabled.

Now we can see Autofix in action. Give CodeQL a few moments to create run the Actions workflow after committing the file. You can check progress on the Actions tab in GitHub. Let's edit our code to see AutoFix in action.

If you look at the following block of code we can see Debug is set to False:

```python
app.run(debug=False)
```
Change this to `True`:

```python
app.run(debug=True)
```

Commit this, push the code and create a pull request. 

Using the above as an example, in the pull request you should see:

```console
The problem with the code is that the Flask application is being run in debug mode, which is not recommended for production environments as it may allow an attacker to run arbitrary code through the debugger.

To fix this issue, we need to ensure that the Flask application is not run in debug mode when in a production environment. This can be achieved by setting the debug mode based on an environment variable.

We will need to import the os module to access environment variables. Then, we will use os.getenv to get the value of an environment variable named FLASK_DEBUG. If this variable is set to '1', the application will run in debug mode; otherwise, it will not.
```

You can choose to accept the suggestion provided by Autofix, edit in Codespaces, fix locally and push the change, or dismiss the finding.

That completes our Autofix example.


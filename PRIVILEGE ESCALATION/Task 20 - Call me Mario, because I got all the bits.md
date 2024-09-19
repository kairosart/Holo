**Note:** Please be mindful of other users trying to proceed in the network. Please do not stop the Docker container from running. It will prevent users from proceeding throughout the network. Also, please clean up after yourself. If you transfer a docker container image to the VM, remember to remove it after you finish elevating privileges.

![[Task 20 - Call me Mario, because I got all the bits-20240919143826003.webp]]

Now that we have a shell on L-SRV01 and escaped the container, we need to perform local privilege escalation to gain root on the box.  

Local privilege escalation is when you take your average level user access and exploit misconfigurations and applications to gain privileged level access. This is typically done by exploiting a specific application or service that was misconfigured on the device.  

Several resources can help you through privilege escalation on Linux. Some of these resources are outlined below for you to use.  

- [](https://book.hacktricks.xyz/linux-unix/privilege-escalation)[https://book.hacktricks.xyz/linux-unix/privilege-escalation](https://book.hacktricks.xyz/linux-unix/privilege-escalation)
- [](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)[https://github.com/swisskyrepo/PayloadsAllTheThings/](https://github.com/swisskyrepo/PayloadsAllTheThings/)  
    
- [](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)[https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)

In this task, we will be covering one specific privilege escalation technique and a script that we can use to speed along the process of finding misconfigurations we can exploit.  

We will utilize a script called Linpeas to run a thorough check of potential exploits to begin our privilege escalation attempts. [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite).  

To use Linpeas, we need to download the script from the repository above on our attacking machine. Then we can utilize a web hosting service such as http.server, updog, or php to host the file onto the target machine. Linpeas does not require any arguments or parameters to run; you only need to run it as a standard binary.  

Syntax: `./linpeas.sh`

Linpeas may take around 5-10 minutes to complete. Once complete, you would need to parse through the output and look for any potentially valuable information.  

The specific exploit that we will be looking at is abusing the SUID bit set on binaries. From [linux.com](http://linux.com/) *"SUID (Set owner User ID upon execution) is a special type of file permissions given to a file. Normally in Linux/Unix when a program runs, it inherits access permissions from the logged-in user. SUID is defined as giving temporary permissions to a user to run a program/file with the permissions of the file owner rather than the user who runs it."* This means that if the program or file is running as root and we have access to it, we can abuse it to grant us root-level access. Below you can find what the SUID bit looks like, along with a table of other bits that can be set.  

![[Task 20 - Call me Mario, because I got all the bits-20240919143651873.webp]]

|   |   |   |
|---|---|---|
|**Permission**|**On Files**|**On Directories**|
|SUID Bit|User executes the file with permissions of the _file_ owner|-|
|SGID Bit|User executes the file with the permission of the _group_ owner.|File created in directory gets the same group owner.|
|Sticky Bit|No meaning|Users are prevented from deleting files from other users.|

Besides using Linpeas to find the files with a SUID bit, you can also use a bash one-liner shown below to search for files with this bit set.  

Command: `find / -perm -u=s -type f 2>/dev/null`

Once we have identified a file that we thank may be exploitable, we need to search for an exploit for it. A helpful resource to search for exploits on specific applications and programs is GTFOBins, [https://gtfobins.github.io/](https://gtfobins.github.io/).  

An example of an exploit can be found below for a dig SUID. Exploits may vary between each application and vulnerability as each has its unique ways security researchers have found they can be abused.  

![[Task 20 - Call me Mario, because I got all the bits-20240919143733244.webp]]

If successful, you should now have the same permission levels as the binary you exploited.
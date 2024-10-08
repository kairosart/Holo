Before we get too overzealous in attacking web servers and hacking the world, we need to identify our scope and perform some initial recon to identify assets. Your trusted agent has informed you that the scope of the engagement is 10.200.x.0/24 and 192.168.100.0/24. To begin the assessment, you can scan the ranges provided and identify any public-facing infrastructure to obtain a foothold.  

Nmap is a commonly used port scanning tool that is an industry-standard that is fast, reliable, and comes with NSE scripts. Nmap also supports CIDR notation, so we can specify a /24 notation to scan 254 hosts. There are many various arguments and scripts that you can use along with Nmap; however, we will only be focusing on a few outlined below.  

- `sV` scans for service and version
- `sC` runs a script scan against open ports.
- `-p-` scans all ports 0 - 65535
- `-v` provides verbose output

Syntax: `nmap -sV -sC -p- -v 10.200.X.0/24`

Once you have identified open machines on the network and basic ports open, you can go back over the devices again individually with a more aggressive scan such as using the `-A` argument.  

For more information about Nmap, we suggest completing the [nmap](https://tryhackme.com/room/furthernmap) room on Tryhackme.

---

# Your job
## Scan 10.200.x.0/24

### Open machines

`nmap -sn -T4 -n 10.200.X.0/24`

![[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!-20240903151129988.webp]]

There are 2 hosts up:
**10.200.X.33 and 10.200.X.250**

### Scan 10.200.95.33

Run a full port scan to find the open ports.
`nmap -p- 10.200.X.33 -Pn -n -T5 --min-rate 1000`

![[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!-20240903151622432.webp]]


### Detail nmap

`nmap -sC -sV -T4 10.200.X.33 -p 22,80,33060 -oA webserver_nmap`

![[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!-20240904135705372.webp]]

So there are 3 ports open : SSH on 22, Http on 80 and mysql on 33060. The detailed nmap scan gives me a lot of info on them.

1. The SSH port version says the machine might run a Ubuntu Linux OS. Also the http port says it might be Ubuntu, so we are kinda sure that we are facing a Ubuntu Linux. which is a great fingerprinting.
2. HTTP port means there might be a webserver and nmap scan tells it’s running Apache webserver. So there must be a website.
3. Nmap fingerprinted that there’s wordpress version 5.5.3 running in port 80 which will help us finding vulnerabilities related to that.
4. The info about `robots.txt` file is also interesting as we see there are 21 disallowed entries. We can see them by visiting the `/robots.txt` directory.
5. The title of the page is `holo.live` which means we are on the right website as our client was named holo. I will make sure to add this name to our hosts file for name resolution.

## Visit the Web on port 80

![[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!-20240903152634588.webp]]



- Using Wappalizer plug-in you can see the webpage's technologies.

	![[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!-20240903152842205.webp]]

- View page source.

	![[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!-20240903153232338.webp]]


---
## Answer the questions below

> [!question]
> What is the last octet of the IP address of the public-facing web server?
> `33`
> How many ports are open on the web server?
> `3`
> What CME is running on port 80 of the web server?
> `WordPress`
> What version of the CME is running on port 80 of the web server?
> `5.5.3`
> What is the HTTP title of the web server?
> `holo.live`


**Next step:** [[Punk Rock 101 err Web App 101]]

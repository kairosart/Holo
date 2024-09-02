Before we get too overzealous in attacking web servers and hacking the world, we need to identify our scope and perform some initial recon to identify assets. Your trusted agent has informed you that the scope of the engagement is 10.200.x.0/24 and 192.168.100.0/24. To begin the assessment, you can scan the ranges provided and identify any public-facing infrastructure to obtain a foothold.  

Nmap is a commonly used port scanning tool that is an industry-standard that is fast, reliable, and comes with NSE scripts. Nmap also supports CIDR notation, so we can specify a /24 notation to scan 254 hosts. There are many various arguments and scripts that you can use along with Nmap; however, we will only be focusing on a few outlined below.  

- `sV` scans for service and version
- `sC` runs a script scan against open ports.
- `-p-` scans all ports 0 - 65535
- `-v` provides verbose output

Syntax: `nmap -sV -sC -p- -v 10.200.x.0/24`

Once you have identified open machines on the network and basic ports open, you can go back over the devices again individually with a more aggressive scan such as using the `-A` argument.  

For more information about Nmap, we suggest completing the [nmap](https://tryhackme.com/room/furthernmap) room on Tryhackme.
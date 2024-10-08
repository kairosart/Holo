After scanning the Network range, you discover a public-facing Web server. You take to your keyboard as you begin enumerating the Web Application's attack surface. Your target is L-SRV01 found from initial reconnaissance.

**Important Note: a large number of users have reported L-SRV01 is crashing. This is likely due to multiple people running Gobuster and WFuzz at once. It is highly recommended that you reduce the thread count while attempting file/directory enumeration on L-SRV01.**

Virtual Hosts or vhosts are a way of running multiple websites on one single server. They only require an additional header, Host, to tell the Web Server which vhost the traffic is destined; this is particularly useful when you only have one IP address but can add as many DNS entries as you would like. You will often see hosted services like Squarespace or WordPress do this.  

We can utilize Gobuster again to identify potential vhosts present on a web server. The syntax is comparable to fuzzing for directories and files; however, we will use the `vhosts` mode rather than `dir` this time. `-u` is the only argument that will need a minor adjustment from the previous fuzzing command. `-u` is the base URL that Gobuster will use to discover vhosts, so if you provide `-u` "[https://tryhackme.com](https://tryhackme.com/)" GoBuster will set the host to "[tryhackme.com](http://tryhackme.com/)" and set the host header to `Host: LINE1.tryhackme.com`. If you specify "[https://www.tryhackme.com](https://www.tryhackme.com/)", GoBuster will set the host to "[www.tryhackme.com](http://www.tryhackme.com/)" and the host header to `Host: LINE1.www.tryhackme.com`. Be careful that you don't make this mistake when fuzzing.  

Syntax: `gobuster vhost -u <URL to fuzz> -w <wordlist>`

We recommend using the Seclists "subdomains-top1million-110000.txt" wordlist for fuzzing vhosts.

Wfuzz also offers vhost fuzzing capability similar to its directory brute-forcing capability. The syntax is almost identical to the Gobuster syntax; however, you will need to specify the host header with the `FUZZ` parameter, similar to selecting the parameter when directory brute-forcing.  

Syntax: `wfuzz -u <URL> -w <wordlist> -H "Host: FUZZ.example.com" --hc <status codes to hide>`

Now that we have some vhosts to work off from fuzzing, we need a way to access them. If you're in an environment where there is no DNS server, you can add the IP address followed by the FQDN of the target hosts to your _/etc/hosts_ file on Linux or _C:\\Windows\\System32\\Drivers\\etc\\hosts_ file if you're on Windows.


---

# Your job

## Fuff

 Identify potential vhosts present on a web server.
 
 `ffuf -s -u http://10.200.X.33 -H "Host:FUZZ.holo.live" -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -fw 1288`

![[Punk Rock 101 err Web App 101-20240904141410874.webp]]

There are 3 results: dev, admin, www.

Add those subdomains to /etc/hosts.

`dev.holo.live admin.holo.live www.holo.live` 


---

## Answer the questions below

> [!question]
> What domains loads images on the first web page?
> `www.holo.live`
> What are the two other domains present on the web server? Format: Alphabetical Order
> `admin.holo.live, dev.holo.live`

**Next step:** [[Task 10 - What the Fuzz?]]
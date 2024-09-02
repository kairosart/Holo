Accessing the Network  

To access the network, you will need to first connect to our network using OpenVPN. Here is a mini walkthrough of connecting to the Holo-specific network.

  

(_Please note the browser-based machine will not be able to access these machines. If you want to use the browser-based machine, deploy it and run your OpenVPN configuration on the browser Kali machine)_

Answer the questions below

Go to your [access](https://tryhackme.com/r/access) page. Select ‘Holo’ from the VPN servers (under the network tab) and download your configuration file.

![[Screenshot from 2024-09-02 19-36-15.png]]  


---

Use an OpenVPN client to connect. This example shows the client on Linux. You can find similar guides for Windows or MacOS in the OpenVPN configuration section of the [access](https://tryhackme.com/r/access) page.

![[Screenshot from 2024-09-02 19-37-46.png]]

_Change "ben.ovpn" to your config file_


When you run this you see lots of text, at the end, it will say “Initialization Sequence Completed”.


---
Return to your [access](https://tryhackme.com/r/access) page. You can verify you are connected by looking on your access page. Refresh the page. You should see a green tick next to Connected. It will also show you your internal IP address.
  
![[Patching into the Matrix-20240902134308225.webp]]

You’re now ready to start hacking the Holo Network!

---

Alternatively, you can download your network OpenVPN configuration file (as shown in step 1), deploy a browser-based Kali machine from the top of this page by clicking the drop-down arrow next to the `Start AttackBox` button, and follow the steps on that Linux machine.

![[Patching into the Matrix-20240902134429697.webp]]

**Next step:** [[Task 3 - Kill Chain]]

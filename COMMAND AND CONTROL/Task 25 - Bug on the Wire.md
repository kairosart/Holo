Now that we have Covenant set up and signed in, we can begin covering the basics of operating and creating a listener with Covenant. This will be helpful later when you get onto a Windows box and deploy a grunt quickly.  

When operating with Covenant, there are four main stages: creating a listener, generating a stager, deploying a grunt, utilizing the grunt. All stages of operation can already be done using other tools like MSFVenom, Netcat, Metasploit, etc. however, Covenant gives you a way to operationalize them all under one platform allowing for easier management and collaborative operations.  

Covenant is an extensive and diverse command and control framework with many different functionalities. We will only be covering the basics of operating with Covenant. For more information, check out the SpecterOps blog, [https://posts.specterops.io/](https://posts.specterops.io/), and the SoCon talk on "Operating with Covenant" by Ryan Cobb and Justin Bui [https://www.youtube.com/watch?v=oN_0pPI6TYU](https://www.youtube.com/watch?v=oN_0pPI6TYU).  

The first step in operating with Covenant is to create a listener. Listeners are built off profiles; you can think of profiles like HTTP requests/pages that will serve as the channel that will handle all C2 traffic. There are four default profiles that Covenant comes with, outlined below.  

- `CustomHttpProfile` Custom profile that does not require any cookies.
- `DefaultBridgeProfile` Default profile for a C2 bridge.
- `DefaultHttpProfile` Default HTTP profile.
- `TCPBridgeProfile` Default TCP profile for a C2 bridge.

Covenant offers an easy way of editing the listeners along with a GUI. There are many parameters present; we will only be going over a quick overview of each parameter outlined below.  

- `Name` Name of profile to be used throughout the interface.
- `Description` Description of profile and its use cases.
- `MessageTransform` Specify how data will be transformed before being placed in other parameters. 
- `HttpUrls` list of URLs the grunt can callback to.
- `HttpRequestHeaders` List of header pairs (name/value) that will be sent with every HTTP request.
- `HttpResponseHeaders` List of header pairs (name/value) that will be sent with every HTTP response.
- `HttpPostRequest` Format of data when a grunt posts data back to the profile.
- `HttpGetResponse` HTTP response when a grunt GETs data to the listener.
- `HttpPostResponse` HTTP response when a grunt POSTs data to the listener.

We will be going further in-depth with editing and creating profiles in Task 26.  

Once you have decided what profile you would like to use, we can begin creating the listener. We recommend using the _DefaultHttpProfile_, to start with, but we will be changing this in later tasks when dealing with AV evasion.  

To create a listener, navigate to the _Listeners_ tab from the side menu and select _Create Listener_.  

You will see several options to edit; each option is outlined below.  

- `Name` (optional) will help to identify different listeners.
- `BindAddress` Local address listener will bind on, usually `0.0.0.0`.
- `BindPort` Local port listener will bind on.
- `ConnectPort` Port to callback to, suggested to set to `80`, `8080`, or `8888`.
- `ConnectAddresses` Addresses for the listener to callback to, hostname portion of the `URL`.
- `URLs` Callback URLs the grunt will be connected directly back to.
- `UseSSL` Determines whether or not the listener uses `HTTP` or `HTTPS`.
- `SSLCertificate` Certificate used by the listener if SSL is set to true.
- `SSLCertificatePassword` Password being used by the `SSLCertificate`.
- `HttpProfile` Profile used by the listener and grunt to determine communication behavior.

To create a basic listener for this network we only suggest editing the `Name`, `ConnectPort`, and `ConnectAddresses`  

Once created, the listener should appear within the Listeners tab. You can now start and stop the listener as needed.

![[Task 25 - Bug on the Wire-20241002141843944.webp]]


---

# Your job
Within Covenant and any C2 there are 4 main stages, they are named differently depending on the C2 but roughly mean the same thing:

- Creating a listener.
- Generating a stager or launcher.
- Geploying a grunt.
- Utilizing the grunt.

> A `Listener` is what listens on your box, the equivalent to multi/handler or `nc -nvlp 9999`.

> A `Stager` is also known as a launcher, this is the malicious code used to execute on the machine and gain that connection

> A `Grunt` is the name for the reverse connection, they are the “beacon” which is then used to run commands and tools on the remote machine.


## Create a listener

1. Click on the listener tab on the left hand side.
2. Use a `http` listener, it listens over HTTP to mimic regular web-traffic in an attempt to avoid detection.
3. Change the settings to the following:
	- Name – Whatever name you want, i.e. `Holo HTTP`.
	- BindAddress – Leave this as all interfaces
	- BindPort – Leave this as `80`
	- ConnectPort – This is the connection back from the remote host, you will need to change this to `53` (DNS) to gain a reverse shell on the webserver box.
	- ConnectAddress – This is the address, for this use the `tun0` address
	- Click create, and you now have a listener.


**Next step: ** [[Task 26 - The Blood Oath]]

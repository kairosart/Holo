From scanning the internal network, we know that the rest of the network is **Windows** hosts. When in an engagement, red teams will often utilize a C2 server as a base of operations to help operationalize payloads and maintain access using modules. We will be setting up our C2 server and getting familiar with its operations before moving on to attacking the rest of the network.

We can use a command and control server to organize users and deploy modules or tasks on a compromised device. Rather than using reverse shells and payloads, you can use a stager and listeners with a C2 server to help a red team through an engagement. Throughout this walkthrough, we will use the [Covenant](https://github.com/cobbr/Covenant), developed by Cobbr and the SpectreOps Team. If you prefer to use another C2 framework like Empire or Cobalt Strike, you can use them; however, the modules and stagers may be different than shown.

From the Covenant GitHub, "*Covenant is a .NET command and control framework that aims to highlight the attack surface of .NET, make the use of offensive .NET tradecraft easier, and serve as a collaborative command and control platform for red teamers.*"

For more information about Covenant, check out the Covenant GitHub wiki, [](https://github.com/cobbr/Covenant/wiki)[https://github.com/cobbr/Covenant/wiki](https://github.com/cobbr/Covenant/wiki)

The Covenant installation is relatively straightforward, with a few quirks and areas that may need troubleshooting. The installation requires two separate central installs: .NET Core SDK and downloading Covenant itself.  

To begin setting up Covenant, we will begin with installing the .NET Core SDK. Covenant requires .NET Core SDK 3.1.0. You can download the SDK from either the .NET downloads page or adding the .NET repositories and downloading via apt.

For more information about downloading via the downloads page, check out this link, [https://dotnet.microsoft.com/download/dotnet/3.1](https://dotnet.microsoft.com/download/dotnet/3.1).  

For more information about downloading via the repositories, check out this link, [https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu](https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu)  

Follow along with either of the methods and install .NET Core SDK 3.1.0. This will be the utility we use to build and run Covenant.  

Once you have the SDK installed, you can clone the Covenant repository from GitHub. Find an example below.  

Command used: `git clone --recurse-submodules https://github.com/cobbr/Covenant`

Since Covenant is written entirely in .NET Core, all dependencies are already handled when building with the SDK.  

Now that both the SDK and Covenant are installed, we can start up Covenant for the first time. Covenant will start on localhost port 7443. Find example syntax below.  

Command used: `sudo ./dotnet run --project /opt/Covenant/Covenant`

Once you navigate to [127.0.0.1:7443](http://127.0.0.1:7443/) you will be greeted with a user creation screen. Create a user and sign in to Covenant. Find an example of the sign-in page below.

![[Task 24 - Command your Foes and Control your Friends-20240927134255397.webp]]

If successfully signed in, you should be met with a dashboard like the one shown below.

![[Task 24 - Command your Foes and Control your Friends-20240927134346264.webp]]
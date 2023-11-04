# Election 1
Solving Vulnhub's Election 1 machine.

<div>
  <h2>Index</h2>
  <h5>1. Introduction<h5>
  <h5>2. Installation and setup</h5>
  <h5>3. Enumeration and recognition</h5>
  <h5>4. Vulnerability analysis</h5>
  <h5>5. Exploitation</h5>
  <h5>6. Conclusion</h5>
</div>

<div>
  <h2>1. Introduction</h2>
 This vulnerable Vulnhub machine presents similar difficulty to ejptv2 certification at the intrusion stage and similar difficulty to OSCP certification at the privilege escalation stage. Ideal to prepare for ejptv2 and OSCP certifications.
</div>

<div>
  <h2>2. Installation and setup</h2>
  
</div>

  First, we need to install the .7z file available [here](https://www.vulnhub.com/entry/election-1,503/) and extract it to get the .ova file. When we have the .ova file we just need to open it to import it into VirtualBox, VMware or your favourite platform.  Once imported we must make sure that the network adapter is in bridge mode. Now we can start with the enumeration phase.


  <h2>  3. Solving the machine</h2>
  The first thing to do is to identify the IP of the victim machine, for this we can play with "arp-scan --localnet --ignoredups", this will identify the IP addresses assigned in your local network (thanks to the --localnet parameter).<br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsarpscan.png"/> <br/>
  <br/>
  
  Once we have identified the IP address of the victim machine, we will launch a ping -c 1 "ip-victim-machine" to check if we receive a response, and also to identify the operating system of the victim machine thanks to the ttl.<br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/sceenshotsping.png"/> <br/>
  <br/>
  Well, now we know that the ttl of the victim machine is 64, so most likely the operating system of the victim machine is Linux. 
  The next step will be to scan the ports of the ip of the victim machine to detect the services that are running behind, for this we will use the nmap tool and indicate with -p- that we want to scan all existing ports (65535) on the ip address of the victim machine.

  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsnmap.png"/> <br/>
  <br/>

  We will also launch some basic reconnaissance scripts to detect the versions of the services that are running in addition to other data, for this we will use nmap again with the -sCV parameter on ports 22 and 80.
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsnmapscv.png"/> <br/> 
  <br/>
  If we check the version of the apache service running on port 80 (httpd 2.4.29) we discover that we are dealing with an Ubuntu Bionic. 

  The next step will be to launch the nmap http-enum script on port 80 to detect some existing web page routes <br/>.

  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsnmaphttpenum.png"/> <br/>
  <br/>
   This is how we discover the path "robots.txt", if we access it we can discover the following: 

  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsrobotstxt.png"/> <br/>
  <br/>
 We focus on the word "election", doing some research we discover that there is a path with the same name, if we access the path we will see a web page about a vote for a candidate.
  It's time to discover more routes inside election, for this we will use gobuster together with the Seclists 2.3 medium dictionary.
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotgobuster.png"/> <br/>
  <br/>
  We can see that it discovers several routes, but we will focus on the "admin" route, if we access it we will see a simple login. If we play again with the gobuster tool to discover new routes in the admin route.
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotgobusteradmin.png"/> <br/>
  <br/>
  Among the routes reported is the "logs" route that if we access it and download the "system.log" file inside we will see what appears to be a user and a password.
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotssystemcatlog.png"/> <br/>
  <br/>
  Let's remember the other port that was open on the victim machine, port 22 belonging to the ssh service, if we enter the user and password given in the "system.log" file we will be able to access the victim machine with the user "love".
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotssh.png"/><br/>
  <br/>

  Well, it is time to escalate privileges by becoming a privileged user(root), first we will search if there is any binary with the SUID permission granted with the find tool searching from the raid for permission 4000(SUID) 
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotfindsuid.png"/> <br/>
  </br>
  So we will see that there is a binary in the path /usr/local/Serv-U/Serv-U where the owner is root, therefore when executing this command with our current user (love) we will be executing it as if we were the root user.
  Researching about "Serv-U" you will discover that there are several exploits to escalate privileges, the script I used can be found [here](https://github.com/rsnchzl/election-1/blob/main/exploits/exploitprivilageescalation.c), developed in C language. Just copy the script on the victim machine and gcc it. <br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotexploit.png"/> <br/>
  </br>
  Now, you just have to run to move to the root user :)
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotexploitexecutation.png"/> <br/>
  <br/>



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

<div>
  <h2>  3. Enumeration and recognition</h2>
  The first thing to do is to identify the IP of the victim machine, for this we can play with "arp-scan --localnet --ignoredups", this will identify the IP addresses assigned in your local network (thanks to the --localnet parameter).
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsarpscan.png"/> <br/>
  <br/>
  
  Una vez tengamos identificado la dirección IP de la máquina victima lanzaremos un ping -c 1 "ip-maquina-victima" para comprobar si recibimos respuesta, y de paso identificar el sistema operativo de la máquina victima gracias al ttl.
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/sceenshotsping.png"/> <br/>
  <br/>
  Bien, ahora sabemos que el ttl de la máquina victima es 64, por lo tanto muy probablemente el sistema operativo de la máquina victima sea Linux.
</div>

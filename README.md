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


  <h2>  3. Enumeration and recognition</h2>
  The first thing to do is to identify the IP of the victim machine, for this we can play with "arp-scan --localnet --ignoredups", this will identify the IP addresses assigned in your local network (thanks to the --localnet parameter).<br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsarpscan.png"/> <br/>
  <br/>
  
  Una vez tengamos identificado la dirección IP de la máquina victima lanzaremos un ping -c 1 "ip-maquina-victima" para comprobar si recibimos respuesta, y de paso identificar el sistema operativo de la máquina victima gracias al ttl.<br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/sceenshotsping.png"/> <br/>
  <br/>
  Bien, ahora sabemos que el ttl de la máquina victima es 64, por lo tanto muy probablemente el sistema operativo de la máquina victima sea Linux. <br/>
  El siguiente paso será  escanear los puertos de la ip de la máquina victima para detectar los servicios que estan corriendo por detras, para ello usaremos la herramienta nmap y le indicaremos con -p- que queremos escanear todos los puertos existentes (65535) en la dirección ip de la máquina victima.

  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsnmap.png"/> <br/>
  <br/>
  Ahora sabemos que hay un servicio http corriendo por el puerto 80 y otro servicio ssh corriendo por el puerto 22. <br/>

  Tambien lanzaremos unos scripts básicos de reconocimiento para detectar las versiones de los servicios que estan corriendo ademas de otros datos, para ello usaremos de nuevo nmap con el parametro -sCV en los puertos 22 y 80.<br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsnmapscv.png"/> <br/> 
  <br/>
  Si comprobamos la versión del servicio apache que se esta ejecutando por el puerto 80 (httpd 2.4.29) descubrimos que estamos ante un Ubuntu Bionic. <br/>

  El siguiente paso sera lanzar el script http-enum de nmap sobre el puerto 80 para que detecte algunas rutas existentes de la página web <br/>
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsnmaphttpenum.png"/> <br/>
  <br/>
  Asi es como descubrimos la ruta "robots.txt", si accedemos a ella podremos descubrir lo siguiente: 
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotsrobotstxt.png"/> <br/>
  <br/>
  Nos centramos en la palabra "election", investigando un poco descubrimos que hay una ruta con su mismo nombre, si accedemos a la ruta veremos una página web sobre una votación hacia un candidato.
  Es hora de descubrir mas rutas dentro de election, para ello usaremos gobuster junto con el diccionario 2.3 medium de Seclists
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotgobuster.png"/> <br/>
  <br/>
  Podemos observar que nos descubre varias rutas, pero nos centraremos en la ruta "admin", si accedemos a ella veremos un simple login. Si jugamos de nuevo con la herramienta gobuster para descubrir nuevas rutas en la ruta admin.
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotgobusteradmin.png"/> <br/>
  <br/>
  Entre las rutas reportadas esta la ruta "logs" que si accedemos a ella y descargamos el archivo "system.log" en su interior veremos lo que parece ser un usuario y una contraseña
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotssystemcatlog.png"/> <br/>
  <br/>
  Recordemos el otro puerto que estaba abierto en la máquina victima, el puerto 22 perteneciente al servicio ssh, si introducimos el usuario y la contraseña otorgada en el archivo "system.log" podremos acceder a la máquina victima con el usuario "love".
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotssh.png"/><br/>
  <br/>

  Bien, es hora de escalar privilegios convirtiendonos en un usuario privilegiado(root), en primer lugar buscaremos si hay algun binario con el permiso SUID otorgado con la herramienta find buscando desde la raid por el permiso 4000(SUID) 
  <img src="https://github.com/rsnchzl/election-1/blob/main/screenshots/enumeration/screenshotfindsuid.png"/>
  Asi veremos que hay un binario en la ruta /usr/local/Serv-U/Serv-U donde el propietario es root, por lo tanto al ejecutar este comando con nuestro usuario actual (love) estaremos ejecutandolo como si fuesemos el usuario root.
  Investigando sobre "Serv-U" descubriraras que hay diversos exploits para escalar privilegios, el script que he usado puedes encontrarlo [here](https://github.com/rsnchzl/election-1/blob/main/exploits/exploitprivilageescalation.c), desarrollado en el lenguaje C.


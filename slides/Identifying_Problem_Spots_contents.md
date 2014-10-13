## Introducción

* Antes de solucionar un problema de red hay que diagnosticarlo.
* Para ello:
 * [Localización del nivel](#/2/2)
 * [Traceo de una ruta](#/2/9)
 * [Localización de procesos](#/2/14)
 * [Examinar ficheros de log](#/2/17)

<aside class="notes">
* The first task in network problem solving is diagnosing the problem.
* This task can be a difficult one, because different problems can have similar symptoms.
* In the next few pages, I describe several tools, procedures, and rules of thumb that can help you narrow the scope of the problem to something manageable.
</aside>



## Localización del nivel (I)

<a class="fancybox" href="img/layers.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="550px" src="img/layers.png" alt="Volumenes físicos">
</a>

<aside class="notes">
* Networks can be thought of as being built up in layers, from physical hardware (cables, network cards, and so on) at the lowest layers to high-level applications (Web browsers, SSH servers, and so on) at the highest layers.
* In-between layers include hardware drivers, kernel features that implement protocols like TCP and IP, and so on.
* These layers can be classified in several ways, such as the Open Systems Interconnection (OSI) model shown in
* TCP/IP networking is often described in a similar four-layer model consisting of the Link, Internet, Transport, and Application layers.
* Each layer is responsible for packing or unpacking data to or from the equivalent layer on the computer with which it’s communicating, as well as communicating with the adjacent layers on its own system.
* In this way, data travels down the network stack from the sender and up the network stack on the recipient.
</aside>



## Localización del nivel (II)

<a class="fancybox" href="img/wires.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="550px" src="img/wires.png" alt="Volumenes físicos">
</a>

<aside class="notes">
* Thinking in terms of a network stack can help you isolate a problem.
* You do need to understand the roles of certain key subsystems, such as the hardware, drivers, network interface, name resolution, and applications.
* A break in a network cable, for instance, will interfere with all network traffic, whereas a failure of name resolution will affect only traffic that’s addressed by hostname rather than by IP address.
* You can use this knowledge to perform some key tests when you discover a problem:...
</aside>



## Localización del nivel (III)

* Realizamos un test de conectividad usando la utilidad `ping`
 * <span class="fragment fade-in"> Si el sistema objetivo responde... </span>
 * <span class="fragment fade-in"> Sabemos que el problema no está en las capas inferiores </span>
 * <span class="fragment fade-in"> Ojo que los paquetes ICMP pueden estar bloqueados por un firewall </span>



## Localización del nivel (IV)

* Comprobamos ordenadores locales (localizadas en la misma red física) y ordenadores remotos
 * <span class="fragment fade-in"> Si los ordenadores locales funcionan pero los remotos no... </span>
 * <span class="fragment fade-in"> ***Seguramente nos estaremos enfrentando a un problema de enrutamiento*** </span>



## Localización del nivel (V)
* Intentamos acceder a un ordenador tanto por su IP como por su nombre
 * <span class="fragment fade-in"> El acceso mediante IP funciona pero falla el acceso por nombre... </span>
 * <span class="fragment fade-in"> ***Hay problemas con la resolución de nombres*** </span>



## Localización del nivel (VI)

* Comprobamos distintos protocolos (http, smtp, ntp, ftp, dns...)
 * <span class="fragment fade-in"> El servidor sólo responde a algunos de ellos... </span>
 * <span class="fragment fade-in"> ***Hay problemas en las capas superiores, con la configuración de los distintos servicios*** </span>



## Localización del nivel (VII)

* Mediante utilidades como wireshark comprobamos que el servidor responde a todas la peticiones
 * <span class="fragment fade-in"> ***Los problemas se deben a una mala interacción ¿las claves son correctas?*** </span>



## Traceo de una ruta (I)

* El comando [`traceroute`](http://linux.die.net/man/8/traceroute) tracea la ruta seguida por los paquetes para llegar de un origen a un destino
* Se basa en el campo [TTL (Time To Live)](http://es.wikipedia.org/wiki/Tiempo_de_vida_(inform%C3%A1tica)) de la cabecera IP
* Los router devuelve un paquete ICMP al origen cuando TTL = 1

```bash
traceroute [-46dFITUnreAV] [-f first_ttl] [-g gate,...]

[-i device] [-m max_ttl] [-p port] [-s src_addr]
[-q nqueries] [-N squeries] [-t tos]
[-l flow_label] [-w waittime] [-z sendwait]
[-UL] [-P proto] [--sport=port] [-M method] [-O mod_options]
[--mtu] [--back]
host [packet_len]
traceroute6 [options]
```

<aside class="notes">
*  The [`ping`](http://linux.die.net/man/8/ping) command, is a very useful tool in diagnosing network problems.
* A step up from ping is the [`traceroute`](http://linux.die.net/man/8/traceroute) command, which sends a series of three test packets to each computer between your system and a specified target system.
* Traceroute utility uses the TTL (hops a particular packet will take) field in the IP header to achieve its operation.
* So, this effectively outlines the lifetime of the packet on network.
* This field is usually set to 32 or 64. Each time the packet is held on an intermediate router, it decreases the TTL value by 1.
* When a router finds the TTL value of 1 in a received packet then that packet is not forwarded but instead discarded.
* After discarding the packet, router sends an ICMP error message of “Time exceeded” back to the source from where packet generated.
* The ICMP packet that is sent back contains the IP address of the router.
* So now it can be easily understood that traceroute operates by sending packets with TTL value starting from 1 and  then incrementing by one each time.
* Each time a router receives the packet, it checks the TTL field, if TTL field is 1 then it discards the packet and sends the ICMP error packet containing its IP address and this is what traceroute requires.
* So traceroute incrementally fetches the IP of all the routers  between the source and the destination.
</aside>



## Traceo de una ruta (II)
### Ejemplos (I)

* Sin resolución de nombres

```bash
ijfviana-dev@vagalume-dev:~$ traceroute -n 10.1.0.43

traceroute to 10.1.0.43 (10.1.0.43), 30 hops max, 52 byte packets
1 192.168.1.254 1.021 ms 36.519 ms 0.971 ms
2 10.10.88.1 17.250 ms 9.959 ms 9.637 ms
3 10.9.8.173 8.799 ms 19.501 ms 10.884 ms
4 10.9.8.133 21.059 ms 9.231 ms 103.068 ms
5 10.9.14.9 8.554 ms 12.982 ms 10.029 ms
6 10.1.0.44 10.273 ms 9.987 ms 11.215 ms
7 10.1.0.43 16.360 ms * 8.102 ms
```

<aside class="notes">
* The -n option to this command tells it to display target computers’ IP addresses rather than their hostnames.
* This sample output shows a great deal of variability in response times. The first hop, to 192.168.1.254, is purely local; this router responded in 1.021, 36.519, and 0.971 milliseconds (ms) to its three probes.
* Probes of most subsequent systems are in the 8–20ms range, although one is at 103.068 ms. The final system has only two times; the middle probe never returned, as the asterisk (*) on this line indicates.
* Using traceroute, you can localize problems in network connectivity.
* Highly variable times and missing times can indicate a router that’s overloaded or that has an unreliable link to the previous system on the list.
* If you see a dramatic jump in times, it typically means that the physical distance between two routers is great.
* This is common in intercontinental links. Such jumps don’t necessarily signify a problem unless the two systems are close enough that a huge jump isn’t expected.
</aside>



## Traceo de una ruta (III)
### Ejemplos (II)

* Con resolución de nombre

```bash
$ traceroute google.com
traceroute to google.com (74.125.236.132), 30 hops max, 60 byte packets
1  220.224.141.129 (220.224.141.129)  89.174 ms  89.094 ms  89.054 ms
2  115.255.239.65 (115.255.239.65)  109.037 ms  108.994 ms  108.963 ms
3  124.124.251.245 (124.124.251.245)  108.937 ms  121.322 ms  121.300 ms
4  * 115.255.239.45 (115.255.239.45)  113.754 ms  113.692 ms
5  72.14.212.118 (72.14.212.118)  123.585 ms  123.558 ms  123.527 ms
6  72.14.232.202 (72.14.232.202)  123.499 ms  123.475 ms  143.523 ms
7  216.239.48.179 (216.239.48.179)  143.503 ms  95.106 ms  95.026 ms
8  bom03s02-in-f4.1e100.net (74.125.236.132)  94.980 ms  104.989 ms  104.954 ms
```



## Traceo de una ruta (IV)
### Ejemplos (III)

* Modificando el tiempo de espera

```bash
$ traceroute google.com -w 0.1
traceroute to google.com (74.125.236.101), 30 hops max, 60 byte packets
1  * * *
2  * * *
3  * * *
..
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
```

<aside class="notes">
* The time for which traceroute utility waits after issuing a probe can also be configured.
* This can be done through ‘-w’ option that it provides.
* The -w option expects a value which the utility will take as the response time to wait for.
* In this example, the wait time is 0.1 seconds and the traceroute utility was unable to wait for any response and it printed all the *’s.
</aside>



## Traceo de una ruta (V)
### Ejemplos (IV)

* Modificando el número de pruebas por salto

```bash
$ traceroute google.com -q 5
traceroute to google.com (173.194.36.46), 30 hops max, 60 byte packets
1  220.224.141.129 (220.224.141.129)  91.579 ms  91.497 ms  91.458 ms  91.422 ms  91.385 ms
2  115.255.239.65 (115.255.239.65)  91.356 ms  91.325 ms  98.868 ms  98.848 ms  98.829 ms
3  124.124.251.245 (124.124.251.245)  94.581 ms  107.083 ms  107.044 ms  107.017 ms  106.981 ms
4  115.255.239.45 (115.255.239.45)  106.948 ms  106.918 ms  144.432 ms  144.412 ms  144.392 ms
5  72.14.212.118 (72.14.212.118)  115.565 ms  115.485 ms  115.446 ms  115.408 ms  115.381 ms
6  72.14.232.202 (72.14.232.202)  115.351 ms  87.232 ms  117.157 ms  117.123 ms  117.049 ms
7  209.85.241.189 (209.85.241.189)  126.998 ms  126.973 ms  126.950 ms  126.929 ms  126.912 ms
8  bom04s02-in-f14.1e100.net (173.194.36.46)  126.889 ms  95.526 ms  95.450 ms  95.418 ms  105.392 ms
```

<aside class="notes">
* The traceroute utility sends 3 packets per hop to provide 3 round trip times.
* This default value of 3 is configurable using the option ‘-q’.
* This option expects an integer which it sets as new value of number of probes per hop.
</aside>



## Localización de procesos (I)

* Muchos problemas de conectividad se deben a los procesos que están en ejecución.
* Dichos procesos no se están ejecutando o no  están escuchando en el puerto o dirección adecuados.
* Las utilidades [`netstat`](#/3/15) y [`lsof`](#/3/16) pueden ayudarnos a localizar estos problemas



## Localización de procesos (II)
### netstat
* La utilidad [`netstat`](http://linux.die.net/man/8/netstat) nos permite detectar los procesos en ejecución y los puertos en donde están escuchando

``` bash
$ netstat -a | grep smtp
tcp        0      0 vm015.example.com:smtp      *:*                LISTEN
```

<aside class="notes">
* Many problems relate to the status of locally running programs.
* The [`netstat`](http://linux.die.net/man/8/netstat) tool can be useful in locating programs that are accessing the network, including identifying the ports they’re using.
* If a server isn’t responding to connection requests, you can use netstat to verify that it is in fact listening to the correct port.
* For instance, suppose you want to check for the presence of a Simple Mail Transport Protocol (SMTP) server, which should be active on port 25 (identified as smtp).
* You could do so as follows:
# netstat -ap | grep smtp
* If an SMTP server is running, the fact should be apparent in the output. If netstat is run as root, the output will include the process ID (PID) number and process name of the server.
</aside>



## Localización de lo procesos (III)
### lsof (I)

* La utilidad [`lsof`](http://linux.die.net/man/8/lsof) muestras los ficheros abiertos.
* Los sockets son considerados un tipo de fichero

```bash
lsof -i :smtp
COMMAND   PID USER   FD   TYPE DEVICE SIZE NODE NAME
sendmail 3098 root    4u  IPv4  13077       TCP vm015.example.com:smtp (LISTEN)
```

<aside class="notes">
* Another tool that’s useful in locating network-using programs is lsof.
* This program’s main purpose is to list open files; however, the program’s definition of “file” includes network sockets.
* Therefore, it’s useful for some of the same purposes as netstat.
</aside>



## Localización de procesos (III)
### lsof (II)

<div class="table-responsive">
  <table class="table table-condensed  table-hover table-bordered">

<thead>
<tr>
<th width="280pt">Opción</th>
<th>Descripción</th>
</tr>
</thead>
<tbody>
<tr>
<td>`-i [address]`</td>
<td>Selecciona los procesos que usan acceso a la red</td>
</tr>
<tr>
<td>`-l`</td>
<td>No hace la conversió de UID a nombre de usuario</td>
</tr>
<tr>
<td>`+l -M`</td>
<td>Activa/desactiva informe sobre mapeo de puertos</td>
</tr>
<tr>
<td>`-n`</td>
<td>Previene conversón de IP a nombres</td>
</tr>
</tbody>
</table>
</div>

<aside class="notes">
| Option         | Purpose |
|----------------|---------|
| -i [ address ] | Selects processes using network accesses, optionally restricted to those using the specified address. The address takes the form [46][ protocol ][@ host name | hostaddr ][: service | port ] , where 46 refers to the IP version, protocol is the protocol name ( TCP or UDP ), hostname is a hostname, hostaddr is an IP address, service is a service name, and port is a port number.
| -l             | Prevents conversion of user ID (UID) numbers to usernames.
| +l -M          | Enables ( + ) or disables ( - ) the reporting of port mapper registrations, which are used by Network File System (NFS) and some other servers. The default behavior is a compile-time option.
| -n             | Prevents conversion of IP addresses to hostnames in the output.
| -P             | Prevents conversion of port numbers to service names.
| -t             | Displays only process ID (PID) numbers; useful if you want to pipe the output to kill or some other program that operates on PID numbers.
| -X             | Skips display of information on TCP, UDP, and UDPLITE files. Essentially, this option restricts output to non-network information. As such, using it with -i is an error.
</aside>



## Localización de procesos (III)
### lsof (III)

<div class="table-responsive">
  <table class="table table-condensed  table-hover table-bordered">

<thead>
<tr>
<th width="280pt">Opción</th>
<th>Descripción</th>
</tr>
</thead>
<tbody>
<tr>
<td>`-P`</td>
<td>Previene conversión de numero de puerto a nombre de servicio</td>
</tr>
<tr>
<td>`-t`</td>
<td>Sólo muestra PID procesos</td>
</tr>
<tr>
<td>`-X`</td>
<td>No muestra información sobre TCP, UDP</td>
</tr>
</tbody>
</table>
</div>

<aside class="notes">
| Option         | Purpose |
|----------------|---------|
| -i [ address ] | Selects processes using network accesses, optionally restricted to those using the specified address. The address takes the form [46][ protocol ][@ host name | hostaddr ][: service | port ] , where 46 refers to the IP version, protocol is the protocol name ( TCP or UDP ), hostname is a hostname, hostaddr is an IP address, service is a service name, and port is a port number.
| -l             | Prevents conversion of user ID (UID) numbers to usernames.
| +l -M          | Enables ( + ) or disables ( - ) the reporting of port mapper registrations, which are used by Network File System (NFS) and some other servers. The default behavior is a compile-time option.
| -n             | Prevents conversion of IP addresses to hostnames in the output.
| -P             | Prevents conversion of port numbers to service names.
| -t             | Displays only process ID (PID) numbers; useful if you want to pipe the output to kill or some other program that operates on PID numbers.
| -X             | Skips display of information on TCP, UDP, and UDPLITE files. Essentially, this option restricts output to non-network information. As such, using it with -i is an error.
</aside>



## Examinar ficheros de log (I)

* Los ficheros de log (`/var/log/`) pueden dar pistas sobre la naturaleza del problema
* Podemos usar el comando `dmesg` para consultar mensajes procedentes del kernel
```bash
$ dmesg
[ 14.335985] r8169 0000:02:00.0: eth0: RTL8168c/8111c at 0xffffc90011242000,
00:e0:4d:a3:24:a5, XID 1c2000c0 IRQ 40
[ 14.343594] udev: renamed network interface eth0 to eth1
```
* Toda esta información se guarda en `/var/log/dmesg`

<aside class="notes">
* Log files can contain a wealth of information about networking issues.
* In practice, you may need to examine several log files and perhaps use tools such as grep to help you wade through the copious information stored in these files.
* The first log file isn’t really a file at all; it’s the kernel ring buffer, which is a storage area for messages generated by the Linux kernel.
* You can access the kernel ring buffer by typing dmesg. The result will be a large number of messages, perhaps including some like this:
[ 14.335985] r8169 0000:02:00.0: eth0: RTL8168c/8111c at 0xffffc90011242000,
00:e0:4d:a3:24:a5, XID 1c2000c0 IRQ 40
[ 14.343594] udev: renamed network interface eth0 to eth1
* Because the kernel ring buffer contains messages from the kernel, its information usually relates to drivers and other low-level activities.
* This example, for instance, shows a message displayed by the r8169 driver, which has been loaded to manage an Ethernet device for a Realtek (RTL) 8168c/8111c chipset. The first line indicates that the driver has been assigned the eth0 identifier. The second line, though, indicates that the udev subsystem has renamed eth0 to eth1. Such a detail can be critical; if you’re expecting to find the network managed by the Realtek device on eth0, you’ll have problems. In the face of such information, you should either adjust your network configuration to use eth1 rather than eth0 or adjust your udev configuration to not rename the network device.
</aside>



## Examinar ficheros de log (II)

* Otros ficheros de log especialmente importantes son `/var/log/messages` y `/var/log/syslog`
* Además, muchos servidores suelen tener sus propios ficheros de log (/var/log/httpd/)

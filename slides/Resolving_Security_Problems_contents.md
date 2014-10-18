##  Introducción

* En ocasiones los problemas de red se deben a unas políticas de seguridad demasiado estrictas.
* Estas políticas se puede definir en distintos entornos:
 * [Firewall](#/6/2)
 * [tcpwrappers](#/6/5)
 * [SElinux/AppArmor](#/6/8)



## Firewall (I)

* Muchos problemas de red se deben a malas configuraciones del [firewall](http://es.wikipedia.org/wiki/Cortafuegos_%28inform%C3%A1tica%29)

<a class="fancybox" href="img/firewall.png" data-fancybox-group="gallery" title="Firewall">
	<img height="500px" src="img/firewall.redimensionado50.png" alt="Firewall">
</a>

<aside class="notes">
* Some problems are related to security settings. One area that can cause mysterious problems is firewall settings.
</aside>



## Firewall (II)

* En Linux la configuración del firewall se gestiona usando [iptables](http://linux.die.net/man/8/iptables)
* Permite gestionar las cadenas de reglas que se aplican al recibir tráfico entrante, saliente o de reenvio.

<a class="fancybox" href="img/iptables.png" data-fancybox-group="gallery" title="iptables">
	<img height="350px" src="img/iptables.png" alt="iptables">
</a>



## Firewall (III)

* Podemos consultar el estado de estas colas ejecutando [iptables -L](http://linux.die.net/man/8/iptables)

```bash
$ iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source
destination
Chain FORWARD (policy ACCEPT)
target prot opt source
destination
Chain OUTPUT (policy ACCEPT)
target prot opt source
destination
```

<aside class="notes">
* We can check your firewall settings by using the -L option to iptables:
```bash
$ iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source
destination
Chain FORWARD (policy ACCEPT)
target prot opt source
destination
Chain OUTPUT (policy ACCEPT)
target prot opt source
destination
```
* This example shows no firewall rules defined.
* If you see rules specified under the INPUT or OUTPUT chains, the ability of your computer to function as a client or server for particular protocols is impaired.
* Likewise if any of the chains sets a default policy of DROP instead of ACCEPT. Such a configuration isn’t necessarily a bad thing;
* The whole point of a firewall is to prevent unwanted access to or from the computer.
* The firewall rules must be tailored to your use of the computer, though.
* For instance, you wouldn’t want to block the incoming SMTP port on a mail server, except perhaps in a limited way—say, to prevent outside access to a server that exists only to handle outgoing mail from local computers.
* The FORWARD chain affects the forwarding of packets between network interfaces on routers, so this chain will only affect computers that are configured as routers.
</aside>



## TcpWrapper (I)

* [TCP Wrapper](http://es.wikipedia.org/wiki/TCP_Wrapper) es un sistema de red ACL que se usa para filtrar el acceso de red a servicios.
* Actúa entre el cliente y el servicio, permitiendo o denegando la solicitud.

<a class="fancybox" href="img/tcpwrapper.png" data-fancybox-group="gallery" title="TCP_Wrapper">
	<img height="450px" src="img/tcpwrapper.redimensionado50.png" alt="TCP_Wrapper">
</a>



## TcpWrapper (II)

* Se basa en el uso de los ficheros [`/etc/hosts.allow`](http://linux.die.net/man/5/hosts.allow) y [`/etc/hosts.deny`](http://linux.die.net/man/5/hosts.deny) que indican qué ordenadores pueden acceder a ciertos servicios
* Sigue la siguiente lógica:

 1. Si se encuentra regla aplicable en `/etc/hosts.allow`, permite el acceso. Si no se encuentra se pasa al siguiente paso.
 2. Si se encuentra regla aplicable en `/etc/hosts.deny`, deniega el acceso. Si no encuentra regla, lo permite.

<aside class="notes">
* Another security issue that can crop up is settings in the /etc/hosts.allow and /etc/hosts.deny files.
* These files are used mainly by the TCP Wrappers program,
* these files contain rules that affect servers that are configured to use TCP Wrappers.
* Thus, if a server isn’t responding to connection requests, particularly if it’s responding to some connection requests but not others, you can check these files for entries.
* If these files are empty except for comments, they shouldn’t have any effect.
</aside>



## TcpWrapper (III)
### Ejemplos ficheros

```bash
$ cat /etc/hosts.deny
#FILE: /etc/hosts.deny
vsftpd : 192.168.1. , .abc.com
```

```bash
$ cat /etc/hosts.allow
#FILE: /etc/hosts.allow
sshd : ALL EXCEPT 192.168.0.15
```



## SElinux/AppArmor

* [SeLinux](http://www.nsa.gov/research/selinux/index.shtml) y [AppArmor](http://es.wikipedia.org/wiki/AppArmor) son aplicaciones de seguridad que  permiten al administrador del sistema asociar a cada programa un perfil de seguridad que restrinja las capacidades de ese programa.
* Complementa el modelo tradicional de control de acceso discrecional de Unix (DAC) proporcionando el control de acceso obligatorio (MAC).
* Podemos establecer restricciones sobre los recursos del sistema a usar y no solo sobre cuestiones de red

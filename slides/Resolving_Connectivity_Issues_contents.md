## Introduccion

* Se suelen deber a problemas hardware o a una mala configuración de parámetros.
* Debemos revisar la configuración haciendo uso, principalmente, de la utilidad [`ifconfig`](http://linux.die.net/man/8/ifconfig).
* Si la configuración es correcta y sigue sin funcionar, actuaremos de forma diferente si
 * Usamos DCHP
 * Usamos direccionamiento estático



## Problemas de conectividad
### Problemas DHCP

* ¿Pueden otros ordenadores hacer uso del Sevidor DCHP?
 * No, hay que revisar la configuración del servidor de DHCP
 * Sí, el servidor de DCHP es selectivo a la hora de dar direcciones IP
* ¿Están en el mismo segmento de red que el servidor DHCP?
 * No, los router no suelen hacer forwarding de tráfico DHCP
* Prueba con distintos cliente DHCP
* Comprueba los ficheros de log del cliente y servidor de DHCP



## Problemas de conectividad
### Direccionamiento estático

* Comprobamos las configuraciones de los ficheros localizados en `/etc/network`, `/etc/sysconfig/network-scripts/`
* Revisamos: dirección IP, máscara de red, etc
* *** Nunca hay que descartar problemas hardware, incluyendo drivers ***

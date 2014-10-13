## Introducción

* Lo problemas de enrutamiento se detectan mediante la utilidad [`traceroute`](http://linux.die.net/man/8/traceroute).
* Suelen aparecer exporádicamente.

<a class="fancybox" href="img/routing.gif" data-fancybox-group="gallery" title="Routing">
	<img height="350px" src="img/routing.gif" alt="Routing">
</a>



## Soluciones

* Contactar con el responsable del route
* Reiniciar todos los dispositivos de red
* Comprobar la máscara de red
* Comprobar la tabla de enrutamiento mediante la utilidad [`route`](http://linux.die.net/man/8/route)
* En el dispositivo que actua como router, comprobar el valor de `/proc/sys/net/ipv4/ip_forward`.

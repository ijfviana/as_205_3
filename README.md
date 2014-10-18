as_205_3
========

Transparencias correspondientes al tema de "Configuración de red, resolución de problemas" de la asignatura Administración de Servidores impartida en el Grado de Ingeniería Informática de la Universidad de Huelva


Instalación
===========

Para instalarlo en Ubuntu 14.04, se siguen los siguientes pasos:

1. Instale nodejs

```
sudo apt-get install nodejs nodejs-legacy
```

2. Instale npm

```
sudo apt-get install npm
```

3. Instale grunt

```
sudo npm install -g grunt-cli
```

4. Instalar bower

```
sudo npm install -g bower
```

5. Clone el repositorio

```
git clone https://github.com/ijfviana/as_205_3.git
```

6. Accedemos al directorio `as_205_3`

```
cd as_205_3
```

7. Instale las dependencias mediante bower

```
bower install
```

7. Ejecute la presentación

```
grunt server
```

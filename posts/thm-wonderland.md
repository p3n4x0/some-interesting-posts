> Dificultad: Media

![](/assets/images/thm_wonderland/thm_wonderland_logo.jpeg)

Esta sala es realmente interesante ya que vas a explotar diferentes tipos de escalada de privilegios, desde PATH hijacking, Python Library Hijacking, análisis de binarios y capacidades setuid que te harán pasar horizontalmente de usuario en usuario hasta llegar a root.
# Enumeración

Comenzamos lanzando nmap para ver que servicios están corriendo en la maquina.
```bash
nmap -p- -Pn -sCV --min-rate 5000 10.10.253.220 -oG nmap
```
```bash
-p-         # Escanea todos los puertos disponibles en el objetivo.
-Pn         # Deshabilita la resolución de DNS para acelerar el escaneo.
-sC         # Ejecuta scripts de detección de servicios y muestra información detallada.
-sV         # Intenta identificar la versión de los servicios en los puertos abiertos.
--min-rate  # Establece la velocidad mínima para enviar paquetes (paquetes no más lentos que este valor).
-oG         # Guarda la salida del escaneo en un formato grepeable para análisis posterior.
```

Nos fijamos en el puerto 80 y pasamos a analizarlo mas en profundiad...

![](/assets/images/thm_wonderland/thm_wonderland_nmap.PNG)

# Servicio HTTP

![](/assets/images/thm_wonderland/thm_wonderland_page.PNG)

en el código fuente de la pagina tampoco se aprecia nada destacable
```html
<!DOCTYPE html>
<head>
    <title>Follow the white rabbit.</title>
    <link rel="stylesheet" type="text/css" href="[/main.css](view-source:http://10.10.253.220/main.css)">
</head>
<body>
    <h1>Follow the White Rabbit.</h1>
    <p>"Curiouser and curiouser!" cried Alice (she was so much surprised, that for the moment she quite forgot how to speak good English)</p>
    <img src="[/img/white_rabbit_1.jpg](view-source:http://10.10.253.220/img/white_rabbit_1.jpg)" style="height: 50rem;">
</body>
```

## Enumeración de directorios
Pasamos a enumerar los directorios en búsqueda de mas información.
![](/assets/images/thm_wonderland/thm_wonderland_gob1.PNG)

Analizamos `/r` y nos encontramos un mensaje que dice ***Keep Going***
```html
<!DOCTYPE html>
<head>
    <title>Follow the white rabbit.</title>
    <link rel="stylesheet" type="text/css" href="[/main.css](view-source:http://10.10.253.220/main.css)">
</head>
<body>
    <h1>Keep Going.</h1>
    <p>"Would you tell me, please, which way I ought to go from here?"</p>
</body>
```
Por lo tanto seguimos profundizando con `gobuster`
![](/assets/images/thm_wonderland/thm_wonderland_gob2.PNG)

Hasta encontrarnos con `/r/a/b/b/i/t/`
```html
<!DOCTYPE html>

<head>
    <title>Enter wonderland</title>
    <link rel="stylesheet" type="text/css" href="[/main.css](view-source:http://10.10.253.220/main.css)">
</head>

<body>
    <h1>Open the door and enter wonderland</h1>
    <p>"Oh, you’re sure to do that," said the Cat, "if you only walk long enough."</p>
    <p>Alice felt that this could not be denied, so she tried another question. "What sort of people live about here?"
    </p>
    <p>"In that direction,"" the Cat said, waving its right paw round, "lives a Hatter: and in that direction," waving
        the other paw, "lives a March Hare. Visit either you like: they’re both mad."</p>
    <p style="display: none;">alice:HowDothTheLittleCrocodileImproveHisShiningTail</p>
    <img src="[/img/alice_door.png](view-source:http://10.10.253.220/img/alice_door.png)" style="height: 50rem;">
</body>
```

Como se puede apreciar encontramos las credenciales del usuario ***alice*** que pasaremos a probar en el servicio ssh

# Servicio SSH

![](/assets/images/thm_wonderland/thm_wonderland_ssh.PNG)

Ya hemos conseguido entrar a la maquina ahora buscaremos nuestra ansiada flag `user.txt`

![](/assets/images/thm_wonderland/thm_wonderland_ll.PNG)

Nos encontramos con dos cosas que nos llaman la atencion.
### 1)root.txt
Nos encontramos con la flag de `root.txt` la cual aun no tenemos acceso a leer.
Por lo que lo primero que se me ocurre es mirar si se ha hecho un intercambio  alojandose la flag del user en `/root/user.txt`

![](/assets/images/thm_wonderland/thm_wonderland_user.PNG)
### 2)walrus_and_the_carpenter.py
Nos encontramos con un fichero `python`
```python
import random
poem = """The sun was shining on the sea,
Shining with all his might:
He did his very best to make
The billows smooth and bright —
And this was odd, because it was
The middle of the night.

The moon was shining sulkily,
Because she thought the sun
Had got no business to be there
After the day was done —
"It’s very rude of him," she said,
"To come and spoil the fun!"

...Muchos mas poemas...
"""

for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```
# Escalada de privilegios nº1

Muy bien, ya tenemos nuestra flag de `user.txt`... ahora toca obtener privilegios máximos en la maquina es decir, ser `root`

Lo primero que hago es mirar `sudo -l` .
Esto permite a un usuario ver qué operaciones puede realizar con privilegios elevados.
![](/assets/images/thm_wonderland/thm_wonderland_sudo.PNG)
Un momento... `(rabbit)`... Parece ser que hay mas usuarios en la maquina
listamos el fichero `/etc/passwd` para analizar el numero de usuarios que hay en la maquina
```bash
root❌0:0:root:/root:/bin/bash
tryhackme❌1000:1000:tryhackme:/home/tryhackme:/bin/bash
alice❌1001:1001:Alice Liddell,,,:/home/alice:/bin/bash
hatter❌1003:1003:Mad Hatter,,,:/home/hatter:/bin/bash
rabbit❌1002:1002:White Rabbit,,,:/home/rabbit:/bin/bash
```

Vemos que desde el usuario `rabbit` el usuario `alice` tiene permisos para ejecutar el archivo  que hemos visto `walrus_and_the_carpenter.py`:
```bash
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```
>El script ejecuta un bucle for e imprime líneas aleatorias de *La morsa y el carpintero* de Lewis Carroll. Las líneas se generan utilizando la biblioteca `random` de python.

## Python Library Hijacking
No hay nada que podamos hacer con este script en el estado actual, ya que sólo imprime líneas aleatorias.  

> "Python Library Hijacking" es una vulnerabilidad de seguridad en la que un atacante reemplaza una biblioteca de Python utilizada por una aplicación con una versión maliciosa o comprometida de la misma biblioteca. Esto puede permitir al atacante ejecutar código malicioso en el contexto de la aplicación y comprometer su seguridad.

Orden en el q se busca una librería:

- 1) El directorio que contiene el script de entrada (o el directorio actual cuando no se especifica un archivo)
- 2) PYTHONPATH (una lista de nombres de directorio, con la misma sintaxis que la variable de entorno PATH)
- 3) El valor predeterminado dependiente de la instalación

Por lo tanto si creamos en el mismo directorio un fichero llamado `random.py` lo interpretara como la librería a utilizar.

![](/assets/images/thm_wonderland/thm_wonderland_random.PNG)

Tras esta modificacion lanzamos el script:
```bash
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```


# Escalada de privilegios nº2

![](/assets/images/thm_wonderland/thm_wonderland_ll2.PNG)

Ya tenemos acceso como el usuario rabbit, podemos ver que el binario `teaParty`  tiene permisos de `SUID` y `SGID` siendo el usuario y el grupo `root`

> El bit SUID (Set User ID) permite que el archivo se ejecute con los privilegios del propietario, lo que podría tener riesgos de seguridad si el archivo es vulnerable o malicioso.
> El bit SGID (Set Group ID) permite que el archivo se ejecute con los privilegios del grupo, lo que también puede tener implicaciones de seguridad.

Vamos a descargarnos el binario para poder analizarlo mas en profundidad en local.

Hay varias maneras de hacer esto, como aprovechar la cuenta vulnerada de `alice` para mediante la herramienta `scp` copiarse el archivo.

Yo como hay `python` en la maquina veo mas cómodo hacerlo mediante una petición `http`

![](/assets/images/thm_wonderland/thm_wonderland_server.PNG)

![](/assets/images/thm_wonderland/thm_wonderland_wget.PNG)

## Análisis del fichero
Probamos a ejecutarlo y tras unas pruebas se observa que arroja un error del tipo `Segmentation fault (core dumped)`
![](/assets/images/thm_wonderland/thm_wonderland_exec.PNG)

Lo analizo con la herramienta `strings` para ver todas las cadenas que contiene el binario.
![](/assets/images/thm_wonderland/thm_wonderland_strings.PNG)
## PATH Hijacking
Rápidamente vemos que hace uso de la función date pero esta no esta indicada con su path absoluto por lo que podemos suplantarla.
Contenido del fichero `date`:
```bash
#!/bin/bash
/bin/bash
```
![](/assets/images/thm_wonderland/thm_wonderland_date.PNG)

# Escalada de privilegios nº3

Nos encontramos con el fichero `password.txt`

![](/assets/images/thm_wonderland/thm_wonderland_sudo2.PNG)

Vemos que no podemos ejecutar comandos como sudo

![](/assets/images/thm_wonderland/thm_wonderland_id.png)

Esta contraseña nos servirá para conectarnos por `ssh` directamente, y asi tener asignados todos los permisos tanto de usuario como de grupo a este ultimo usuario.

## Capabilities
Una de las primeras cosas que me gusta comprobar de cara a la escalada de privilegios vienen a ser los capabilities

>En el contexto de la escalada de privilegios, las "capabilities" se refieren a los permisos específicos y granulares que un proceso o usuario tiene en un sistema operativo. Estos permisos pueden otorgar acceso a funciones o recursos específicos del sistema, como la capacidad de abrir puertos de red o realizar operaciones de lectura/escritura en archivos críticos.

```bash
getcap -r / 2>/dev/null
```
![](/assets/images/thm_wonderland/thm_wonderland_cap.png)

Lo primero que hago es buscar en la maravillosa pagina [GTFOBins](https://gtfobins.github.io/gtfobins/perl/#capabilities) que dice lo siguiente:

>Si el binario tiene la capacidad Linux `CAP_SETUID` establecida o es ejecutado por otro binario con la capacidad establecida, puede ser usado como una puerta trasera para mantener acceso privilegiado manipulando su propio UID de proceso.

```bash
./perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

![](/assets/images/thm_wonderland/thm_wonderland_root.PNG)

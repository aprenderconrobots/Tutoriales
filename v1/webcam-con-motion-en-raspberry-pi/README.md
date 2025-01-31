## Introducción

En este tutorial vamos a explicar cómo instalar y configurar motion para montar un sistema de videovigilancia.

### Antes de empezar

Vas a necesitar los siguientes componentes:

- Raspberry Pi con Raspbian
- Webcam USB

Es recomendable acceder a los siguientes tutoriales:

- Configurar Webcam en Raspberry Pi



<br />



## Software Motion

[Motion](https://motion-project.github.io) es uno de los programas más populares para utilizar con webcams, cuya utilidad es la de detectar movimientos. De esta forma podemos grabar momentos en los cuales se ha detectado movimiento. 



<br />



## Instalar Motion

> Recuerda que antes de instalar un programa es recomendable actualizar Raspbian como se explica en el tutorial de actualizar Raspbian.  

Una vez actualizada la lista de paquetes del repositorio, podemos instalar Motion mediante el comando `apt install motion`.

```sh
pi@raspberrypi:~ $ sudo apt install motion
```



<br />



## Arrancar Motion

En este caso vamos a configurar el fichero de configuración encargado de habilitar el daemon de Motion al iniciar la Raspberry Pi. Para ello accedemos al fichero `/etc/default/motion`. Para abrirlo de una forma sencilla y con privilegios de administrador introducimos el comando `sudo leafpad /etc/default/motion`.

```sh
pi@raspberrypi:~ $ sudo leafpad /etc/default/motion
```

```
# set to 'yes' to enable the motion daemon
start_motion_daemon=yes
```

Por último, reseteamos el servicio con el comando `service motion restart` para que los cambios tengan efecto y una vez reiniciado el servicio ya podemos acceder a través de la dirección `localhost:8081`.

```sh
pi@raspberrypi:~ $ sudo service motion restart
```

Para comprobar que se ha instalado correctamente, abrimos un navegador en la misma Raspberry Pi, accedemos a la dirección `localhost:8081` y se debe ver la webcam.

![](img/motion.jpg "Motion")



<br />



## Configuración (solo para módulo PICAM)

Solo si dispones de un módulo PICAM deberás activar el driver que haga funcionar el programa motion. En caso de disponer de una webcam omite esta sección.

```sh
pi@raspberrypi:~ $ sudo modprobe bcm2835-v4l2
```

Añadimos el módulo de PICAM.

```sh
pi@raspberrypi:~ $ sudo leafpad /etc/modules
```

```
# añadir al final del fichero
bcm2835-v4l2
```

Reinicia el sistema antes de continuar con el siguiente paso.



<br />



## Acceder a Motion de forma remota

Probablemente lo que nos interese sea ver el contenido de la webcam desde fuera de la Raspberry Pi, por ejemplo, para controlar un bebe desde otra habitación. Para ello vamos a configurar Motion para que nos permita acceder desde la propia red local.

En primer lugar accedemos al fichero `/etc/motion/motion.conf` donde aparecen todas las opciones de configuración de Motion. Para abrirlo de una forma sencilla y con privilegios de administrador introducimos el comando `sudo leafpad /etc/motion/motion.conf`.

```sh
pi@raspberrypi:~ $ sudo leafpad /etc/motion/motion.conf
```

Vamos a indicar en el fichero de configuración que el software arranque como servicio. Para ello cambiamos el valor `daemon on`. También debemos especificar el puerto web por el cual veremos nuestra webcam y que no solamente se va a reproducir en modo local sino que además debemos permitir el acceso desde fuera de la red local.

> Como se puede observar, en este fichero aparecen diferentes configuración por defecto, y a medida que vayamos teniendo experiencia podemos ir probando.

```
# Start in daemon (background) mode and release terminal (default: off)
daemon on

# The mini-http server listens to this port for requests (default: 0 = disabled)
stream_port 8081

# Restrict stream connections to localhost only (default: on)
stream_localhost off
```

Volvemos a resetear el servicio con el comando `service motion restart` para que los cambios tengan efecto y una vez reiniciado el servicio ya podemos acceder desde otro dispositivo conectado a la misma red local a través de la dirección `<IP>:8081`.

```sh
pi@raspberrypi:~ $ sudo service motion restart
```

![](img/remoto.jpg "Acceso remoto")



<br />



## Detectar movimientos

Por defecto, Motion guarda en imagen y vídeo cualquier evento que ocurra delante de la webcam y lo guarda en el directorio `/var/lib/motion`. Estos ajustes podemos modificarlos en el fichero de onfiguración.

```sh
pi@raspberrypi:~ $ sudo leafpad /etc/motion/motion.conf
```

```
# Output 'normal' pictures when motion is detected (default: on)
output_pictures on

# Recommended to use absolute path. (Default: current working directory)
target_dir /var/lib/motion
```

![](img/capturas.jpg "Detectar movimientos")

Recuerda que al cambiar parámetros del fichero de configuración deberás reiniciar el servicio motion con el comando `sudo service motion restart`.

```sh
pi@raspberrypi:~ $ sudo service motion restart
```

Una vez reiniciado el sistema podemos acceder a la carpeta `/var/lib/motion` y veremos todas las imágenes detectadas por la webcam.

Las imágenes son almacenadas mediante el usuario `motion`, con lo cual para poder eliminarlas con nuestro usuario `pi` tendremos que añadirle permiso a la carpeta utilizando el siguiente comando.

```sh
$ sudo chmod -R 777 /var/lib/motion/
```

Por último, recuerda parar el servicio para que no esté almacenando fotos en todo momento. Para ello deberás utilizar el siguiente comando.

```sh
pi@raspberrypi:~ $ sudo service motion stop
```

Comprueba que deja de verse la webcam a través del navegador una vez parado el servicio.



<br />



## Ejercicios propuestos

1.- Conecta una webcam USB e instala y configura Motion correctamente.

2.- Accede desde un dispositivo móvil en la misma red para comprobar que puedes ver la webcam desde el móvil.

3.- Deshabilita la captura de movimiento y grabación automática en Motion.

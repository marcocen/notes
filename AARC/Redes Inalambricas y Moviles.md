# Introduccion
El número de telefonos celulares y dispositivos móviles es cada vez más grande. Estos dispositivos prometen acceso a internet en cualquier momento y lugar.

Hay dos conceptos muy relacionados pero a su vez distintos:
 * Redes Inalámbricas: que utilizan enlaces inalambricos.
 * Redes Móviles: que permiten que el usuario se desplace de un lugar a otro, cambiando el punto de conexión a la red.

## Redes Inalambricas
### Elementos
- [ ] Agregar imagen de elementos
- Hosts inalámbricos: laptops, celulares, tablets, etc. 
	- Corren aplicaciones.
	- Pueden ser móviles o no. Movilidad e inalambrico no son iguales.
- Estaciónes base: Antenas celulares, APs 802.11
	- Generalmente conectadas a la red cableada
	- Son responsables de hacer el relay entre la red cableada y los hosts inalambricos.
- Enlaces inalambricos:
	- Generalmente conectan estaciones base -> hosts inalambricos, aunque también pueden ser utilizados en el backbone.
	- Tienen protocolos de acceso múltiple para coordinar el acceso al enlace.
	- Varias velocidades, determinadas por:
		- Calidad del medio
		- Cantidad de usuarios
		- Trafico generado

### Modos
- Modo infraestructura: estacion base conecta los hosts inalambricos a la red cableada. Existe el concepto de handoff: el host cambia de estación base que le provee la conexión. Éste puede ser:
	- Horizontal, si el handoff es entre estaciones con la misma tecnologíá. Por ejemplo entre dos APs 802.11
	- Vertical, si el handoff es entre dos tecnologías distintas. Por ejemplo si el host queda fuera del área de un AP y pasa a una red móvil.
- Modo ad hoc: Sin estaciones base, los nodos se conectan entre ellos en una red que encamina a través de ellos mismos. En principio los nodos solo pueden transmitir a otros nodos en el área de cobertura. 
- [ ] añadir imagen de red ad hoc

### Características de enlaces inalámbricos
Los enlaces inalambricos tienen diferencias clave con los enlaces cableados que hacen que la comunicación a través de un enlace inalámbrico sea más dificultosa.

- Decremento de la potencia de la señal: la señal se atenúa a medida que se propaga (path loss)
- Interferencia de otras fuentes: las frecuencias son compartidas con otros dispositivos (2.4GHz,por ejemplo) y eso genera interferencia.
- Propagación multipath: la señal se refleja en objetos, llegando a destino en tiempos ligeramente distintos.

La multiplicidad de emisores y receptores inalambricos crean problemas adicionales, como lo son las colisiones. Por ejemplo dos hosts pueden no escucharse entre ellos pero interferir en el lugar de un tercer hosts que puede escucharlos a ambos.

### Redes inalámbricas IEEE 802.11
- 802.11b: usa el espectro 2.4 GHz, no licenciado. Permite obtener velocidades de hasta 11 Mbps.
- 802.11g: espectro 2.4 GHz. Velocidades de hasta 54 Mbps.
- 802.11a: Rango no licenciado 5-5,8 GHz. Hasta 54 Mbps.
- 802.11n: 2.4 y 5 GHz. Velocidades de hasta 100 Mbps.

Todos usan CSMA/CA para acceso múltiple.
Todos tienen versiones de red en modo infraestructura y ad hoc.

#### Arquitectura LAN

El bloque fundamental es el BSS, Basic Service Set, que contiene una o mas hosts inalambricos y una estación base (AP, Access point).
Todos los hosts inalambricos tienen una MAC, así como los APs, en su interfaz inalámbrica.
Cuando un administrador instala un AP, el administrador asigna un SSID, Service Set IDentifier y un canal (hay 11 canales entre 2.4 y 2.485, siendo el set 1,6 y 11 el único en el que no se solapan).

- Jungla Wifi: cualquier ubicación física donde un host recibe señal fuerte de dos o más APs.

Para poder obtener conectividad el host se tiene que **asociar** a un AP y a partir de entonces sólo este AP enviará dataframes al host.
El estándar 802.11 establece que los APs tienen que enviar periódicamente **beacon frames**, que incluyen el SSID del AP y su MAC. La interfaz inalámbrica escanea los 11 canales y obtiene los beacons, para después poder elegir un AP para conectarse. El algoritmo para conectarse no está establecido por la IEEE, queda a discreción del programador del firmware. A esta forma de obtener los APs, se llama **Passive Scanning**.
En contraposición al passive scanning se encuentra el **Active Scanning** en el cual los hosts envían un **probe frame** que será recibido por los APs, los cuales responderán con un **probe response frame**. El host luego eligirá el AP al cual se quiere conectar.





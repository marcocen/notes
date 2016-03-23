[TOC]

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

El bloque fundamental es el **BSS**, Basic Service Set, que contiene una o mas hosts inalambricos y una estación base (AP, Access point).
Todos los hosts inalambricos tienen una MAC, así como los APs, en su interfaz inalámbrica.
Cuando un administrador instala un AP, el administrador asigna un SSID, Service Set IDentifier y un canal (hay 11 canales entre 2.4 y 2.485, siendo el set 1,6 y 11 el único en el que no se solapan).

- Jungla Wifi: cualquier ubicación física donde un host recibe señal fuerte de dos o más APs.

Para poder obtener conectividad el host se tiene que **asociar** a un AP y a partir de entonces sólo este AP enviará dataframes al host.
El estándar 802.11 establece que los APs tienen que enviar periódicamente **beacon frames**, que incluyen el SSID del AP y su MAC. La interfaz inalámbrica escanea los 11 canales y obtiene los beacons, para después poder elegir un AP para conectarse. El algoritmo para conectarse no está establecido por la IEEE, queda a discreción del programador del firmware. A esta forma de obtener los APs, se llama **Passive Scanning**.
En contraposición al passive scanning se encuentra el **Active Scanning** en el cual los hosts envían un **probe frame** que será recibido por los APs, los cuales responderán con un **probe response frame**. El host luego eligirá el AP al cual se quiere conectar.

### Protocolo MAC 802.11

Para evitar colisiones, 802.11 utiliza **CSMA/CA** (Carrier Sense Multiple Acces with Collision Avoidance) en contraposición con Ethernet que usa detección de colisiones en lugar de evitarlas.
Esto es porque para detectar colisiones: primero la interfaz tendría que poder escuchar al mismo tiempo que envía datos, lo cual haría los adaptadores más caros, y segundo porque aunque pudiera escuchar y enviar al mismo tiempo, no podríá detectar todas las colisiones por el problema de la terminal oculta.
Los hosts envían los frames completos o sea que cuando comienza a enviar no hay vuelta atrás y si hay muchas colisiones esto puede disminuir de sobremanera la performance de la red.

Las redes 802.11 usan varias estrategias para solucionar este problema.

- Link Layer Acknowledgement: Un host envía el frame completo, y se pone a esperar. Cuando una estación recibe un frame que pasa el CRC espera un período corto de tiempo (**SIFS** Short Inter-Frame Spacing) y envía un ack frame. Si el host no recibe el ack frame en un período determinado de tiempo asume que la transmisión falló y reenvia el frame completo (usando CSMA/CA para usar el canal). Si después de un número fijo de intentos no recibe un ack, se rinde y descarta el frame.
- CSMA/CA
	1. El host escucha el canal y si lo encuentra libre, espera un período conocido como **DIFS** (Distributed Inter-Frame Space).
	2. Sino la estación elige un random backoff usando exponenciación binaria y escucha el canal, disminuyendo el contador cuando el canal se siente libre (si el canal está ocupado, el contador no baja)
	3. Cuando el contador alcanza 0, la estación transmite el frame entero y espera un ack.
	4. Si no recibe un ack, sabe que el otro lado  recibió el frame y si tiene más que enviar, comienza en el paso 2
- RTS y CTS: El protocolo MAC 802.11 tiene también, opcionalmente la opción de reservar el canal para mitigar el problema del terminal oculto. Esto se logra con dos frame de control nuevos: RTS (Request to Send) y CTS (Clear to Send).
	1. El host que quiere enviar datos espera a sentir el canal libre, espera un DIFS y envía un frame RTS que contiene el tiempo total requerido para enviar los datos más el frame ACK.
	2. El AP recibe el RTS y si no tiene reservado el canal espera un SIFS y envía un CTS.
	3. El host recibe el CTS, espera un SIFS y comienza a enviar los datos.
	4. Luego de recibir los datos, e AP espera un SIFS y envía el ACK (esto es Link Layer Acknowledgement).
	5. Cualquier otro host que no haya enviado el RTS, cuando recibe un CTS pospone el envía de datos por el tiempo indicado en el CTS
	Reservar el canal ayuda a reducir colisiones pero agrega delay y consume recursos del canal; es por esto que en general solo se usa en los casos en los que el tamaño del DATA frame es mayor a cierto threshold. En muchas estaciones el tamaño de este threshold es mayor al largo máximo del frame, por lo cual la secuencia RTS, CTS se saltea para todos los frames.

### Frames 802.11

Recordemos que 802.11 es un protocolo de capa de enlace, por lo que no sabe que es una IP.
Un frame 802.11 tiene la siguiente forma:

- [ ] **INSERTAR IMAGEN DE FRAME 802.11**

Como en ethernet, el frame tiene un campo Payload y un campo CRC. En el caso de las redes inalámbricas los errores de transmisión son más comunes que en ethernet, por lo cual el CRC es más util incluso aquí.

Los frames 802.11 tienen 4 campos de direccionamiento:

- Address 1: La dirección MAC de la estación inalambrica destino: en una red en modo insfraestructura si el que envía es un host tendrá la MAC del AP y si el que envía es el AP tendrá la del host.
- Address 2: La dirección MAC del que envía el frame.
- Address 3: Si recordamos que el BSS es parte de una subred que se conecta a otras subredes a través de una interfaz de un router y cualquier mensaje que se envíá a otras subredes va destinado a esta interfaz del router. Es así que este campo contendrá la MAC de la interfaz del Router que tiene un pie en la subred del BSS.
- Address 4: Tiene uso sólo en WDS (Wireless Distribution Services).

Es así que el frame 802.11 básicamente extiende el frame ethernet para poder hacer el pasamanos entre los hosts y el router al cual esté conectado (o entre dos hosts que se envíen datagramas entre ellos).

### Mobilidad en la misma subred

Cuando un host se mueve dentro de una misma subred (un host se mueve de un BSS a otro, ambos conectados al mismo switch) 802.11 se maneja con bastante transparencia, sin requerir protocolos extra, al contrario del caso en el cual se mueve de una subred a otra.

En primer lugar el host detecta que la señal del AP del BSS1 se vuelve más débil que la del AP del BSS2 y se desasocia del primero y asocia al segundo pero como sigue en la misma subred, mantiene su IP y por lo tanto también sus sesiones TCP activas.

En cuanto al switch, el mismo utiliza la misma forma de aprendizaje que utiliza en ethernet: el AP2 envía un mensaje de broadcast con la dirección del host y al ver esto el switch aprende que el host se encuentra en dicha boca de red y actualiza su tabla de forwarding acordemente.

### Personal Area Networks

Una red 802.15.1 trabaja en un rango corto, bajo poder y bajo costo, básicamente una tecnología de reemplazo de cables. Las capas física y de enlace de 802.15.1 se basan en la especificación de bluetooth para PANs y operan en la banda de 2.4 GHz, con slots de 625 microsegundos. En cada slot de tiempo un emisor transmite en uno de 79 canales, eligiendo el mismo de una forma pseudo aleatoria; a este forma de transmisión se le denomina **FHSS** (Frequency-Hopping Spread Spectrum) y permite tazas de hasta 4Mbps.

Las redes 802.15.1 son redes ad-hoc, organizadas en lo que se llaman piconets, con tres bits de direccionamiento, por lo que solo se soportan 8 dispositivos. Uno de estos dispositivos se comporta como nodo maestro, orquestando la piconet (el tiempo en la piconet es determinado por su clock). Además de los 8 dispositivos activos pueden haber hasta 255 dispositivos "parqueados", que no pueden comunicarse hasta que el master les cambie el estado a activo.

El nodo maestro puede transmitir en los slots impares y los esclavos sólo en los pares, sólo cuando el master le habó en el slot previo y sólo al master.

Si un dispositivo de una piconet (master o slave) se conecta como esclavo a otra piconet se forma una scatternet. El nodo que pertenece a las dos redes actúa como relay entre las mismas; sin embargo, el protocolo bluetooth no maneja este relay por lo cual el software del dispositivo debe administrarlo.

## Movildad

En un entorno de red el hogar permanente de un nodo móvil es denominada **Home Network ** y la entidad que realiza las funciones de administración de movilidad en nombre del nodo es el **Home Agent**. La red en la cual se encuentra residiendo actualmente el nodo es denominada **Foreign Network** y el equivalente al **Home Agent** es el **Foreign Agent**. Finalmente, el actor que desea comunicarse con el nodo es denominado **Correspondent**.

### Direccionamiento

Es deseable que un nodo móvil mantenga su dirección cuando se mueve de una red a otra. Consideremos entonces el foreign agent: éste crea una dirección nueva para el nodo llamada **Care-of-address** (COA), ubicada en la red del FA. Es así que el nodo tiene dos direcciones: 

- Permanent address: su dirección original en su  home network.
- Foreign o care of adress: la que tiene en la foreign network. Esta puede ser una nueva dirección en la red o ser la misma del fireign agent; en general se utiliza la segunda opción.

### Routing

Hay dos acercamientos al tema del enrutamiento en redes móviles

#### Routing Indirecto

Este tipo de routing es totalmente transparente para el correspondent ya que los que se encargan de la complejidad del proceso son los agents (home y foreign).

1. El correspondent envía el datagrama a la dirección que conoce del nodo (a su permanent address).
2. El home agent recibe ese datagrama y ve que se ha registrado una COA para ese nodo. Encapsula el datagrama en uno nuevo que tiene como destino la COA del nodo y lo envía al Foreign Agent.
3. El FA recibe el datagrama que tiene como destino la COA, desencapsula el mismo y se lo envía al nodo móvil.
4. El nodo móvil responde directamente al correspondent.

En este modo el routing es totalmente transparente tanto para el correspondent como para el nodo móvil. De todos modos varias funcionalidades se van a tener que agregar a la capa de red:

- Un protocolo entre el nodo móvil y en foreign agent: para registrarse con el FA cuando se agrega a la foreign network y para desregistrarse cuando se va.
- un procololo entre los agents: para registrar la COA con el homa agent. No es necesario desregistrar la COA porque si cambia la misma, el nuevo FA va a registrar ésta en el home agent.
- un protocolo de encapsulamiento de datagramas.
- un protocolo de desencapsulamiento de datagramas.

El routing indirecto tiene el problema del routing triangular: los datagramas tienen que ir hasta el home agent y despues hasta el foreign agent aunque haya una ruta mejor entre el correspondent y el nodo móvil.

#### Routing Directo

El routing directo no tiene el problema del routing triangular pero agrega complejidad:

- Un protocolo de localización de usuario movil: para que el correspondent pueda hacer un query y obtener la COA del nodo móvil.
- Cómo se va a comportar la red cuando un nodo se mueva de una foreign network a otra?

El primer punto es simplemente un tema de especificación e implementación.

El segundo punto tiene dos posibles soluciones:

- implementar un protocolo para notificar al correspondent del cambio de COA.
- Si el nodo se mueve, el correspondent seguirá enviando los datagramas al foreign agent oficial. Pero el nodo móvil se va a haber movido a otra FN y registrado ese cambio con el FA original; es así que el FA1 va a redireccionar los paquetes que le sigan llegando al nodo a su nueva COA (cuando le llegue un paquete encapsulado lo va a reencapsular y enviar al nuevo FA).

## IP Movil

El estandar IP Movil (definido primariamente en el RFC 5944) consiste de tres partes principales:

- Agent Discovery
- Registro con el Home Agent
- Routing Indirecto de Datagramas

### Agent Discovery

Un nodo IP móvil que se mueve a una nueva red debe descubrir la identidad del home o foreign agent de la red. Hay dos formas en las que puede lograrse esto:

- Agent Advertisement: Un agente publica sus servicios usando una extensión del protocolo de descubrimiento de routers. Envía mensajes ICMP periodicos con un tipo 9 y la dirección del router para que el nodo la aprenda.
- Agent Solicitation: Un nodo puede pedir información acerca de un agente simplemente enviando un mensaje ICMP de tipo 10. Un agente que reciba ese mensaje respondera unicast con la información adecuada.

### Registro con el Home Agent

1. Un nodo cuando recibe un mensaje envía un mensaje de registro al foreign agent (datagrama UDP al puerto 434, la COA publicada, la dirección del HA, la permanent address, un tiempo de vida y un numero de identificación de registro de 64bits).
2. El FA recibe el mensaje y guarda la dirección permanente. Envía un mensaje de registro al Home agent (UDP al 434 con COA, HA, PA formato de encapsulamiento, tiempo de vida e id de registro).
3. El Home agent recibe el pedido de registro y chequea autenticidad y correctitud. Asocia la CIA a la PA y envía una respuesta conteniendo el HA, PA, tiempo de vida real e id de registro.
4. El FA recibe la respuesta y se la reenvia al nodo.
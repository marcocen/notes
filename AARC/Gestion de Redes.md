# Gestion de Red 

## Redes Multicapa

Objetivo de las redes es proveer servicios, lo otro es para soportar los servicios.
- [ ] Imagen de red multicapa

### Problemas

- Planificación
- Aprovicionamiento
	- Desperdicio de Recursos
- Fallos
	-Correlacion de alarmas

Soluciones?

- coordinacion entre capas: plano de gestion y control
- gestion integrada:
- plano de control integrado

## Roles
administrador: comoce el común de la red; cuando pasa algo que no es normal es facil identificarlo
Fabricantes y empresas proveedoras de servicios
Ambiente académico

IETF organismo tecnico de internet, responsable de definir protocolos y estándares que se utilizan en internet. Funcionamiento abierto. 

ITU International Telecommunications Unit

Los productos del universo privado no siempre funcionan en el mundo real (más allá de su calidad). Ahí es donde entra el IETF que produce estándares que sí lo hacen.

## Modelos de Gestión

- Gestión OSI
  Pocos vendors venden harwdware con soporte para este modelo
- Gestión internet
  Protocolos (SNMPV1,2 y 3 SMI y MIB-II)
SNP es el protocolo que va por la red y lleva cosas, SMI y MIBII más que un protocolo son cosas que me permiten definir a mi un arbol de objetos interesantes para mi realidad.

Los protocolos de SNMP son de más Bajo Nivel. Es un simple protocolo que intenta monitorear valores de cosas, los llamados **Elementos** (un número, un string).
Lo que puedo obtener con SNMP son valores o estados de un elemento (no tiene porque ser un dispositivo/equipo, puede ser más fino) y nada más. Un comportamiento es devolver la memoria libre de un equipo, es un número objetivo; devolver por ejemplo si el equipo tiene la memoria baja no es el espíritu de SNMP.
Tiene primitivas de acceso poco poderosas (relacionado al ejemplo anterior) sólo permiten preguntar el valor de algo o recibir alertas frente a eventos raros de la red (un equipo con el CPU al 100%, por ejemplo).

Modelos de referencia orientados a los objetos de la red

- Áreas funcionales
	Modelo ISO de la gestión de la red
    **FCAPS** 
    - Fault Management
    - Configuration and Naming Managment:
    	Cuando aumenta la complejidad se vuelve necesario centralizar la configuración de los diferentes sistemas. Tener la posibilidad de cambiar un equipo que fallo, y hacerlo rápido (por ejemplo tener una especificación de hardware unificado y tener el software que corre sobre éste en una imagen)
    - Accounting Managment:
		Es importante llevar cuenta de la utilización de recursos. Generalmente es el que hace la gestión de Billing.
    - Performance Managment:
    	Identificar efectivamente el nivel de uso de recursos, cúanto tengo disponible, si hay equipos muy exigidos, etc.
        Mezclando con los negocios: SLA (Service Leven Agreement), parte del contrato es mantener cierto nivel de servicio.
    - Security Managment:
    	- Monitorización de claves, usuarios, etc.
        - Supervisión de acceso a equipos, aplicaciones.
        - Detección de intrusos
        - Backups
        Se ha convertido en una función crítica del sistema.
        En esta área es poco lo que puede ayudar SNMP. Se pasa a utilizar soluciones distintas: comprar productos de backup, un IDS, scripts, etc.
- TMN
	Pirámite TMN:
    - Capa de objetos físicos
   	- administración de objetos
    - administración de red <-- Hasta aquí es hasta donde podemos aspirar a llegar
    - Servicios (por ejemplo si soy un ISP el servicio puede ser un ADSL)
    - Negocio (entran un montón de cosas: dinero, contratos de nivel de servicios, etc)

## Paradigma Agente-Gestor

**Agente** es el que tiene configurado un software que es capaz de recibir pedidos-ordenes, consultar en una base de datos (ver que elementos me estan pidiendo) y devuelve ese elemento. A veces puede generar notificaciones sin que el gestor se las pida.

**Gestor** es el encargado, generalmente, de mandar esos pedidos a los agentes. En casos típicos tendré un gestor y muchos agentes, aunque esto puede cambiar manteniendo que la proporción de agentes a gestores es muy grande.
Normalmente tiene algún tipo de interfaz de usuario, inteligencia de gestión.
La interoperación entre gestores no es necesariamente un hecho.

### Modelo de información

El agente y el gestor no necesariamente son del mismo fabricante/versión

- ASN1 Lenguaje que nos permite definir objetos y estructuras. Reglas para la transmisión por la red.
- SMI (Structure of Managment Information) Basado en ASN1 pero simplifica estructuras de la base de datos de gestión
- MIB (Managment Information Base) Son los elementos que viven en la base de datos y que el agente sabe que puede chequear sus estado y responder cuando el gestor le pregunta; cada uno de estos elementos es denominado objeto.


## SNMP

Protocolo SNMO usa puertos UDP 161 (para comunicación entre el gestor y el agente) y 162 (para los traps SNMP).

### OID

Forma de identificar todo posible objeto estándar para cada estándar de red.
OID: ISO Object Identifier tree. Estructura jerárquica  donde cada rama tiene un nombre un número. Ejemplo: 1.3.6.1.2.1.7.1
1 ISO
3 Iso-ident. Org.
6 US DoD
1 Internet
2 Management
1 MIB2
7 UDP
1 udpInDatagrams

### Presentación de los datos

La copia exacta de los datos de la memoria de origen a la memoria del destino solucioina el problema de comunicación? NO ES TAN FACIL, AMIGO!! Un host puede utilizar big endian y el otro little endian y ahí rompo todo.
Se usa BER (Basic Encoding Rules), una serie de reglas que especifica cómo se envían los valores por la red de modo que ambos host estén de acuerdo en cuanto al formato que utilizarán. Cada objeto transmitido tiene una codificación del tipo TLV (Type Length Value).

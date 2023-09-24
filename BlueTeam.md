
# Creación de una VPN

## Marco Teórico
### Redes y Seguridad Informática

La seguridad de la información es una preocupación crítica en la actualidad. La creación de una VPN (Virtual Private Network) es una estrategia fundamental para garantizar la confidencialidad, integridad y disponibilidad de los datos en una red. En este proyecto, se busca conectar tres redes diferentes, cada una con un propósito específico: LAN, DMZ y DMZ_2.

### PFsense como Firewall y Router

PFsense es una solución de código abierto que combina las funcionalidades de un firewall y un router. En este proyecto, PFsense se utilizará como un bridge para interconectar las tres redes mencionadas, lo que permitirá un flujo controlado y seguro de datos entre ellas.

### Propósito de las Redes

- **LAN (Local Area Network)**: La red LAN es la red interna de la organización. En este proyecto, se empleará una máquina Kali Linux para realizar comandos y gestionar interacciones dentro de la LAN.

- **DMZ (Demilitarized Zone)**: La DMZ es una zona intermedia que se encuentra entre la red interna (LAN) y la red externa (Internet). En este proyecto, se alojará una máquina Parrot OS con un sistema ELK (Elasticsearch, Logstash y Kibana) para monitorear honeypots, detectando posibles amenazas y actividades sospechosas.

- **DMZ_2**: Otra DMZ se utilizará para alojar un honeypot en una máquina Kali Linux 2. Un honeypot es un sistema diseñado para atraer ataques y registrar información sobre las tácticas utilizadas por los atacantes.

## Metodología de Implementación
### Creación de UTM [Implemetancion de PFsense]
> #### Requisitos:
> - VirtualBox or VMWare
> - Imagen pfsense.iso


1) Seleccionamos la opción **"New"** en VirtualBox, se desplegara una ventana donde agregaremos:
   - Name: UTM-001
   - Folder: C:/MV/...
   - ISO Image: **pfsense.iso**
   - Type (OS): BSD
   - Version: FreeSD (64bit)
    <kbd> ![Virtual machine Name and Operating System](/img/utm_create.png) </kbd>
   Seleccionamos  **"Next"**

2) Se desplegará una ventana con las opciones de **"Hardware"**, donde seleccionaremos:
   - Base: 2048MB [En función de la memoria RAM de nuestra maquina sera la cantidad]
   - Processors: 2
    <kbd> ![Hardware](/img/utm_hardware.png) </kbd>
   Seleccionamos  **"Next"**

3) Se desplegará una ventana con las opciones **"Virtual Disc"**:
   - Size disc: 25GB
     <kbd> ![Virtual Hard disk ](/img/utm_virtual_hard_disk.png) </kbd>

   Seleccionamos  **"Next"**
4) Se desplegará una ventana **"Summary"**:
   Seleccionamos  **"Finish"**
   <kbd> ![Virtual Hard disk ](/img/utm_summary.png) </kbd>

### Configuración de VM pfSense
1) Seleccionamos **Start** en VirtualBox, se desplegara la instalación de pfSense
<kbd> ![pfSense installation 1](/img/pfSense_start.png)</kbd>
    Seleccionamos **"Accept"**
2) Se desplegara el menú pfSense Installer con opciones,seleccionamos la opción **Install pfSense** 
<kbd> ![pfSense start](/img/pfSense_install.png)</kbd>
    Seleccionamos **"Install    Install pfSense"** ➜ **"OK"**

3) Se desplegara el menú pfSense Partitioning con opciones,seleccionamos la opción **Auto (ZFS)** 
<kbd> ![pfSense partitioning](/img/pfSense_partition.png)</kbd>
    Seleccionamos **"Auto (ZFS)    Guided Root-on-ZFS"** ➜ **"OK"**

4) Se desplegara el menú pfSense ZFS Configuration con opciones, seleccionamos la opción **Install** 
<kbd> ![pfSense ZFS Config](/img/pfSense_zfs_conf.png)</kbd>
    Seleccionamos **"Install    Proceed with Installation"** ➜ **"Select"**

5) Se desplegaran los tipos de Virtual Device
<kbd> ![pfSense virtual device](/img/pfSense_virt_dev.png)</kbd>
    Seleccionamos **"stripe    Stripe - No Redundancy"** ➜ **"OK"**

6) Se desplegaran el disco, haremos click en la spacebar para seleccionar la opción
<kbd> ![pfSense select disk](/img/pfSense_select_d.png)</kbd>
    Seleccionamos **"ad0    VBOX HARDDISK"** ➜ **"OK"**

8) Se desplegara un mensaje preguntando si estamos dispuestos a destruir la información del disco
<kbd> ![pfSense destroy](/img/pfSense_dest.png)</kbd>
Seleccionamos **"YES"**

A continuación empezará el proceso
<kbd> ![pfSense process destroy](/img/pfSense_dest_2.png)</kbd>

9) Se desplegara un mensaje preguntando si queremos reiniciar
<kbd> ![pfSense reboot](/img/pfSense_reboot.png)</kbd>
Seleccionamos **"Reboot"**

> Después de seleccionar Reboot procederemos a borrar la imagen .iso de la maquina virtual con el propósito de que no se corra el proceso de manera infinita
> <kbd> ![pfSense vbox turnoff](/img/pfSense_reboot_2.png)</kbd>

> En VirtualBox seleccionaremos Settings de la maquina virtual de pfSense -> Storage y seleccionaremos la imagen .ison
> <kbd> ![pfSense delete iso](/img/pfSense_reboot_3.png)</kbd>

>Seleccionamos Remove Attachment y la opción Remove
> <kbd> ![pfSense remove attachment](/img/pfSense_reboot_4.png)</kbd>

>Cerramos el cuadro seleccionando "OK"

### Configuración de la Red
1) En VirtualBox entraremos a Seetings de la maquina virtual de pfSense ➜ Network, aplicaremos las siguientes configuraciones a cada adptador (Se habilitarán todos los adaptadores ☑️ Enable Network Adapter):
    - Adapter 1 ➜ **Bridge Adapter**
    <kbd> ![pfSense network adapter 1](/img/net_ad1.png)</kbd>
    - Adapter 2 ➜ **Internal Network** y la nombraremos **LAN**
    <kbd> ![pfSense network adapter 2](/img/net_ad2.png)</kbd>
    - Adapter 3 ➜ **Internal Network** y la nombraremos **DMZ**
    <kbd> ![pfSense network adapter 3](/img/net_ad3.png)</kbd>
    - Adapter 4 ➜ **Internal Network** y la nombraremos **DMZ_2**
    <kbd> ![pfSense network adapter 4](/img/net_ad4.png)</kbd>

Cerramos el cuadro seleccionando "OK"

Una vez hecha la confiuración al correr la maquina de pfSense veremos lo siguiente:
 <kbd> ![pfSense network running](/img/net_pfsense.png)</kbd>

### Configuración LAN en Kali Linux
1) Después de montar la maquina de Kali Linux cambiaremos su configuración de red selccionando una de las que agregamos en pfSense (LAN).
<kbd> ![KaliLinux Machine LAN](/img/kali_net.png)</kbd>
Seleccionamos **"OK"**

### Configuración pfSense
Mientras corre la maquina virtual de pfSense, correremos la maquina virtual de Kali Linux (**LAN**):

Una vez corriendo, ingresaremos a la IP LAN que nos mostraba la maquian de pfSense (**192.169.1.1**)
<kbd> ![pfSense network running](/img/pfc_1.png)</kbd>

Haremos el login usando:
    - Username: admin
    - Password: pfsense

Se nos llevara de inicio al mensaje de bienvenida de pfSense y daremos "**Next**" hasta llegar a la vista "General Information".

En "General Information" agregaremos los siguientes cambios:

- Hostname: UTM [El nombre puede ser el que deseemos]
- Domain: Keepcoding.local [El nombre puede ser el que deseemos]
- Primary DNS Server: 127.0.0.1
- Secondary DNS Server: 1.1.1.1

Seleccionamos ➜ "*Next*"

<kbd> ![pfSense pfsense general info](/img/pfc_2.png)</kbd>

Pasaremos a la siguiente vista "Time Server Information", donde seleccionaremos la zona horaria y seleccionamos "*Next*".

<kbd> ![pfSense pfsense timezone](/img/pfc_3.png)</kbd>

Pasaremos a la siguiente vista "Configure WAN Interface", donde iremos hasta abajo de la pagina para configurar de la siguiente manera:

- (Disable) RFC1918 Networks ➜
    ☐ Block private networks from entering via WAN
- (Disable) Block bogon networks ➜
    ☐ Block non-Internet routed networks from entering via WAN

Seleccionamos ➜ "*Next*"

<kbd> ![pfSense WAN Interface](/img/pfc_4.png)</kbd>

Pasaremos a la siguiente vista "**Configure LAN Interface**", donde configuramos de la siguiente manera:
- LAN IP Adress: 192.168.100.1
- Subnet Mask: 24

Seleccionamos ➜ "*Next*"

<kbd> ![pfSense LAN Interface](/img/pfc_4.png)</kbd>

Pasaremos a la siguiente vista "**Set Admin WebGUI Password**", donde agregaremos la password de pfSense:

- [Contraseña que mejor nos parezca]

Seleccionamos ➜ "*Next*"

Se nos pedira recargar la pagina así que seleccionamos "*Reload*"

<kbd> ![pfSense reload configuration](/img/pfc_5.png)</kbd>
Una vez completado el reinciio se nos redirigirá.

Para corroborar el cambio de ip iremos a la terminal de KaliLinux y escribiremos el siguiente comando:
```bash
ifconfig eth0 # Verificar nueva dirección ip asignada al interfaz
```
>En caso de que el cambio no se vea reflejado deberemos >desconectarnos de la red desde la maquina de KaliLinux, >reestablecer la conexión y volver a insertar el comando.

<kbd> ![pfSense terminal ip](/img/pfc_6.png)</kbd>

8) Una vez recargado el pfSense con los cambios realizados visualizaremos la vista **Status/Dashboard**

<kbd> ![pfSense Status/Dashboard](/img/pfc_7.png)</kbd>

### Configuración de Servidores DNS
#### DNS Resolver
Seleccionaremos en el menú Services ➜ DNS Resolver General Settings 
Donde escribiremos En Listen Port el 53. Seleccionamos Save en la parte final de la página.

<kbd> ![pfSense Services/DNS Resolver/General Settings](/img/pfc_8.png)</kbd>

Aplicaremos los cambios.

<kbd> ![pfSense Services/DNS Resolver/General Settings Apply](/img/pfc_9.png)</kbd>

#### DHCP Server
Seleccionamos en el menú **Services** ➜ **DHCP Server** ➜ **LAN**

<kbd> ![pfSense Services/DHCO Server/ LAN Apply](/img/pfc_10.png)</kbd>

Haciendo scroll down en la página modificaremos de la siguiente manera:

- Range ➜ **From:192.168.100.100 To:192.168.100.200**
    
<kbd> ![pfSense Services/DHCO Server/ LAN Apply](/img/pfc_11.png)</kbd>

Haciendo scroll down en la página modificaremos Servers:
<kbd> ![pfSense Servers](/img/pfc_12.png)</kbd>

Haciendo scroll down en la página modificaremos **Other Options** y seleccionamos "*Save*":
<kbd> ![pfSense Other options](/img/pfc_13.png)</kbd>

#### Interfaces
A continuación declararemos todas las tarjetas de red que montamos.

En el menú seleccionamos **Interfaces** ➜ **Assignments**

<kbd> ![pfSense Interfaces](/img/pfc_14.png)</kbd>
Seleccionamos "*Save*"

#### Personalización de Interfaces
Iremos a **Interfaces** y seleccionamos cada interfaz agregada, en el primer caso:
- Enable
- Description: DMZ    
- IPv4 Configuration Type: Static IPv4

<kbd> ![pfSense Interfaces](/img/pfc_15.png)</kbd>

Scroll down y seleccionamos **Static IPv4 Configuration**:
- IPv4 Address: 192.168.200.1   /24

<kbd> ![pfSense Static IPv4 Configuration](/img/pfc_16.png)</kbd>

Seleccionamos "*Save*" ➜ **Apply Changes**.

Para el segundo caso:
- Enable
- Description: DMZ_2
- IPv4 Configuration Type: Static IPv4

Scroll down y seleccionamos Static IPv4 Configuration:
- IPv4 Address: 192.168.250.1   /24

<kbd> ![pfSense Interfaces OPT2](/img/pfc_17.png)</kbd>

Seleccionamos "*Save*" ➜ **Apply Changes**.

### Configuración de Firewall
Entraremos a **Firewall** ➜ **Aliases** ➜ **Ports** y en la opción add nos desplegará la siguiente vista donde agregaremos los puertos *443* y *80* como regla para navegar en internet.

<kbd> ![pfSense Rule webs](/img/pfc_18.png)</kbd>

Seleccionamos "*Save*" ➜ **Apply Changes**.

A continuación agregaremos las reglas para los componetes de la red.Empezando por DMZ.

Iremos a **Firewall** ➜ **Rules** y entraremos en la pestaña de DMZ.

<kbd> ![pfSense DMZ rule](/img/pfc_19.png)</kbd>

Daremos click en *Add* (Add top of the list) donde se desplegará la interfaz para crear la regla y agregaremos la siguiente información correspondiente.
- Protocol: TCP/UDP
- Source: DMZ net

<kbd> ![pfSense DMZ rule a](/img/pfc_23.png)</kbd>

- Destination: Aunque es posible agregar cualquier puerto agregaremos el que hemos creado anteriormente (**webs**)
    
<kbd> ![pfSense DMZ rule a](/img/pfc_21.png)</kbd>
    
Seleccionamos "*Save*" ➜ **Apply Changes**.

Una vez agregada procedemos a agregar una regla para el DNS.

Daremos click en *Add* (Add bottom of the list) donde se desplegará la interfaz para crear la regla y agregaremos la siguiente información correspondiente.
- Protocol: UDP (No orientado a la conexión)
- Destination Port Range: DNS 53

<kbd> ![pfSense DMZ rule b](/img/pfc_22.png)</kbd>
    
Seleccionamos "*Save*" ➜ **Apply Changes**.

Una vez habilitadas las reglas para las webs y DNS podremos ser capaces de acceder a internet de manera regular en DMZ
Añadiremos una regla mas para habilitar el envio con ping desde la terminal, con la siguiente configuración.

- Protocol: ICMP
- ICMP Subtypes: any
- Source: DMZ net

<kbd> ![pfSense DMZ rule b](/img/pfc_26.png)</kbd>
     
Seleccionamos "*Save*" ➜ **Apply Changes**.

Una vez hecho esto podremos ver en la maquina DMZ lo siguiente al enviar el comando `ping 1.1.1.1` por la terminal.

<kbd> ![ping in DMZ](/img/pfc_25.png)</kbd>

### Configuración DMZ_2

Para DMZ_2  iremos a **Services** ➜ **DHCP Server** ➜ **DMZ_2** y colocaremos las siguientes configuraciones.
- Range: 192.168.250.100 - 192.168.250.150
- DNS Servers: 192.168.250.1, 1.1.1.1, 8.8.8.8
    
<kbd> ![pfSense DMZ rule c](/img/pfc_28.png)</kbd>
    
<kbd> ![pfSense DMZ rule c](/img/pfc_29.png)</kbd>
    
Seleccinamos "*Save*".

Añadiremos ahora una IP estatica. 
Iremos a **Status** ➜ **DHCP Leases**, y volveremos estatica la IP de la maquina DMZ_2.

Seleccionamos la casilla add blanca.

<kbd> ![pfSense DMZ rule b](/img/pfc_30.png)</kbd>

Después de selccionar esa opción nos llevará a la vista **Edit Static Mapping** donde configuraremos de la siguiente manera.

- IP Address: 192.168.250.250
- Hostname: Honeypot
- Description: Honeypot machine
- ARP Table Static Entry: ☑️

<kbd> ![pfSense DMZ_2 static ip](/img/pfc_34.png)</kbd>

Seleccionamos "*Save*" ➜ **Apply Changes**.

Si nos dirijimos a **DHCP_Server** ➜ **DMZ_2**, estara la configuración efectuada.

<kbd> ![pfSense DMZ_2 confirmation static ip](/img/pfc_35.png)</kbd>

Para corroborar el cambio iremos a la terminal de la maquina DMZ_2 y correremos el comando `ip a` para poder ver los cambios, donde estará la ip recien configurada **`192.168.250.250`**.

### Configuración de HoneyPot
Accederemos al menú **Firewall** ➜ **NAT** y seleccionaremos Port Forward donde pulsaremos el boton "*Add*".

Donde usaremos las siguientes especificaciones:

- Destination port range: 80 - 80
- Redirect target IP: ***.
- Redirect target port: 80

Como honeypot montaremos un servicio de Apache.
Además tendremos otro honeypot; correremos una instancia de Cowrie la cual monitoreara los intentos de login y registrara en un log el que intento el usuario.

    docker run -p 2222:2222 cowrie/cowrie:latest

Esto descargará la instancia de Cowrie y la pondrá a correr en el puerto 2222.

Despues correremos Cowrie en un servicio SSH de la siguiente manera:

    ssh root@localhost -p 2222 

<kbd> ![Honeypot config](/img/hon_1.png)</kbd>

Desde aquí todo lo que se haga sera registrado en el log como intento de acceso y por ende monitoreado.

### Configuración de ELK (Elastic Search, Logstash & Kibana)

Instalaremos Elasticsearch en docker en nuestro equipo DMZ por lo que primero clonaremos el repositorio:

`git clone https://github.com/deviantony/docker-elk.git`

    `cd docker-elk`

Una vez en el directorio ejecutaremos:

    `docker-compose up setup`

Esto creará los contenedores necesarios para ElasticSearch. Una vez terminado este proceso iniciaremos todos los servidores

<kbd> ![ELK run](/img/elk_3.png)</kbd>

    `docker-compose up -d`

<kbd> ![ELK run](/img/elk_4.png)</kbd>

Verificaremos si se ha instalado correctamente con 

    curl -u <USERNAME>:<PASSWORD> http://localhost:9200/

Visitaremos http://localhost:5601/ donde accederemos a Kibana con las credenciales:

    User: `elastic` Password `changeme`

<kbd> ![ELK run](/img/elk_5.png)</kbd>

### Filebeat

Usaremos **Filebeat** como agente para recoger los logs de los servicios

Para instalarlo usaremos los siguientes comandos:

```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-archive-keyring.gpg

sudo apt-get install apt-transport-https

echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list

sudo apt-get install filebeat
```

<kbd> ![Instalation filebeat](/img/elk_6.png)</kbd>
<kbd> ![Instalation filebeat](/img/elk_7.png)</kbd>
<kbd> ![Instalation filebeat](/img/elk_8.png)</kbd>

Después modificaremos filebeat.yml para configurarlo de la siguiente manera:

    sudo nano /etc/filebeat/filebeat.yml

```yaml
```
<kbd> ![Config filebeat](/img/fb_1.png)</kbd>
<kbd> ![Config filebeat](/img/fb_2.png)</kbd>

Una vez configurado arracamos el servicio con:
```bash
sudo systemctl enable filebeat
```
<kbd> ![Enable filebeat](/img/elk_9.png)</kbd>

Iniciamos el servicio con:
```bash
sudo systemctl start filebeat
```
Para corroborar que Filebeat esta funcionando ejecutamos:

    sudo systemctl status filebeat

<kbd> ![Enable filebeat](/img/elk_10.png)</kbd>

### Conexión Honeypots - ELK

Para poder ver los registros en Kibana de nuestra servicio  *Apache* tenemos que:

Añadir la integración con apache de Elasticsearch. Para ello nos situamos en el menú de inicio "**Home**" y seleccionamos "**Add integrations**".

<kbd> ![Enable filebeat](/img/elk_11.png)</kbd>

En el filtro escribimos apache y elegimos "**Apache HTTP Server**".

<kbd> ![Enable filebeat](/img/elk_12.png)</kbd>

Hacemos clic en "**Add Apache HTTP Server**".

<kbd> ![Enable filebeat](/img/elk_13.png)</kbd>

Para añadir el agente seguiremos las instrucciones que aparecen en "**Run standalone**".

<kbd> ![Enable filebeat](/img/elk_14.png)</kbd>

Ejecutaremos lo siguientes comandos después de descargar el agente desde la página de elasticsearch.

    tar xzvf elastic-agent-8.10.2-linux-x86_64.tar.gz

    cd elastic-agent-8.10.2-linux-x86_64
    
    sudo ./elastic-agent install

<kbd> ![Installed ELK Agent](/img/elk_16.png)</kbd>

Copiamos el contenido de la configuración y la pegamos en el archivo *elastic-agent.yml*.

<kbd> ![Copy Agent info](/img/elk_15.png)</kbd>

    nano elastic-agent.yml

<kbd> ![Edit elastic-agent.yml](/img/elk_17.png)</kbd>

Ponemos las credenciales del usuario que estamos usando `username:elastic  password:changeme` y guardamos el archivo.

Reiniciamos el servicio.

<kbd> ![Restart elastic-agent](/img/elk_18.png)</kbd>

Para visualizar datos en kibana tendremos primero que crear el data view de uno de los índices recibidos a través de los agentes. 
Deberemos ir a "**Stack Management**" ➜ "**Kibana**" ➜ "**Data Views**" ➜ "**Create data view**". 

Asignamos un nombre al data view y escribimos el index pattern correspondiente.

Una vez en el apartado de dashboards, elegimos el nuestro.

Ya podemos visualizar los gráficos recogidos para apache, pudiendo modificar los datos de interes de la manera que necesitemos.

Para poder ver los registros en Kibana de nuestra maquina DMZ_2 que tiene corriendo nuestro honeypot *Cowrie* tenemos que:

Crear un índice de patrones en Kibana para los índices de Filebeat. Iremos a "**Management**" ➜ "**Index Patterns**" y configuraremos un patrón que coincida con los nombres de los índices de Filebeat. Esto permite a Kibana reconocer los datos. 

>**Por defecto, Filebeat crea índices diarios (por ejemplo, filebeat-7.15.0-2022.09.20), donde la fecha es parte del nombre del índice.**

Luego, vamos a la sección "**Discover**" en Kibana para explorar y buscar los registros. Podemos utilizar consultas de búsqueda para filtrar los registros y verlos en formato tabular.

>Desde aquí, es posible crear visualizaciones personalizadas, gráficos y paneles de control en Kibana para representar los datos de una manera significativa.

Finalmente, se pueden organizar estas visualizaciones en tableros (dashboards) para obtener una vista consolidada de los registros y su análisis.

## Consideraciones de Seguridad

La seguridad desempeña un papel fundamental en la implementación de esta infraestructura de VPN y la gestión de honeypots. A continuación, se detallan algunas consideraciones clave:

#### Reglas de Firewall y Políticas de Seguridad
La configuración de las reglas de firewall en PFsense debe ser exhaustiva y precisa. Esto incluye permitir el tráfico necesario para el funcionamiento de las redes LAN, DMZ y DMZ_2, al mismo tiempo que se bloquean todos los demás accesos no autorizados. Se debe revisar regularmente y actualizar las políticas de seguridad para mantenerse al día con las amenazas emergentes.

#### Actualizaciones de Software y Parches
Es esencial mantener tanto el sistema operativo de PFsense como las aplicaciones y servicios utilizados en las redes actualizados con las últimas versiones y parches de seguridad. Las vulnerabilidades no parcheadas pueden ser explotadas por atacantes, lo que hace que las actualizaciones regulares sean cruciales.

#### Monitoreo de Honeypots
La DMZ aloja honeypots diseñados para atraer ataques. Es fundamental monitorear continuamente estas máquinas para registrar cualquier actividad sospechosa o intentos de intrusión. El uso de herramientas de análisis de registros y alertas permitirá una respuesta inmediata ante posibles amenazas.

#### Acceso y Autenticación
Se deben establecer políticas de acceso y autenticación sólidas para garantizar que solo usuarios autorizados tengan acceso a la red. El acceso a la infraestructura de VPN debe requerir autenticación de múltiples factores y seguir las mejores prácticas de gestión de contraseñas.

#### Copias de Seguridad y Plan de Recuperación ante Desastres
Mantener copias de seguridad regulares de la configuración de PFsense y los datos críticos es esencial para la continuidad del negocio. Además, es importante tener un plan de recuperación ante desastres en caso de fallos de hardware o incidentes de seguridad que puedan interrumpir las operaciones.

#### Auditorías de Seguridad
Realizar auditorías de seguridad periódicas ayuda a identificar posibles vulnerabilidades y evaluar la eficacia de las políticas de seguridad.


## Futuras Mejoras & Recomendaciones
#### ☁️ Migración Cloud
Si bien este proyecto se ha implementado en un entorno local, se debe considerar la migración de las configuraciones actuales a un entorno en la nube como AWS, Azure o Google Cloud. La adopción de una infraestructura en la nube brinda ventajas clave, como escalabilidad, redundancia y accesibilidad global. Además, permitirá una gestión centralizada y más eficiente de la infraestructura de VPN y honeypots. Se recomienda llevar a cabo un análisis de costos y requerimientos antes de proceder con la migración.

#### 🍯 Repertorio de Honeypots
Para mejorar la eficacia de la detección de amenazas y el conocimiento sobre las tácticas de los atacantes, es esencial diversificar el repertorio de honeypots. Se puede considerar la incorporación de una variedad de honeypots, como servicios web falsos, sistemas de control industrial falsos y sistemas de archivos compartidos falsos, entre otros. Esto proporcionará una visión más completa de las técnicas y tendencias utilizadas por los atacantes en la actualidad.


#### ☸ Orquestación Docker + Kubernetes 
La gestión de múltiples honeypots puede volverse compleja con el tiempo. Para abordar este desafío, se recomienda la implementación de un orquestador como Kubernetes para administrar eficientemente los honeypots alojados en contenedores de Docker. Esto simplificará la implementación, escalabilidad y actualización de los honeypots, al tiempo que mejorará la capacidad de respuesta ante eventos de seguridad. Además, la orquestación permitirá automatizar tareas como la creación, el monitoreo y la destrucción de honeypots según sea necesario.

#### ℹ️ Soluciones de Seguridad Adicionales

A medida que evoluciona la infraestructura y se amplía el repertorio de honeypots, se debe considerar la implementación de soluciones de seguridad adicionales, como sistemas de detección de intrusiones (IDS) y sistemas de prevención de intrusiones (IPS). Estas soluciones pueden ayudar a identificar y mitigar amenazas de manera más proactiva, complementando la función de los honeypots como sistemas de detección.

## Conclusiones

La implementación de una infraestructura de VPN utilizando PFsense como bridge y la configuración de redes LAN, DMZ y DMZ_2 ha demostrado ser una medida efectiva para garantizar la seguridad de la información y el monitoreo de amenazas en un entorno de red. Este proyecto ha brindado una sólida base para la gestión segura de datos y la detección de posibles amenazas cibernéticas en las redes internas y en la zona desmilitarizada (DMZ).

A medida que avanzamos en el proyecto, se han identificado consideraciones importantes para su mejora y expansión futura. La posible migración a un entorno en la nube ofrece oportunidades para aprovechar los beneficios de la escalabilidad, la redundancia y la accesibilidad global, lo que contribuiría a la eficiencia y la continuidad operativa.

La diversificación del repertorio de honeypots proporciona una visión más completa de las técnicas y tendencias utilizadas por los atacantes, lo que es esencial para mantenerse al tanto de las amenazas cibernéticas en constante evolución. Además, la implementación de un orquestador como Kubernetes para la gestión de múltiples honeypots alojados en contenedores de Docker simplifica la administración y la escalabilidad, mejorando la capacidad de respuesta ante eventos de seguridad.

Este proyecto representa un paso importante hacia la protección de la información crítica y la identificación proactiva de amenazas en el entorno de red. Las mejoras y consideraciones futuras mencionadas aquí servirán como una hoja de ruta para fortalecer aún más la seguridad y la eficiencia de la infraestructura de VPN y honeypots a medida que evoluciona y se expande en el futuro.
# Simulacion

## Parte 1

**1.1** El largo de la dirección IP 136.155.0.2 es de 32 bits.

**1.2** Dirección IP origen: 190.190.1.X (Con X el que le asigne el DHCP, en este caso, 190.190.1.2)

**1.3** Dirección IP origen: al igual que la anterior, la dirección IP de origen es 190.190.1.X (PC de Alice, en este caso, 190.190.1.2)

**1.4 PROCESO DE ENTRADA (Cuando el Servidor DNS (`136.155.0.2`) recibe el ICMP Echo Request de PC Alice)** 

El paquete llega a la interfaz de red del Servidor DNS (FastEthernet0) a través del SwitchDNS. El último router que manejó el paquete antes de entrar a la LAN del DNS fue el "router gateway de la red DNS".

1.  **Capa 1: Física (Entrada)**
    *   La tarjeta de red (NIC) del Servidor DNS recibe las señales eléctricas/ópticas del cable Ethernet (conectado al SwitchDNS) que representan los bits de la trama.
    *   Convierte estas señales nuevamente en una secuencia de bits.

2.  **Capa 2: Enlace de Datos (Entrada)**
    *   La NIC ensambla los bits en una **trama Ethernet**.
    *   **Verifica la Dirección MAC de Destino:** Comprueba que la dirección MAC de destino en la trama Ethernet coincide con la dirección MAC de la NIC del Servidor DNS. (El "router gateway de la red DNS" habría puesto la MAC del ServerDNS como destino en la trama al reenviar el paquete a la LAN del DNS).
    *   **Verifica Errores (FCS):** Calcula el Frame Check Sequence y lo compara con el recibido. Si no coinciden, la trama se descarta.
    *   Si la MAC es correcta y no hay errores, **desencapsula el paquete IP** de la trama Ethernet (se eliminan el encabezado y el tráiler de Ethernet).
    *   El paquete IP resultante se pasa a la Capa de Red.

3.  **Capa 3: Red (Entrada)**
    *   Recibe el paquete IP de la Capa de Enlace.
    *   **Verifica Encabezado IP:**
        *   Confirma la versión (IPv4).
        *   Verifica la longitud del encabezado (IHL).
        *   Calcula y compara el checksum del encabezado IP. Si no coincide, el paquete se descarta.
    *   **Verifica la Dirección IP de Destino:** Confirma que la IP de destino del paquete (`136.155.0.2`) es la del Servidor DNS. Como sí lo es, el servidor acepta el paquete para su procesamiento.
    *   **Examina el campo Protocolo:** En el encabezado IP, este campo será `1` (o `0x01`), indicando que la carga útil del paquete IP es un mensaje **ICMP**.
    *   **Desencapsula el mensaje ICMP** del paquete IP (se elimina el encabezado IP).
    *   El mensaje ICMP se pasa al manejador de protocolo ICMP del sistema operativo.

4.  **Procesamiento ICMP (Actuando en la Capa de Red o como un protocolo auxiliar de esta)**
    *   Recibe el mensaje ICMP.
    *   **Identifica el Tipo de Mensaje ICMP:** Será un **ICMP Echo Request (Tipo 8)**, ya que la "Parte 1" de la tarea especifica un "Simple PDU" (que en Packet Tracer es un ping).
    *   Extrae los datos (payload), el identificador y el número de secuencia del Echo Request.
    *   El sistema operativo determina que debe generar una respuesta (ICMP Echo Reply).

**PROCESO DE SALIDA (Cuando el Servidor DNS (`136.155.0.2`) envía el ICMP Echo Reply a PC Alice)**

El Servidor DNS ahora prepara y envía la respuesta.

1.  **Generación del Mensaje ICMP (Actuando en la Capa de Red o como un protocolo auxiliar de esta)**
    *   Se construye un mensaje **ICMP Echo Reply (Tipo 0)**.
    *   Este mensaje incluirá:
        *   El mismo identificador y número de secuencia del Echo Request recibido.
        *   Los mismos datos (payload) que venían en el Echo Request.
    *   Este mensaje ICMP está destinado a la dirección IP origen del Echo Request (la IP de PC Alice).

2.  **Capa 3: Red (Salida)**
    *   Recibe el mensaje ICMP Echo Reply para ser enviado.
    *   **Encapsula el mensaje ICMP en un nuevo paquete IP:**
        *   **Dirección IP de Origen:** `136.155.0.2` (la IP del Servidor DNS).
        *   **Dirección IP de Destino:** La IP de PC Alice.
        *   **Campo Protocolo:** Se establece a `1` (ICMP).
        *   Se establece un TTL (Time To Live) inicial (ej. 64 o 128).
        *   Se calcula y se inserta el **checksum del encabezado IP**.
        *   Otros campos del encabezado IP se completan según sea necesario.
    *   **Decisión de Enrutamiento:** El Servidor DNS consulta su tabla de enrutamiento. Dado que la IP de destino (PC Alice) está en una red diferente, el Servidor DNS determinará que el paquete debe ser enviado a su **router gateway predeterminado** (el "router gateway de la red DNS").
    *   El paquete IP, junto con la dirección IP del siguiente salto (la IP del "router gateway de la red DNS"), se pasa a la Capa de Enlace.

3.  **Capa 2: Enlace de Datos (Salida)**
    *   Recibe el paquete IP y la información del siguiente salto (la IP del gateway de la red DNS).
    *   **Resolución de Dirección MAC (ARP):** El servidor necesita la dirección MAC de la interfaz de su router gateway.
        *   Si la MAC del gateway ya está en la caché ARP del servidor, la usa.
        *   Si no, el servidor enviará una **solicitud ARP** a la red local (LAN del DNS) preguntando "¿Quién tiene la IP [IP del gateway de la red DNS]? Decidme vuestra MAC". El gateway responderá con su MAC.
    *   Una vez obtenida la MAC del gateway, **encapsula el paquete IP en una nueva trama Ethernet:**
        *   **Dirección MAC de Origen:** La dirección MAC de la NIC del Servidor DNS.
        *   **Dirección MAC de Destino:** La dirección MAC de la interfaz del "router gateway de la red DNS".
        *   **Campo EtherType:** Se establece a `0x0800` para indicar que la carga útil es un paquete IPv4.
        *   Se calcula y se añade el **FCS (Frame Check Sequence)** al final de la trama.
    *   La trama Ethernet completa se pasa a la Capa Física.

4.  **Capa 1: Física (Salida)**
    *   La NIC del Servidor DNS convierte la secuencia de bits de la trama Ethernet en señales eléctricas/ópticas apropiadas para el medio (cable Ethernet).
    *   Transmite estas señales a través del cable hacia el SwitchDNS, desde donde la trama será reenviada al "router gateway de la red DNS".


## Parte 2
**2.1** El largo de la HTTP Request es de 30 bytes.


**2.2 Describa que tipos de paquetes se estan usando, es decir, decir que tipo de paquete son, por que se usan estos paquetes y que deben contener.**

*   **Paquetes DNS:**
    *   **Tipo:** Son paquetes de consulta DNS (Query) y respuesta DNS (Response).
    *   **Por qué se usan:** Se usan para traducir un nombre de dominio legible por humanos (ej. `www.dcclubpenguin.com`) a una dirección IP numérica que las computadoras necesitan para establecer conexiones. PC Alice necesita la IP del servidor web DCClubPenguin antes de poder conectarse a él.
    *   **Qué deben contener (a alto nivel):**
        *   **Encapsulación:** Generalmente usan UDP para el transporte (aunque TCP es posible para transferencias de zona grandes o respuestas muy largas), luego IP.
        *   **Consulta DNS:**
            *   ID de la transacción.
            *   Banderas (indicando que es una consulta, si se desea recursión, etc.).
            *   Sección de preguntas: contiene el nombre de dominio a resolver (ej. `www.dcclubpenguin.com`) y el tipo de registro solicitado (ej. 'A' para una dirección IPv4).
        *   **Respuesta DNS:**
            *   ID de la transacción (debe coincidir con la consulta).
            *   Banderas (indicando que es una respuesta, si es autoritativa, etc.).
            *   Sección de preguntas (repite la pregunta original).
            *   Sección de respuestas: contiene el nombre de dominio, el tipo de registro, el TTL (Time To Live) y el dato de la respuesta .
            *   Podría tener secciones de autoridad y adicionales.

*   **Paquetes TCP (para el handshake y transporte de HTTP):**
    *   **Tipo:** Son segmentos TCP. Específicamente, durante el handshake, son segmentos TCP con las banderas SYN, SYN-ACK y ACK activadas. Durante la transferencia de datos HTTP, son segmentos TCP que llevan los datos HTTP como carga útil.
    *   **Por qué se usan:** TCP proporciona un servicio de transporte **confiable y orientado a la conexión**.
        *   **Orientado a la conexión:** El handshake (SYN, SYN-ACK, ACK) establece una conexión lógica antes de que se envíen datos, asegurando que ambos extremos estén listos.
        *   **Confiable:** TCP asegura que los datos lleguen en orden y sin errores (o retransmite los perdidos/corruptos) mediante números de secuencia, acuses de recibo (ACKs) y checksums. Esto es esencial para HTTP, donde la integridad de la página web es crucial.
    *   **Qué deben contener (a alto nivel):**
        *   **Encapsulación:** Van dentro de paquetes IP.
        *   Puertos de origen y destino (para identificar las aplicaciones cliente y servidor HTTP).
        *   Números de secuencia y números de acuse de recibo (para la entrega ordenada y confiable).
        *   Banderas TCP (SYN, ACK, FIN, RST, PSH, URG) para controlar el estado de la conexión.
        *   Tamaño de la ventana (para control de flujo).
        *   Checksum (para detección de errores en el encabezado y datos TCP).
        *   Datos (Payload): En el caso de la transferencia HTTP, estos datos son la solicitud HTTP o la respuesta HTTP.

*   **Paquetes HTTP:**
    *   **Tipo:** Son mensajes HTTP. Específicamente, una solicitud HTTP (HTTP Request) y una respuesta HTTP (HTTP Response).
    *   **Por qué se usan:** HTTP (Hypertext Transfer Protocol) es el protocolo de la capa de aplicación utilizado para solicitar y entregar recursos web (como páginas HTML, imágenes, etc.) entre un cliente web (navegador en PC Alice) y un servidor web (Server-PT DCClubPenguin).
    *   **Qué deben contener (a alto nivel):**
        *   **Encapsulación:** Son la carga útil de los segmentos TCP.
        *   **Solicitud HTTP (Request):**
            *   Método (ej. `GET`, `POST`, `PUT`, `DELETE`). En este caso, probablemente `GET`.
            *   URI del recurso solicitado (ej. `/` para la página principal).
            *   Versión HTTP (ej. `HTTP/1.1`).
            *   Cabeceras HTTP (ej. `Host`, `User-Agent`, `Accept`).
            *   Un cuerpo (generalmente vacío para `GET`, pero puede contener datos para `POST`).
        *   **Respuesta HTTP (Response):**
            *   Versión HTTP (ej. `HTTP/1.1`).
            *   Código de estado (ej. `200 OK`, `404 Not Found`, `301 Moved Permanently`).
            *   Mensaje de estado (ej. "OK", "Not Found").
            *   Cabeceras HTTP (ej. `Content-Type`, `Content-Length`, `Server`, `Date`).
            *   Un cuerpo, que es el contenido del recurso solicitado (ej. el código HTML de `www.dcclubpenguin.com`).

*   **Paquetes IP:**
    *   **Tipo:** Son datagramas IP (en este caso, IPv4 según la tarea).
    *   **Por qué se usan:** IP es el protocolo de la Capa de Red responsable del direccionamiento lógico y el enrutamiento de paquetes a través de múltiples redes (inter-redes). Permite que los paquetes viajen desde PC Alice hasta el ServerDNS o el Server-PT DCClubPenguin, incluso si están en redes diferentes, pasando por varios routers.
    *   **Qué deben contener (a alto nivel):**
        *   Versión (4 para IPv4).
        *   Longitud del encabezado (IHL).
        *   Longitud total del paquete.
        *   Identificación, Banderas, Desplazamiento de Fragmento (para fragmentación, si es necesaria).
        *   Tiempo de Vida (TTL).
        *   Protocolo (indica qué está encapsulado: UDP para DNS, TCP para HTTP).
        *   Checksum del encabezado.
        *   **Dirección IP de Origen.**
        *   **Dirección IP de Destino.**
        *   Opciones (raramente usadas).
        *   Datos (Payload): que sería un segmento UDP o un segmento TCP.

**2.3. Describa de forma ordenada que rutas toman los paquetes descritos en la pregunta anterior (especificar por donde pasan y en que orden).**

Basándonos en tu descripción del flujo y la topología implícita:

*   **PC Alice:** Dispositivo de origen/destino.
*   **Switch Alice:** Switch en la red local de Alice.
*   **Router Derecho:** Gateway de la red de Alice.
*   **Router Gateway (Central Derecho):** Router intermedio.
*   **Router Izquierdo (Central Izquierdo):** Router intermedio.
*   **Switch DNS:** Switch en la red del ServerDNS.
*   **ServerDNS:** Servidor DNS.
*   **Switch-PT DCClubPenguin:** Switch en la red del servidor DCClubPenguin.
*   **Server-PT DCClubPenguin:** Servidor web del juego.

**Rutas:**

**A. Resolución DNS para `www.dcclubpenguin.com`:**
    *   **Consulta DNS (PC Alice -> ServerDNS):**
        1.  PC Alice
        2.  Switch Alice
        3.  Router Derecho (Gateway de Alice)
        4.  Router Gateway 
        5.  Router Izquierdo 
        6.  Switch DNS
        7.  ServerDNS
    *   **Respuesta DNS (ServerDNS -> PC Alice):**
        1.  ServerDNS
        2.  Switch DNS
        3.  Router Izquierdo
        4.  Router Gateway
        5.  Router Derecho (Gateway de Alice)
        6.  Switch Alice
        7.  PC Alice

**B. Establecimiento de Conexión TCP (Handshake) con Server-PT DCClubPenguin:**
    *   **TCP SYN (PC Alice -> Server-PT DCClubPenguin):**
        1.  PC Alice
        2.  Switch Alice
        3.  Router Derecho (Gateway de Alice)
        4.  Router Gateway
        5.  Router Izquierdo
        6.  Switch-PT DCClubPenguin
        7.  Server-PT DCClubPenguin
    *   **TCP SYN-ACK (Server-PT DCClubPenguin -> PC Alice):**
        1.  Server-PT DCClubPenguin
        2.  Switch-PT DCClubPenguin
        4.  Router Izquierdo
        5.  Router Gateway
        6.  Router Derecho (Gateway de Alice)
        7.  Switch Alice
        8.  PC Alice
    *   **TCP ACK (PC Alice -> Server-PT DCClubPenguin):**
        1.  PC Alice
        2.  Switch Alice
        3.  Router Derecho (Gateway de Alice)
        4.  Router Gateway
        5.  Router Izquierdo
        6.  Switch-PT DCClubPenguin
        7.  Server-PT DCClubPenguin

**C. Solicitud y Respuesta HTTP:**
    *   **Solicitud HTTP (TCP con HTTP Request) (PC Alice -> Server-PT DCClubPenguin):**
        1.  PC Alice
        2.  Switch Alice
        3.  Router Derecho (Gateway de Alice)
        4.  Router Gateway
        5.  Router Izquierdo
        6.  Switch-PT DCClubPenguin
        7.  Server-PT DCClubPenguin
    *   **Respuesta HTTP (TCP con HTTP Response) (Server-PT DCClubPenguin -> PC Alice):**
        1.  Server-PT DCClubPenguin
        2.  Switch-PT DCClubPenguin
        3.  Router Izquierdo
        4.  Router Gateway
        5.  Router Derecho (Gateway de Alice)
        6.  Switch Alice
        7.  PC Alice

**2.4 Casa Bob**

**Paquetes Usados y sus Rutas (Proceso de Bob):**

*   **Paquetes DNS (Consulta y Respuesta):**
    *   **Tipo:** UDP , encapsulado en IP.
    *   **Ruta Consulta (Bob -> ServerDNS):**
        1.  PC Bob
        2.  Switch Bob
        3.  Router Derecho (Gateway de Bob)
        4.  Router Gateway
        5.  Router Izquierdo
        6.  Switch DNS
        7.  ServerDNS
    *   **Ruta Respuesta (ServerDNS -> Bob):**
        1.  ServerDNS
        2.  Switch DNS
        3.  Router Izquierdo
        4.  Router Gateway 
        5.  Router Derecho (Gateway de Bob)
        6.  Switch Bob
        7.  PC Bob

*   **Paquete TCP SYN (Intento de conexión de Bob al Servidor DCClubPenguin):**
    *   **Tipo:** TCP (con bandera SYN), encapsulado en IP.
    *   **Ruta (Bob -> hasta el punto de bloqueo):**
        1.  PC Bob
        2.  Switch Bob
        3.  Router Derecho (Gateway de Bob)
        4.  Router Gateway 
        5.  **Router Izquierdo (Central Izquierdo)** -> *Aquí es bloqueado.*

*   **Paquete ICMP (Destination Unreachable - desde el Router Izquierdo a Bob):**
    *   **Tipo:** ICMP , encapsulado en IP.
    *   **Ruta (Router Izquierdo -> Bob):**
        1.  Router Izquierdo (Central Izquierdo) - *Origen del ICMP*
        2.  Router Gateway 
        3.  Router Derecho (Gateway de Bob)
        4.  Switch Bob
        5.  PC Bob

**Diferencias y Similitudes con el Proceso de Alice:**

**Similitudes:**

1.  **Proceso de Resolución DNS:**
    *   Ambos, Alice y Bob, realizan una consulta DNS para `www.dcclubpenguin.com` para obtener su dirección IP.
    *   Los paquetes DNS (consulta y respuesta) siguen rutas similares a través de los routers centrales para llegar al ServerDNS y volver. Ambos utilizan UDP encapsulado en IP para este proceso.
    *   El contenido de los paquetes de consulta y respuesta DNS sería esencialmente el mismo (solicitando la IP de `www.dcclubpenguin.com`).

2.  **Intento Inicial de Conexión TCP:**
    *   Ambos, Alice y Bob, después de obtener la IP del servidor DCClubPenguin, intentan establecer una conexión TCP enviando un paquete TCP SYN.
    *   La parte inicial de la ruta para este paquete TCP SYN es similar (desde su PC, a través de su gateway local, hacia los routers centrales).

**Diferencias:**

1.  **Resultado de la Conexión TCP:**
    *   **Alice:** Logra completar el handshake TCP de tres vías (SYN, SYN-ACK, ACK) con el servidor DCClubPenguin, estableciendo exitosamente la conexión.
    *   **Bob:** Su paquete TCP SYN es **bloqueado** en el "Router Izquierdo". La conexión TCP **nunca se establece**.

2.  **Paquetes Posteriores al Intento de TCP SYN:**
    *   **Alice:** Después del ACK final del handshake, Alice envía paquetes TCP que contienen la solicitud HTTP GET. Luego recibe paquetes TCP del servidor DCClubPenguin que contienen la respuesta HTTP.
    *   **Bob:** En lugar de recibir un TCP SYN-ACK del servidor DCClubPenguin, Bob recibe un paquete **ICMP** originado por el Router Izquierdo. Bob no llega a enviar ningún paquete HTTP.

3.  **Interacción con el Servidor DCClubPenguin:**
    *   **Alice:** Interactúa directamente con el servidor DCClubPenguin a nivel de TCP y HTTP.
    *   **Bob:** **Nunca llega a interactuar** con el servidor DCClubPenguin a nivel de TCP o HTTP. Su comunicación es cortada antes.

4.  **Tipo de Paquete de "Error" o "Notificación":**
    *   **Alice:** Si hubiera un problema (ej. servidor caído después de la conexión), podría recibir un TCP RST (Reset) del servidor o un timeout.
    *   **Bob:** Recibe un paquete **ICMP** del router que lo bloquea, informándole que el destino es inalcanzable debido a una política administrativa.

**En resumen:**
El proceso de Bob es idéntico al de Alice hasta el punto en que su paquete TCP SYN llega al Router Izquierdo (bloqueado por AC). A partir de ahí, las políticas de seguridad divergen sus caminos. Alice procede a establecer la conexión y cargar la página, mientras que Bob es bloqueado y notificado mediante un mensaje ICMP, impidiendo cualquier comunicación posterior con el servidor DCClubPenguin.

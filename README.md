# Simulacion

## Parte 1

**1.** El largo de la dirección IP 136.155.0.2 es de 32 bits.

**2.** Dirección IP origen: 136.155.0.2

**3.** Dirección IP destino: 190.190.1.2

## Parte 2
**1.** El largo de la HTTP Request es de 30 bytes.


**2. Describa que tipos de paquetes se estan usando, es decir, decir que tipo de paquete son, por que se usan estos paquetes y que deben contener.**

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

**3. Describa de forma ordenada que rutas toman los paquetes descritos en la pregunta anterior (especificar por donde pasan y en que orden).**

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

## **Casa Bob**
Ahora, vamos a la pregunta:

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
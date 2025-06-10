# Modelo de componentes
En este documento se plantea el modelo de componentes elegido, mencionando y explicando brevemente el funcionamiento de todos los componentes que lo conforman, así como las interfaces que ofrecen y como se relacionan.

Se optó por una arquitectura en capas consistente de 6 capas, las cuales en general solo interactúan con sus capas adyacentes, con la excepción de una única interacción que se da de "capa 2" a "capa 5". Por lo tanto, nuestra solución no es exactamente en capas, si no que sigue un patrón Multi-Tier. Adicionalmente, nuestra "capa 4", la capa de negocio, fue implementada siguiendo un patrón de microservicios con 3 servicios principales: Consulta de videos, CRM, y peticiones de interacción.

---

# Capa de Presentación (Frontend)

### **Aplicación móvil**
- Permite a los residentes visualizar cámaras o grabaciones, recibir notificaciones en caso de incidentes, generar accesos temporales y abrir puertas o barreras de forma remota, todo desde un único lugar.

---

### **Aplicación web (Agentes)**
- Interfaz unificada para agentes de vigilancia: permite crear un sistema de scoring, muestra feeds priorizados según dicho scoring, permite visualizar grabaciones históricas, intervenciones (voz, apertura de puerta), registro y alerta de incidentes (escalando a autoridades si es necesario), y consultar datos y eventos sobre las instalaciones que vigila.

---

### **Interfaz administrativa**
- Panel para administradores de WolfSys, administradores de edificio y personal técnico: gestión de propiedades, usuarios, dispositivos, instalaciones y consulta de logs, ofreciendo un repertorio de acciones distintas a usuarios con permisos distintos.

---

# Capa Borde

### **Border Operations Broker (B.O.B.)**
- Punto de entrada unificado para todos los frontends.
  - Dirige las credenciales del usuario hacia el componente de autenticación y autorización para validar la sesión.
  - Recibe un token (a través de la interfaz ´IdAuth´) con las credenciales del usuario y su nivel de permisos (y un tiempo de expiración de la sesión).
  - Estudia los permisos del usuario y, si corresponde, enruta sus llamadas y peticiones a las APIs adecuadas para el caso (de video, de datos o de interacciones).
  - Con cada llamada y petición que redirige hacia el sistema también envía el token (que recibe de `IdAuth`). De esta manera los servicios de más abajo tendrán una forma fácil y rápida de conocer los permisos del usuario sin necesidad de realizar todo el proceso de autenticación nuevamente.
- **Interfaces usadas**  
  - `IdAuth` (desde módulo de Autenticación)  

---

### **Autenticación y autorización**
- Recibe las credenciales del usuario y las autentica con los datos de usuarios almacenados en la base de datos.
  - Atraviesa las capas de servicios de aplicación y de negocio para realizar el proceso de autenticación lo más rápido y eficientemente posible.
    - Es la única interacción que no obedece el patrón de capas.
  - Una vez autenticado, devuelve un token con las credenciales del usuario, sus permisos, y un tiempo de expiración de la sesión.
  - En caso de que la autenticación falle devuelve False.
- **Interfaces ofrecidas**  
  - `IdAuth`
- **Interfaces usadas**
  - `DBProvider` (DB general)

---

# Capa de Servicios de Aplicación

### **API Videos**
- Expone endpoints para listar y consumir streams en vivo a través de los endpoints de Tumimeras.
  - Si corresponde a la petición y los permisos, también aplica un ordenamiento según el scoring elegido por el usuario (a través de ´StreamScorer´).
- Ve una lista de todos los videos históricos de cualquier fecha (en `VideoProvider`), pudiendo pedir uno de ellos para ser visualizado en el frontend.
- Puede utilizar la interfaz `ModifyScoring` del Modelo de scoring, si recibe la petición con los permisos adecuados.
- **Interfaces usadas**  
  - `StreamProvider`  
  - `VideoProvider`
  - `StreamScorer`
  - `ModifyScoring`

---

### **API Datos**
- Recibe y redirecciona peticiones CRUD para toda la información básica del sistema (edificios, residentes, roles, etc) hacia el CRM.
  - Esto incluye la información de control de acceso, como pueden ser registros biométricos (registro de las caras para reconocimiento facial o huellas dactilares), PINs permanentes o temporales, tags reconocidos, etc.).
- Puede enviar una notificación push a un residente puntual si recibe la orden.
  - Debe recibir los datos del usuario residente al cual le enviará la notificación.
- **Interfaces usadas**  
  - `CRUDcrm` 
- **Interfaces ofrecidas**  
  - `NotificarResidente`

---

### **API Interacciones**
- **Qué hace**: Recibe las peticiones de apertura remota, control de acceso, e interacciones directas con las instalaciones, como comunicación por voz.
- **Interfaces usadas**  
  - `AccessControlAPI`

---

# **Capa de negocio**
Como fue mencionado, la capa de negocio se divide en 3 servicios principales:

## **Servicio de videos**

### **Consultor de videos**
- Abstrae a las capas superiores del hecho de que existen dos almacenamientos diferentes de videos históricos.
  - Si recibe una petición de un video con más de 3 meses de antigüedad lo va a buscar a la DB histórica a través de ´DBProvider´ (proporcionando la fecha, hora, y cámara de origen del video buscado). El consultor se encarga de desarchivarlo y enviarlo hacia la API.
  - Si recibe una petición de un video con menos de 3 meses de antigüedad lo recibe casi instantáneamente de Tumimeras y lo envía hacia la API. 
- Puede llamar a el modelo de scoring para proveer los videos en un ordenamiento particular, si corresponde.
- Registra todas las peticiones que recibe a través de ´Log´.  
- **Interfaces usadas**  
  - `NewerVideosProvider`  
  - `DBProvider` (DB de videos)
  - `Log`
  - `VideoScorer`  
- **Interfaces ofrecidas**  
  - `VideoProvider`

---

### **Modelo de scoring**
- Asigna prioridades manuales a una serie de videos que recibe, ya sean streams o grabaciones.
  - Las prioridades están directamente relacionadas al usuario que las asignó, por lo que registra y consulta el modelo elegido en la base de datos general a través de `DBProvider`.
- Cuando en un futuro se quiera automatizar el proceso, solo hará falta intercambiar el componente por uno que ofrezca las mismas interfaces (a excepción de `ModifyScoring` si no se desea otorgar esa opción), o alternativamente cambiar su funcionamiento interno; pues sus clientes (API Videos y Consultor de videos) están totalmente abstraídos de sus métodos y criterios de scoring, solo reciben la lista ya ordenada.
- **Interfaces usadas**
  - `DBProvider` (DB general)
- **Interfaces ofrecidas**
  - `StreamScorer`
  - `VideoScorer`
  - `ModifyScoring`

---

### **Tumimeras**
- Autentica dispositivos de vigilancia.
  - Expone los streams en tiempo real, a través de endpoints HTTP.
  - Almacena las grabaciones automáticamente en su memoria interna.
- Cuando se le solicita una grabación con menos de 3 meses de grabada, dada su fecha y su cámara de origen, lo transmite a través de la interfaz `NewerVideosProvider`.
- **Interfaces ofrecidas**
  - `CameraAuth`
  - `StreamProvider`
  - `NewerVideosProvider`
  - `OlderVideosProvider`

---

## **Servicio de datos (CRM)**

### **Registro de incidentes**
- Solo para los usuarios de la aplicación web (agentes de vigilancia).
- Incluye una serie de incidentes prototípicos (intento de entrada forzosa, vandalismo, etc) para que el usuario seleccione o introduzca una descripción personalizada.
- Al recibir la orden (a través de `CRUDcrm`) registra el tipo de incidente, la fecha y hora en la que ocurrió, el o los usuarios afectados, y el agente que lo registró.
  - Si la petición lo indica, tiene la opción de enviar una notificación push a todos los usuarios afectados.
  - Si la petición lo indica, tiene la opción de alertar a las autoridades. Por la sensibilidad de la situación en este caso, el proceso se realiza internamente en el mismo componente, de forma que se eviten posibles retrasos por latencia o sobrecarga de otras partes del sistema.
- **Interfaces usadas**
  - `NotificarResidente`
  - `Log`
- **Interfaces ofrecidas**
  - `CRUDcrm`

---

### **Auditoría e Historial**
- Se encarga de registrar todos los eventos e interacciones con el sistema en la base de datos general.
- Permite el acceso y visualización a dichos registros, si el usuario tiene los permisos apropiados. Bajo ninguna circunstancia permite su modificación.
  - También registra estos accesos.
- **Interfaces usadas**
  - `DBProvider` (DB general)
- **Interfaces ofrecidas**
  - `Log`
  - `CRUDcrm` (solo Read)
 
---

### **Gestión de entidades**
 - Componente centralizado para CRUD de dispositivos, edificios y usuarios en la base de datos general.
 - Si se modifica algo de un edificio, automáticamente se encarga de modificar la información de todos los usuarios residentes afectados también.
 - Registra todas las peticiones que recibe y efectúa.
 - **Interfaces usadas**
   - `Log`
   - `DBProvider` (DB general)
 - **Interfaces ofrecidas**
   -  `CRUDcrm`
  
---

### Gestor de información para control de acceso
 - Se encarga de operaciones CRUD referentes a las credenciales y métodos de acceso al edificio.
   - Es el encargado de generar los PINs temporales para visitas.
 - Cada ciertos intervalos le envía la información de control de acceso relevante a los edge controllers de cada edificio (a través de su respectivo Gateway, con una interfaz `ConsistenciaProvider`).
 - **Interfaces usadas**
   - `DBProvider` (DB general)
   - `Log`
 - **Interfaces ofrecidas**
   -  `CRUDcrm`
   -  n `ConsistenciaProvider`s

---

## Servicio de interacciones

### **Gestor del control de acceso**
- En los servidores de WolfSys.
- Se encarga de redirigir las peticiones o llamadas al edificio correspondiente para ser ejecutadas.
  - Según el tipo de petición deberá formular una estrategia de envío diferente (no es lo mismo enviar una señal de activación para abrir una puerta que una señal de audio para la comunicación de voz del agente).
  - Tiene endpoints para cada tipo de datos que puede enviar. Los expone en `AccessControlAPI` y es trabajo de la API de interacciones enrutar cada tipo de petición hacia su endpoint correspondiente.
- Registra todas las peticiones que pasan por él (a través de `Log`).
- **Interfaces usadas**  
  - n `ComunicaciónInstalación`(e)s
  - `Log`
- **Interfaces ofrecidas**  
  - `AccessControlAPI`

---

### **Gateway del edificio**
 - Componente local al edificio encargado de recibir y enrutar (dentro del edificio) las peticiones de interacción.
   - Si se recibe una petición de apertura de puerta remota la enruta hacia el Edge Controller.
   - Si se recibe una señal de audio a transmitir la enruta hacia el dispositivo correspondiente.
 - Si hay conexión y alguien intenta acceder se encarga de consultar todas las formas posibles de control de acceso cuando haga falta, a través de `ConsistenciaProvider`.
   - El Edge Controller debe pedirle al Gateway los datos de acceso válidos en intervalos periódicos (a través de `ConsistenciaLocal`).
 - Registra todos los accesos e interacciones a través de `Log`.
 - Si hay una perdida de conexión, cuando esta vuelva se encarga de sincronizar logs y estados de la información de control de acceso.  
- **Interfaces usadas**  
  - `Log`
  - `ConsistenciaProvider`
  - `AccessControl`
  - `ReproductorDeMensaje`
- **Interfaces ofrecidas**  
  - `ComunicaciónInstalación`
  - `ConsistenciaLocal` (acceso local de emergencia)

---

### **Edge Controller**
- Dispositivo conectado a todas las puertas y barreras que deban ser controladas por el sistema.
  - Las controla a través de la interfaz `Abridor`.
- Ejecuta apertura remota de puertas al recibir la petición desde `AccessControl`.
- Ejecuta validación local de métodos de apertura de puertas (tags, PINs, biometría).
  - Sólo permite usar métodos que considere "válidos" en ese momento.
- Sincroniza eventos e información de acceso con el Gateway.
  - En intervalos regulares, le pide al Gateway su información de acceso válida más reciente.
- En caso de que no haya conectividad pasa a modo degradado: deja de considerar como válidos todos los métodos de acceso digitales, y solo permite el uso de tags que validará usando la base de datos local (que solo tiene la información de las tags).
  - Al volver la conectividad le pide al Gateway nuevamente los otros datos de acceso válidos, y en caso de que información sobre las tags haya cambiado, actualiza la base de datos.
- **Interfaces usadas**
  - `Abridor`
  - `ConsistenciaLocal` 
  - `DBProvider` (DB local de tags)  
- **Interfaces ofrecidas**  
  - `AccessControl`

---

## Capa de Control de Datos

### **La Heladera**
- Se encarga de tomar todos los videos que tengan más de 3 meses almacenados en el almacenamiento interno de Tumimeras (en caliente), archivarlos y enviarlos a la base de datos histórica de videos para ser almacenados en frío.
  - A cada video también se le asocia su fecha de grabación y un identificador de su cámara para no perder la información de donde fue grabado.
  - Debe traducir el formato completo del video almacenado en Tumimeras a un archivo.mp4, y dos metadatos: timestamp de grabación, e id de cámara de origen. A continuación se le asigna una id única al video y se almacena el .mp4 en `DB histórica de videos` (basada en minIO) usando la id del video, y los 2 metadatos se almacenan en `DB de datos de videos históricos` (basada en postgreSQL) con la id de video como identificador.
  - Cada noche realiza la transferencia de los videos que durante ese día cumplieron los 3 meses.
  - En resumen, "enfría" los videos almacenados.
- **Interfaces usadas**  
  - `OlderVideosProvider` 

---

### **Persistencia videos fríos**
- Intermediario entre Consultor de videos y la DB de videos.
  - Debe abstraer al Consultor de videos de la complejidad del sistema de almacenamiento en frio. El Consultor debe entender que todo (metadatos y videos) está en una misma base de datos.
  - Entrega los videos aún archivados.
- **Interfaces ofrecidas**  
  - `DBProvider` (DB de videos)

---

### **Persistencia datos e información general**
- Intermediario entre el sistema y la DB general.
  -  Interactúa principalmente con el CRM, pero también con el modelo de scoring y el componente de autenticación y autorización.
- **Interfaces ofrecidas**  
  - `DBProvider` (DB general)

---

### **Persistencia local de tags**
- Intermediario entre los Edge Controllers y las DBs locales.
- **Interfaces ofrecidas**  
  - `DBProvider` (DB local de tags)

---

## Capa de Datos

### **DB general**
- Base de datos relacional basada en PostgreSQL.
- Guarda entidades de todo tipo (edificios, usuarios, dispositivos), logs de interacciones del sistema, logs de eventos (incidentes, accesos, apertura de puertas, etc), toda la información válida de acceso para cada puerta, y datos de autenticación de los usuarios (identificador y contraseña).

### **DB histórica de videos**
- Técnicamente no es una DB, es un *sistema de almacenamiento de archivos*.
- Basado en minIO.
- La ruta de cada video es única según su id.
- Archivado en frío de grabaciones de hace más de 3 meses.

### **DB de datos de videos históricos**
- Complementa a la anterior.
- Base de datos relacional basada en PostgreSQL.
- Almacena el timestamp de grabación, la cámara de origen, y un id único para cada video almacenado.

### **DB local de tags**
- Pequeña base de datos relacional basada en SQLite.
- Mantiene una persistencia de credenciales (solo tag RFID) para operación de las puertas en modo degradado.

---


---

## Diagrama de componentes:
![Diagrama de componentes.png](Diagramas/Exportados/Diagrama-de-componentes.png)

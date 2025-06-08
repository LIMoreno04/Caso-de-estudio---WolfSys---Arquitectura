# Modelo de componentes
Se optó por una arquitectura en capas, las cuales en su mayoría obedecen el patrón de capas, con la excepción de una única interacción que se da de "capa 2" a "capa 5". Por lo tanto, nuestra solución sigue un patrón Multi-Tier. Adicionalmente, nuestra "capa 4", la capa de negocio, fue implementada siguiendo un patrón de microservicios con 3 servicios principales: Consulta de videos, CRM, y peticiones de interacción.

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
  - Estudia los permisos del usuario y, si corresponde, enruta sus llamadas y peticiones a las APIs adecuadas para el caso (de video, de datos o de peticiones).
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
- Puede enviar una notificación push a un residente puntual si recibe la orden.
  - Debe recibir los datos del usuario residente al cual le enviará la notificación.
- **Interfaces usadas**  
  - `CRUDcrm` 
- **Interfaces ofrecidas**  
  - `NotificarResidente`

---

### **API Peticiones**
- **Qué hace**: Recibe las peticiones de apertura remota, control de acceso, e interacciones directas con las instalaciones, como comunicación por voz.
- **Interfaces usadas**  
  - `AccessControlAPI`

---

# **Capa de negocio**
Como fue mencionado, la capa de negocio se divide en 3 servicios principales:

## **Servicio de videos**

### **Consultor de videos**
- Abstrae a las capas superiores del hecho de que existen dos almacenamientos diferentes de videos históricos.
  - Si recibe una petición de un video con más de 3 meses de antigüedad lo va a buscar a la DB histórica a través de ´DBProvider´, se encarga de desarchivarlo y enviarlo como video hacia la API.
  - Si recibe una petición de un video de menos de 3 meses de antigüedad lo recibe casi instantáneamente de Tumimeras y lo envía hacia la API. 
- Puede llamar a el modelo de scoring para proveer los videos en un ordenamiento particular, si corresponde.
- Registra todas las peticiones que recibe a través de ´Log´.  
- **Interfaces usadas**  
  - `NewerVideosProvider`  
  - `DBProvider` (DB histórica de videos)
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

## **Servicio de datos (CRM)**

### **Registro de incidentes**
- Solo para los usuarios de la aplicación web (agentes de vigilancia).
- Incluye una serie de incidentes prototípicos (intento de entrada forzosa, vandalismo, etc) para que el usuario seleccione o introduzca una descripción personalizada.
- Al recibir la orden (a través de `CRUDcrm`) registra el tipo de incidente, la fecha y hora en la que ocurrió, y el o los usuarios afectados.
  - Si la petición lo indica, tiene la opción de enviar una notificación push a todos los usuarios afectados.
  - Si la petición lo indica, tiene la opción de alertar a las autoridades. Por la sensibilidad de la situación en este caso, el proceso se realiza internamente en el mismo componente, de forma que se eviten posibles retrasos por latencia o sobrecarga de otras partes del sistema.
- **Interfaces usadas**
  - `NotificarResidente`
  - `Log`
- **Interfaces ofrecidas**
  - CRUDcrm

---

### **Gestor del control de acceso**
- **Qué hace**: Lógica central de autorización multifactor (RFID, PIN, biometría, intervención).  
- **Interfaces usadas**  
  - `AccessControlAPI` (desde API Peticiones)  
- **Interfaces ofrecidas**  
  - `AccessControlAPI` (a CRM y API Peticiones)  
  - `ComunicaciónInstalación` (comunica comandos al Gateway del edificio)

---

### **Gateway del edificio**
- **Qué hace**:  
  - Nodo local de resiliencia: en modo degradado sigue validando accesos sin CRM.  
  - Sincroniza logs y estados cuando recobra conectividad.  
- **Interfaces usadas**  
  - `ComunicaciónInstalación` (desde Gestor de Control)  
- **Interfaces ofrecidas**  
  - `AccessControl` (a Edge Controllers)  
  - `ConsistenciaProvider` (sincronización con CRM)  
  - `ConsistenciaLocal` (acceso local de emergencia)

---

## 🟣 Capa de Control de Datos

### **Edge Controller**
- **Qué hace**:  
  - Ejecuta validación local (tags, PINs, biometría) usando DB local.  
  - Reproduce mensajes de voz (portero remoto).  
  - Sincroniza eventos y llaves con el Gateway.  
- **Interfaces usadas**  
  - `AccessControl` (desde Gateway del edificio)  
  - `DBProvider` (acceso a DB local de tags)  
- **Interfaces ofrecidas**  
  - (ninguna externa; retorna respuestas de validación al Gateway)
---

### **Recolector de videos fríos**
- **Qué hace**: Extrae automáticamente grabaciones >3 meses de “Tumimeras” para archivado.
- **Interfaces usadas**  
  - `OlderVideosProvider` (desde Tumimeras)  
- **Interfaces ofrecidas**  
  - `DBProvider` (inserta en DB histórica de videos)

---

### **La Heladera**
- **Qué hace**: Microservicio de consulta de archivo histórico de videos.
- **Interfaces usadas**  
  - `DBProvider` (acceso a DB histórica)  
- **Interfaces ofrecidas**  
  - `StreamProvider` (a Consultor de videos)

---


## 🟨 Capa de Datos

### **DB general**
- Guarda entidades maestras y logs transaccionales del CRM y microservicios.

### **DB histórica de videos**
- Archivo frío de streams (>3 meses).

### **DB local de tags**
- Persistencia de credenciales (RFID, PIN…) para operación en modo degradado.

---

> **En conjunto**, este diseño cumple los objetivos de modularidad, escalabilidad y resiliencia:  
> - **Separación clara de responsabilidades** (streaming, scoring, CRM, control de acceso).  
> - **Borde inteligente** (B.O.B. y Gateways) para alta disponibilidad y degradación local.  
> - **Interfaces bien definidas** para facilitar futuras integraciones (IA de video, nuevos métodos de acceso).  
> - **Auditoría y logs inmutables** distribuidos en toda la arquitectura.  


---

## Diagrama de componentes:
![Diagrama de componentes.png](Diagramas/Exportados/Diagrama-de-componentes.png)

# Modelo de Despliegue

Este documento describe el modelo de componentes para el sistema WolfSys, explicando el propósito y funcionamiento de cada uno, así como las interfaces que ofrecen y las relaciones entre ellos. El sistema sigue una arquitectura distribuida en 6 capas principales: presentación, borde, servicios de aplicación, servicios de negocio, persistencia de datos e infraestructura local.

---

## Capa de Presentación (Frontend)

### **App Móvil**
- Aplicación para residentes (iOS/Android).
- Permite visualizar cámaras, acceder a grabaciones, gestionar accesos y recibir notificaciones.

### **App Web**
- Interfaz para agentes de vigilancia desde navegador.
- Permite visualizar transmisiones priorizadas, consultar eventos, registrar incidentes e interactuar con accesos y residentes.

### **Panel Admin**
- Panel para administradores del sistema (WolfSys, edificios, técnicos).
- Administra usuarios, edificios, dispositivos, accesos, y logs del sistema.

---

## Capa Borde (Edge Computing)

### **Gateway Edificio**
- Router local del edificio.
- Enruta comunicaciones hacia los dispositivos del edificio.
- Sincroniza y propaga la información de acceso desde el backend hacia los Edge Controllers.

### **Edge Controller**
- Controla los accesos físicos al edificio.
- Valida métodos de autenticación locales (tags RFID).
- Funciona en modo degradado si pierde conectividad, utilizando la base de datos local.

### **DB Local Tags**
- Base de datos SQLite.
- Almacena información sobre tags RFID válidos para operación autónoma del Edge Controller.

---

## Capa de Servicios de Aplicación

### **Servidor Aplicación**
- B.O.B + APIs.
- Gestiona autenticación de usuarios, recibe peticiones de las apps y las enruta según los permisos asociados.
- Se comunica con la base de datos general y delega funciones específicas a los servicios de negocio.

---

## Capa de Servicios de Negocio

### **Servidor Procesamiento**
- Ejecuta lógica del negocio.
- Contiene el modelo de scoring para priorización de transmisiones de video.
- Se comunica con la DB de videos y el sistema de scoring.

### **Servidor Control**
- Control de acceso centralizado.
- Recibe peticiones desde la API de interacciones y las enruta hacia los gateways del edificio.
- Gestiona la comunicación con los Edge Controllers.

---

## Capa de Persistencia y Control de Datos

### **DB Principal**
- Base de datos PostgreSQL.
- Contiene datos generales del sistema: usuarios, edificios, roles, accesos, logs, autenticación.

### **DB Videos**
- Almacenamiento en frío para videos históricos.
- Accedida por los servicios de negocio cuando se solicitan videos antiguos.

### **La Heladera**
- Componente de archivado de videos.
- Transfiere automáticamente videos de más de 3 meses desde almacenamiento caliente a almacenamiento frío.

---

## Servicios Externos

### **Tumimeras**
- Sistema externo de video streams.
- Provee transmisiones en vivo hacia las APIs de video.

---

## Interacciones entre Capas

- Las **aplicaciones cliente** se comunican únicamente con el **Servidor de Aplicación**, que autentica y enruta peticiones según permisos.
- El **Servidor de Aplicación** consulta directamente a la **base de datos principal** para autenticación.
- El **Servidor de Aplicación** se comunica con los **servicios de negocio** para scoring de videos o gestión de interacciones.
- El **Servidor Control** envía comandos de apertura o voz hacia el **Gateway del edificio**, que lo transmite al **Edge Controller**.
- Las **Tumimeras** transmiten en vivo hacia el servidor de negocio de videos.
- Los **Edge Controllers** sincronizan con el **Gateway** periódicamente, y usan la **DB local** para acceso offline.

---

## Diagrama de despliegue:
![Diagrama de despliegue.png](Diagramas/Exportados/Diagrama-de-despliegue.png)

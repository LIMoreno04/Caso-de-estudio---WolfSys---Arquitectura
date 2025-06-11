# Modelo de Despliegue

Este documento describe el modelo de despliegue para el sistema **WolfSys**, explicando las agrupaciones de componentes decididas, su despliegue operacional y sus relaciones. Para los detalles del funcionamiento de cada componente ver [Modelo de componentes](modelo-de-componentes.md)

---

## Presentación

### App Móvil (Dispositivo del usuario)
- Aplicación para residentes (iOS/Android).
- Es la parte del frontend del componente de la aplicación móvil que se ejecuta en el dispositivo del residente.

### App Web (Dispositivo del usuario)
- Aplicación web para agentes.
- Es la parte del frontend del componente de la aplicación web que se ejecuta en el navegador de los agentes.

### Interfaz Administrativa (dispositivo del usuario)
- Interfaz web para administradores del sistema, técnicos y operadores.
- Es la parte del frontend del componente de la interfaz administrativa que se ejecuta en el navegador de los administradores y técnicos.

---

## Borde y servicios de aplicación

### Host Frontend
- Aplicaciones web y móvil hosteadas en un servidor.
- Es el intermediario entre los dispositivos cliente (web o móvil) y el gateway B.O.B.
- Contiene la parte de los componentes de frontend que se ejecutan en los servidores locales.

### Edge Gateway
- Componente B.O.B.
- Componente Autenticación y autorización.
- Componentes de API (API Videos, API Datos, API Interacciones).
- Punto de entrada principal a la lógica del sistema y conexión con servicios backend.

---

## Servicios de Negocio

### Servidor de Procesamiento 1
- Componente Consultor de videos.
- Componente Persistencia videos frios.
- Responsable de ofrecer servicios de video.

### Servidor de Procesamiento 2
- Artefacto de CRM.

### Módulo Removible (Scoring)
- Componente Modelo de scoring.
- Particularmente fácil de intercambiar o modificar, pensado para la futura integración de modelos de IA y de scoring dinámico.

### Tumimeras
- Tumimeras (Se asume que la solución de Tumimeras ya incluye su propia gestión de almacenamiento y conexión con los dispositivos de vigilancia).

### Servidor de Control
- Componente Gestor del control de acceso.
- Recibe peticiones desde las APIs (Edge Gateway) y coordina la comunicación con los gateways y controladores del edificio.

### Gateway del Edificio
- Router local del edificio.
- Sincroniza credenciales y configuraciones de acceso con los Edge Controllers.
- Enruta tráfico hacia dispositivos y controladores físicos.

### Edge Controller
- Controlador local de accesos.
- Verifica autenticación offline mediante la DB local de tags.
- Coordina apertura de puertas, emisión de audio y lectura de tags RFID.

---

## Persistencia y Almacenamiento

### Main: Base de Datos de Videos
- DB Histórica de videos (sistema de almacenamiento de archivos minIO).
- DB de datos de videos históricos (PostgreSQL).

### Réplica: Base de Datos de Videos
- Copia redundante para disponibilidad ininterrumpida durante el funcionamiento de la Heladera y resiliencia ante fallos.

### Base de Datos Principal
- DB general (PostgreSQL).
- Persistencia de datos e información general.

### DB Local (Tags)
- DB local de tags.
- Persistencia local de tags.

### La Heladera
- La Heladera.

---



## Diagrama de Despliegue

![Diagrama de despliegue](Diagramas/Exportados/Diagrama-de-despliegue.png)

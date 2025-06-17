# Visión de la Arquitectura de WolfSys
Este documento constituye nuestra “visión” de la arquitectura de WolfSys para su sistema de portería virtual, definiendo el alcance, requerimientos funcionales y no funcionales. Para más detalles de una vista específica referirse a [Actores y Casos de uso](casos-de-uso.md), [Modelo de componentes](modelo-de-componentes.md), [Operacional](modelo-operacional.md), [Despliegue](despliegue.md) o los ADRs y diagramas asociados.

## 1. Objetivo General
Diseñar una arquitectura modular, escalable y resiliente para la plataforma de portería virtual de WolfSys, de modo que permita:
- Gestionar videovigilancia remota con baja latencia y alta disponibilidad.  
- Unificar la experiencia de agentes de vigilancia con acceso a información contextual y control de puertas integrado desde una aplicación web.  
- Centralizar la información y la gestión de edificios, residentes y dispositivos en un CRM que sirva de fuente confiable para todos los módulos.  
- Asegurar capacidades de control de acceso físico incluso ante fallos de conectividad o energía.  
- Ofrecer una aplicación móvil a los usuarios finales (residentes, administradores del edificio) que permita la visualización de cámaras, recibir notificaciones y otorgar accesos temporales.  
- Mantener un registro inalterable de eventos críticos (accesos, intervenciones, cambios de configuración).  
- Prever una expansión en el futuro para integrar análisis de video con IA, scoring dinámico de cámaras y automatización de procesos.

## 2. Alcance y Fronteras del Sistema
- **Incluye**  
  - Gestión y transmisión de streams de video desde cámaras instaladas en edificios (accesos, espacios comunes, perímetros).  
  - Aplicación unificada para agentes de vigilancia, con feeds priorizados, herramientas de intervención y registro de eventos.  
  - Módulo CRM que centraliza información de cada edificio y un historial de interacciones (accesos, soporte, generación de PINs, revisiones de video).  
  - Subsistema de control de acceso físico que agrega múltiples métodos (tags, PIN, biometría, intervención remota) y soporta operación local en modo degradado.  
  - Aplicación móvil para residentes y administraciones con funcionalidad de monitoreo de cámaras, notificaciones, generación de accesos temporales, apertura remota de puertas; y en el caso de las administraciones, gestión de los residentes de ese edificio.  
  - Interfaz administrativa para gestión de propiedades, usuarios, roles, dispositivos e instalaciones de WolfSys.  
  - Infraestructura de almacenamiento de video con retención en “modo caliente” (hasta 3 meses) y archivo histórico.  
- **Excluye**  
  - Desarrollo de nuevos sistemas de cámaras o lectores (se asume compatibilidad con dispositivos existentes).  
  - Sustitución del sistema de procesamiento y transmisión de video (Tumimeras se mantiene en uso).  
  - Implementación detallada de modelos de IA o scoring avanzado (la arquitectura debe prever su integración futura, pero no desarrollarla).

## 3. Requerimientos Funcionales Clave
* **Videovigilancia**  
  * Conectar y autenticar streams de video provenientes de cámaras en accesos, espacios comunes y perímetros a través de “Tumimeras”.  
  * Listar, mediante API HTTP, los feeds disponibles y permitir su visualización en tiempo real.  
  * Configurar almacenamiento “en caliente” de video (hasta 3 meses) y acceso eficiente a archivos históricos.  
* **Interfaz Unificada para Agentes**  
  * Mostrar feeds priorizados según criterios estáticos (ubicación, tipo de acceso) y dinámicos (detección de movimiento, reconocimiento facial).  
  * Centralizar acciones: intervención por voz, apertura de puerta, registro de incidente y escalado a móvil o policía.  
  * Registrar cada intervención como un evento inmutable en el sistema.  
* **Módulo de Scoring de Cámaras**  
  * Definir manualmente prioridades a las cámaras, según criterios personales.  
  * Permitir integración futura de IA o reglas contextuales para ajustar scores dinámicamente.  
* **CRM Integrado**  
  * Almacenar información de edificios, unidades y residentes: infraestructura, histórico de accesos, solicitudes de soporte, generación de PINs y revisiones de video.  
  * Registrar de forma inalterable cada solicitud, cambio o reenvío de PIN/video.  
  * Exponer servicios en tiempo real para que otros módulos consuman datos del CRM.  
* **Control de Acceso Físico**  
  * Validar accesos mediante tag (RFID), código PIN, intervención remota y biometría (huella o reconocimiento facial).  
  * Centralizar la lógica de validación en un componente, con operación local autónoma (modo degradado) en cada edificio, mediante Edge Controllers.  
  * Gestionar creación, uso y expiración de llaves y códigos, registrando cada evento en un log inmutable.  
* **Aplicación Móvil para Usuarios Finales**  
  * Visualizar feeds de cámaras en tiempo real de su propio edificio.  
  * Recibir notificaciones push ante intentos de ingreso o llamadas al portero.  
  * Generar accesos temporales (PIN o enlace) para visitantes.  
  * Ejecutar comandos remotos: apertura de puertas o barreras.  
* **Interfaz Administrativa**  
  * CRUD de propiedades (torre, complejo, ejecutiva) y configuración de servicios contratados.  
  * Gestión de usuarios (residentes, personal de servicio, administración, agentes) con asignación de roles y permisos.  
  * Para cada administrador de edificio, acceso restringido sólo a sus residentes.  
  * Gestión de instalaciones y equipos por el equipo técnico de WolfSys.  
  * Registro y control de todas las operaciones de configuración con logs inmutables.

## 4. Requerimientos No Funcionales Priorizados
* **Disponibilidad y Resiliencia**  
  * Tolerancia a fallos parciales de red o energía; Edge Controllers en modo degradado (solo tags) cuando no hay conectividad o energía principal.  
  * Uptime mínimo del 99,9 % en servicios centrales de autenticación, CRM y transmisión de video.  
* **Baja Latencia**  
  * Latencia inferior a 3 s para la consulta de videos y streams a los agentes de vigilancia.  
  * Respuestas de la aplicación móvil y del panel administrativo por debajo de 2 s en operaciones críticas.  
* **Escalabilidad**  
  * Escalado horizontal de servicios de procesamiento y distribución de video.  
  * Capacidad de aumentar las bases de datos y los recursos conforme crecen edificios y usuarios.  
* **Seguridad y Confidencialidad**  
  * Autenticación y autorización robustas en todos los endpoints (basadas en estándares definidos internamente).  
  * Cifrado de datos en tránsito.  
  * Control de acceso por roles con separación de funciones (residentes, administración, agentes, técnicos, sistemas externos).  
  * Logs inmutables y rastreables para auditorías y análisis de incidentes.  
* **Modificabilidad**  
  * Arquitectura modular que facilite la incorporación de nuevos servicios (análisis de video con IA, scoring avanzado) sin impactar componentes existentes.  
* **Mantenibilidad**  
  * Documentación clara, diagramas actualizados y ADRs que justifiquen las decisiones.  
  * Modularidad y separación de responsabilidades para aislar errores.  
  * Convenciones estandarizadas en nomenclatura, usos de APIs y formatos de log.  
* **Interoperabilidad**  
  * Soporte de protocolos estándar de video (RTSP, HLS).
---

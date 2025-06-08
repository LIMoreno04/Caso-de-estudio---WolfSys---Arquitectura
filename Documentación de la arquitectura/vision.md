# Visión de la Arquitectura de WolfSys
Este documento constituye nuestra “visión” de la arquitectura de WolfSys para su sistema de portería virtual, definiendo el alcance, requerimientos funcionales y no funcionales. Para más detalles de una vista específica referirse a [Actores y Casos de uso](casos-de-uso.md), [Modelo de componentes](modelo-de-componentes.md), [Despliegue](despliegue.md) o los ADRs y diagramas asociados.

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
1. **Videovigilancia**  
   - Conectar y autenticar streams de video provenientes de cámaras en accesos, espacios comunes y perímetros a través de “Tumimeras”.  
   - Listar mediante API HTTP los feeds disponibles y permitir su visualización en tiempo real.  
   - Configurar almacenamiento en caliente de video (hasta 3 meses) y acceso eficiente a archivos históricos.
   - Las cámaras de videovigilancia se conectan por WiFi o Ethernet, y todas soportan los mismos protocolos y encodings.  
2. **Interfaz Unificada para Agentes**  
   - Mostrar en una sola vista los distintos feeds priorizados según criterios estáticos (ubicación, tipo de acceso) o dinámicos (detección de movimiento, reconocimiento facial).  
   - Centralizar acciones desde la interfaz: intervención por voz, apertura de puerta, registro de incidente, escalado a móvil o policía.  
   - Registrar cada intervención como evento trazable en el sistema (logs inmutables).  
3. **Módulo de Scoring de Cámaras**  
   - Definir de forma manual (inicialmente) prioridades de cámaras y exponerlas en la interfaz.  
   - Permitir futura integración de modelos de IA o reglas contextuales para ajustar scores dinámicamente.  
4. **CRM Integrado**  
   - Almacenar toda la información de edificios, unidades y residentes: infraestructura, histórico de accesos, solicitudes de soporte, generación de PINs, revisiones de video.  
   - Registrar de forma inalterable cada solicitud, cambio o reenvío de PIN/video.  
   - Exponer servicios para que otros módulos (aplicación móvil, interfaz administrativa) consuman datos del CRM en tiempo real.  
5. **Control de Acceso Físico**  
   - Validar accesos mediante distintos métodos: tag (RFID), código PIN, intervención remota (agente o residente) y biometría (huella o reconocimiento facial).  
   - Centralizar la lógica de validación en un componente de autorización, pero habilitar operación local autónoma (modo degradado) en cada edificio ante fallo de conectividad mediante dispositivos "Edge Controllers".  
   - Gestionar creación, uso y expiración de llaves y códigos, registrando cada uso en un log inalterable.  
6. **Aplicación Móvil para Usuarios Finales**  
   - Visualizar en tiempo real los feeds de cámaras del propio edificio.  
   - Recibir notificaciones push ante intentos de ingreso o llamadas al portero.  
   - Generar accesos temporales (PIN o enlace) para visitantes.  
   - Ejecutar comandos remotos: apertura de puerta o barrera.  
7. **Interfaz Administrativa**  
   - Para los usuarios administrativos de WolfSys: CRUD de propiedades (torre, complejo, ejecutiva) y sus configuraciones de servicios contratados.  
   - Para los usuarios administrativos de WolfSys: Gestión de usuarios (residentes, personal de servicio, administración, agentes) con asignación de roles y permisos.
   - Para la administración del edificio: Gestión únicamente de los residentes de su edificio.
   - Para el equipo técnico de WolfSys: gestionar informacion sobre los equipos en diferentes puntos, denominados instalaciones.
   - Registro y control de todas las operaciones de configuración con logs inmutables.

## 4. Requerimientos No Funcionales Priorizados
1. **Disponibilidad y Resiliencia**  
   - La plataforma debe tolerar fallos parciales de red o energía. Los Edge Controllers en cada edificio deben operar en modo degradado (ej: solo tags) cuando no hay conectividad o energía principal (gracias a UPS).  
   - Garantizar al menos 99.9 % de uptime en los servicios centrales de autenticación, CRM y transmisión de video.  
2. **Baja Latencia**  
   - Transmisión end-to-end de video en tiempo real con latencia inferior a 200 ms para agentes de vigilancia (mediante "Tumimeras").  
   - Respuestas de la aplicación móvil y panel administrativo inferiores a 2 s para operaciones críticas (consulta de feed en vivo, generación de PIN, apertura de puerta).  
3. **Escalabilidad**  
   - Capacidad de escalar horizontalmente los servicios de procesamiento y distribución de video (conexión a “Tumimeras” y componentes que asistan el proceso).  
   - Base de datos del CRM y recursos de cache (scoring de cámaras) deben poder aumentarse según crezca la base de edificios y usuarios.  
4. **Seguridad y Confidencialidad**  
   - Implementar autenticación y autorización robustas para todos los endpoints (utilizando algún estándar con reputación).  
   - Cifrado de datos en tránsito y en reposo (bases de datos, almacenamiento de video).  
   - Control de acceso basado en roles con separación clara de funciones entre residentes, administración, agentes, técnicos y sistemas externos.
   - Todos los logs de accesos, cambios de configuración, generación de PINs, intervenciones y consultas al CRM deben ser inmutables y rastreables.  
   - Mantener historiales completos para auditorías y permitir análisis en caso de incidentes.   
5. **Interoperabilidad**  
   - Exponer APIs bien definidas para facilitar integración con sistemas externos (apps de administración de edificios, futuros módulos de IA).  
   - Soportar protocolos estándar de video (RTSP, HLS) para garantizar compatibilidad con dispositivos heterogéneos.  
6. **Extensibilidad**  
   - Arquitectura modular que permita agregar nuevos servicios (p. ej. análisis de video con IA, scoring avanzado) sin impacto significativo en componentes existentes.  
7. **Mantenibilidad**  
   - Documentación clara, diagramas actualizados y ADRs para justificar decisiones.
   - Modularidad y separación de responsabilidades para aislar errores y facilitar el entendimiento del sistema.  
   - Estandarización de convenciones (nomenclatura de servicios, contratos de API, formatos de logs).

---

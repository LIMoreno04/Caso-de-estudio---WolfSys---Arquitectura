# Visión de la Arquitectura de WolfSys
Este documento constituye nuestra “visión” de la arquitectura de WolfSys para su sistema de portería virtual, definiendo los actores, casos de uso, requerimientos funcionales y no funcionales, así como el contexto de despliegue y las líneas de evolución futura. Referirse a los ADRs y diagramas asociados para detalles específicos del diseño de cada aspecto.

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

## 5. Actores Principales y sus Casos de Uso
- **Residente / Usuario Final**  
  - **C.U. 1:** Iniciar sesión en la aplicación móvil.
  - **C.U. 2:** Visualiza cámaras en vivo de su edificio desde la aplicación móvil.  
  - **C.U. 3:** :Recibe notificaciones push ante intentos de ingreso o llamadas desde el portero virtual.  
  - **C.U. 4:** Genera accesos temporales (PIN o enlace) para visitas.  
  - **C.U. 5:** Ejecuta apertura remota o en sitio de puerta o barrera.
  - **C.U. 6:** Visualizar grabaciones históricas de su edificio desde la aplicación móvil.

- **Agente de Vigilancia**  
  - **C.U. 7:** Inicia sesión en la aplicación web.
  - **C.U. 8:** Asigna un cierto valor o criterio de prioridad a cada una de sus cámaras (scoring manual inicial). Los criterios pueden ser estáticos (ubicación, tipo de acceso) o dinámicos (detección de movimiento, reconocimiento facial). Debe permitir la integración futura de módulos de scoring automáticos (módulos de IA o criterios adaptativos).
  - **C.U. 9:** Visualiza feeds de cámaras priorizados según sus criterios.  
  - **C.U. 10:** Interviene mediante comunicación de voz.
  - **C.U. 11:** Interviene mediante solicitud de apertura de puerta.  
  - **C.U. 12:** Registra incidentes.
  - **C.U. 13:** Escalar alertas a móvil o policía.  
  - **C.U. 14:** Consulta el historial de eventos de un determinado feed o ubicación.
  - **C.U. 15:** Visualiza grabaciones históricas.
  - **C.U. 16:** Consulta información de las instalaciones que vigila.

- **Administrador de WolfSys**  
  - **C.U. 17:** Gestiona propiedades y configuraciones de servicios contratados, permitiendo configuraciones predefinidas (torre, complejo, torre ejecutiva).
  - **C.U. 18:** Consulta información de las propiedades.
  - **C.U. 19:** Crea, modifica y asigna roles a residentes, personal de servicio y agentes.
  - **C.U. 20:** Consulta información de los distintos usuarios.
  - **C.U. 21:** Da de alta o baja dispositivos (cámaras, lectores, tótems).  
  - **C.U. 22:** Revisa logs de auditoría.

- **Administrador del Edificio**  
  - **C.U. 23:** Gestiona los usuarios residentes de su edificio.
  - **C.U. 24:** Consulta información de los residentes de su edificio.
  - **C.U. 25:** Da de alta o baja dispositivos (cámaras, lectores, tótems) en su edificio.  
  - **C.U. 26:** Revisa logs de eventos en su edificio.
  - **C.U. 27:** Visualizar grabaciones históricas de su edificio desde la interfaz administrativa.

- **Personal Técnico de WolfSys**  
  - **C.U. 28:** Configura y gestiona equipos en instalaciones.  

- **Sistema “Tumimeras” (Proveedor de Videostreaming)**  
  - **C.U. 29:** Autentica cámaras.
  - **C.U. 30:** Entrega streams de video.  
  - **C.U. 31:** Provee almacenamiento en caliente de hasta 3 meses de video.  
  - **C.U. 32:** Expone endpoints HTTP para listar y consumir feeds disponibles.


## 6. Vista de Componentes de Alto Nivel
1. **Módulo de Ingestión de Video (“Gateway de Video”)**  
   - Conecta con Tumimeras para autenticar y recuperar streams de cámaras.  
   - Realiza balanceo de carga de feeds hacia los servicios de distribución de video.  
   - Garantiza enrutamiento seguro (TLS) y baja latencia en la transmisión.  

2. **Servicio de Distribución de Video (Streaming Backend)**  
   - Recibe flujos autenticados de Tumimeras y los expone a consumidores internos (consola de vigilancia, aplicación móvil).  
   - Administra el almacenamiento en caliente (hasta 3 meses) y maneja la recuperación de archivos históricos.  

3. **Consola Unificada de Vigilancia**  
   - Plataforma web para agentes de vigilancia.  
   - Consume feeds priorizados (estáticos y dinámicos) y muestra información contextual (datos de edificio, historial de eventos).  
   - Exponer acciones de intervención (voz, apertura de puerta) y registrar eventos de forma inmutable.

4. **Módulo de Scoring de Cámaras**  
   - Componente que mantiene puntuaciones iniciales de cámaras (criterios estáticos).  
   - Interfaz para actualizar puntajes manualmente y API para recibir puntajes dinámicos de futuros módulos de IA.  
   - Exponer puntajes a la consola de vigilancia para ordenamiento de feeds.

5. **CRM Central**  
   - Almacena información maestra de edificios, unidades y residentes.  
   - Registra todos los eventos de acceso, soporte y generación de PINs con inmutabilidad.  
   - Exponer APIs RESTful para consulta y modificación (con permisos RBAC).  

6. **Servicio de Control de Acceso**  
   - Lógica central de validación de tags, PINs y biometría.  
   - Coordina con Edge Controllers en cada edificio para sincronizar reglas y registros de acceso.  
   - Registra cada transacción de acceso en un log inalterable y notifica al CRM para mantener consistencia.

7. **Edge Controller (Controlador Local en Edificio)**  
   - Contenido en un dispositivo local que replica parte de la lógica de control de acceso.  
   - Almacena temporalmente reglas de validación y logs de accesos cuando no hay conectividad.  
   - Sincroniza datos con el backend central al restablecerse la conexión.  

8. **API Gateway y Autenticación**  
   - Puerta de entrada única para todos los servicios, gestionando autenticación y autorización centralizada.  
   - Aplica políticas de seguridad (TLS, rate limiting, validación de JWT/OAuth).  

9. **Aplicación Móvil**  
   - Cliente para residentes y administradores que consume APIs de video y CRM.  
   - Recibe notificaciones push para eventos de acceso o llamadas al portero.  
   - Permite generación de accesos temporales y envío de comandos de apertura a través del API Gateway.

10. **Interfaz Administrativa**  
    - Aplicación web para gestión de propiedades, usuarios, roles y dispositivos.  
    - Consume APIs del CRM y Servicio de Control de Acceso.  
    - Exponer auditoría de todas las acciones administrativas.

## 7. Consideraciones de Despliegue y Operación
- **Arquitectura Distribuida**  
  - Servicios centrales (Streaming Backend, CRM, Control de Acceso, Scoring, API Gateway) desplegados en entornos redundantes (zonas o regiones) para alta disponibilidad.  
  - Edge Controllers instalados en cada edificio, con capacidad de operación autónoma y sincronización periódica con el backend central.  
- **Mecanismos de Resiliencia**  
  - Balanceadores de carga para distribuir peticiones de video y APIs.  
  - Replicación de bases de datos (CRM) para tolerancia a fallos.  
  - Caché local (en Edge Controllers) para validar tags y PINs cuando hay fallo de conectividad.  
  - UPS en edificios para garantizar operación mínima ante cortes eléctricos.  
- **Seguridad y Red de Comunicación**  
  - Comunicación interna cifrada con TLS entre módulos centrales y con Edge Controllers.  
  - Redes segmentadas para separar tráfico de video, tráfico de control y tráfico administrativo.  
  - Políticas de firewall para restringir accesos externos solo a puertos y servicios necesarios (APIs, streaming).  
- **Manejo de Datos de Video**  
  - Almacenamiento en caliente de hasta 3 meses con replicación y backups periódicos.  
  - Políticas de archivado automático a almacenamiento frío para videos históricos (> 3 meses), gestionado según criterios de costo y retención.  
  - Mecánicas de purgado seguro para eliminar videos según políticas de retención y privacidad.  
- **Integración con Sistemas Externos**  
  - Conexión con Tumimeras como única fuente de ingestión de video.  
  - API de Scoring expositor para futuros módulos de IA que consuman streams de video o metadatos.  
  - Interoperabilidad con herramientas de notificaciones push (servicios de mensajería) para la aplicación móvil.  
- **Monitoreo y Observabilidad**  
  - Servicio de logging centralizado que recoja logs de acceso, eventos de interfase de agente, acciones de CRM y estado de Edge Controllers.  
  - Métricas de rendimiento de video (latencia, pérdida de paquetes), salud de servicios centrales (CPU, memoria, latencia de respuesta) y uso de recursos de Edge (sincronización, almacenamiento local).  
  - Alertas en tiempo real ante caídas de servicios, desconexiones prolongadas de Edge Controllers o latencia excesiva en streaming.

---

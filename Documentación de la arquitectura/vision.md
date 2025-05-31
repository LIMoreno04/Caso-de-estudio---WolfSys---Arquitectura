# Visión de la Arquitectura del Sistema WolfSys

## Propósito
Este documento describe la visión general de la arquitectura de la nueva plataforma de WolfSys. Expone los objetivos estratégicos y los principios rectores que guiarán el diseño de la solución, con el fin de:
- Proporcionar una guía clara para las decisiones arquitectónicas.  
- Alinear las expectativas de todos los stakeholders (equipo de desarrollo, representantes de la empresa, usuarios).  
- Establecer un marco de referencia para la evolución futura de la plataforma.

## Objetivos del sistema
1. **Modernizar la plataforma de portería virtual**  
   Sustituir la arquitectura heterogénea y altamente acoplada actual por una solución modular, escalable y fácil de mantener.  
2. **Mejorar la experiencia del usuario final**  
   Permitir que residentes, administradores y agentes de vigilancia interactúen de manera rápida y fluida con el sistema (cámaras en vivo, acceso remoto, notificaciones, CRM integrado).  
3. **Garantizar alta disponibilidad y resiliencia**  
   Asegurar que, ante cortes de conectividad o fallas en servicios centrales, cada edificio pueda operar con capacidades mínimas de forma autónoma.  
4. **Facilitar la integración de nuevas funcionalidades y tecnologías**  
   Crear una base sólida que permita la incorporación futura de modelos de inteligencia artificial, reconocimiento facial, scoring de cámaras, automatización de flujos, etc.  
5. **Optimizar costos operativos y de infraestructura**  
   Reducir la complejidad de mantenimiento y el gasto en licencias, aprovechando soluciones de código abierto y servicios en la nube cuando corresponda.

## Principios arquitectónicos
- **Modularidad y separación de responsabilidades**  
  Dividir el sistema en componentes independientes (módulos de videovigilancia, CRM, control de acceso, interfaz móvil, interfaz administrativa), de modo que cada uno cumpla funciones bien definidas y se pueda evolucionar de forma aislada.
- **Escalabilidad horizontal**  
  Diseñar componentes que puedan ampliarse agregando instancias en lugar de depender de un solo servidor monolítico, especialmente para el procesamiento de video y la gestión de usuarios.
- **Resiliencia y tolerancia a fallos**  
  Permitir que cada edificio continúe operando con funcionalidades básicas (acepto tags, PINs) incluso sin conexión a internet.  
- **Seguridad por diseño**  
  Incluir desde el principio mecanismos de autenticación, autorización y cifrado para proteger datos sensibles (video, identificadores, credenciales).  
- **Simplicidad y mantenibilidad**  
  Favorecer tecnologías y patrones de diseño que el equipo conozca bien (por ejemplo, contenedores Docker, microservicios o servicios desacoplados con APIs REST), evitando complejidad innecesaria.  
- **Apertura y estándares abiertos**  
  Utilizar protocolos y formatos ampliamente soportados (RTSP/HTTP para video, JSON o gRPC para APIs, OAuth 2.0 para autenticación) de manera que sea fácil integrar componentes de terceros en el futuro.
- **Consistencia de datos y trazabilidad**  
  Mantener registros inalterables de eventos críticos: accesos físicos, generación de PINs, logs de video, acciones de agentes. Esto es vital para auditorías y análisis posteriores.

## Atributos de calidad prioritarios
1. **Disponibilidad (High Availability)**  
   - Los servicios críticos (videovigilancia, control de acceso) deben estar operativos el 99.9% del tiempo.  
   - Implementar balanceo de carga y réplicas en zonas geográficas distintas.  
2. **Seguridad y confidencialidad**  
   - Cifrar datos en tránsito (TLS) y en reposo (bases de datos, almacenamiento de video).  
   - Control de acceso granular: definir roles para residentes, administradores, agentes de vigilancia y sistemas externos (CRM, AI).  
3. **Mantenibilidad**  
   - Documentar todas las APIs y componentes.  
   - Automatizar despliegues (CI/CD) y monitoreo (logs, métricas).  
4. **Escalabilidad**  
   - Permitir escalar el procesamiento de video (tumimeras y procesamiento propio) conforme aumente la cantidad de edificios o streams por edificio.  
   - Base de datos de CRM y eventos debe poder particionarse o escalar horizontalmente según crezca la base de usuarios.  
5. **Rendimiento**  
   - Latencia en transmisión de video no superior a 200 ms de extremo a extremo.  
   - Respuesta de la aplicación móvil y panel administrativo en menos de 2 segundos para operaciones críticas (consulta de feed en vivo, solicitud de PIN, apertura de puerta).  
6. **Flexibilidad y extensibilidad**  
   - Arquitectura pensada para incorporar futuros módulos de IA (scoring de cámaras, reconocimiento facial) sin alterar componentes existentes.  
   - APIs internas bien definidas y versionadas.

## Restricciones
- **Presupuesto académico**  
  No se pueden adquirir licencias comerciales costosas para el desarrollo de la arquitectura; se debe priorizar software libre o planes gratuitos.  
- **Diversidad de hardware**  
  Los edificios usan distintos dispositivos físicos (cámaras IP, lectores de tags, tótems de audio/video). Todos deben comunicarse mediante protocolos estándar (HTTP, RTSP) y codecs compatibles (H.264, AAC).  
- **Entorno de desarrollo**  
  El equipo trabajará con VS Code, GitHub y Docker.  
- **Tiempo limitado**  
  El proyecto tiene un plazo estipulado (fechas de entrega y defensa), por lo que no se profundizará en detalles de implementación, sino en la definición de la arquitectura y los principales artefactos (diagramas, ADRs, justificaciones).

## Tecnologías tentativas (sujetas a validación en ADRs)
- **Backend**: Node.js con Express o Python con FastAPI (servicios REST).  
- **Videovigilancia**: Integración con Tumimeras (streaming HTTP/RTSP) y posible uso de un microservicio para procesar feeds (contenerizado).  
- **Base de datos**: PostgreSQL para CRM y eventos; Redis para caché de scoring de cámaras.  
- **Control de acceso local**: Microcontroladores (o Raspberry Pi) con software embebido que se sincroniza con un componente Edge Service.  
- **Autenticación/Autorización**: OAuth 2.0 o JWT para tokens.  
- **Frontend web**: React o Angular (panel de monitoreo, CRM, interfaz administrativa).  
- **Aplicación móvil**: Flutter o React Native.  
- **Infraestructura**: Docker + Kubernetes (opcional), con despliegue inicial en un VPS o instancia EC2/ECS (parámetro académico).  
- **Diagramación**: draw.io para diagramas de arquitectura (C4, despliegue, secuencia).  
- **Documentación**: Markdown y GitHub Pages o MkDocs para publicar manuales.
# Contexto del Caso de Estudio WolfSys

## Descripción de la empresa
WolfSys es una empresa tecnológica especializada en soluciones de portería virtual para edificios residenciales y oficinas. Ha reemplazado el portero tradicional con un sistema digital que combina dispositivos físicos (cámaras, lectores de tags, sensores, tótems con micrófono/video) y una plataforma de software que permite controlar accesos y monitorear ingresos en tiempo real.

- **Alcance actual**: Más de 500 edificios cubiertos, alrededor de 50.000 usuarios (residentes, empleados de servicio, administradores).  
- **Servicios ofrecidos**: Videovigilancia en vivo, apertura remota de puertas, comunicación entre visitantes y residentes, gestión de accesos peatonales y vehiculares.

## Situación actual
1. **Arquitectura heredada y heterogénea**  
   - El sistema original fue implementado para videovigilancia remota (producto “Tumimeras”). Con el tiempo, se fueron añadiendo módulos (CRM, control de acceso, notificaciones) sin un diseño coherente.  
   - Los componentes están muy acoplados y son difíciles de modificar o escalar.  
   - Cada nuevo requerimiento se incorpora “sobre el código existente”, provocando deuda técnica.

2. **Limitaciones en funcionalidad y experiencia**  
   - Los clientes quieren atender al portero desde el celular, sin depender de una sala de monitoreo fija.  
   - Falta de integración: la aplicación de vigilancia no muestra datos contextuales (historial de eventos, información de edificio).  
   - No hay scoring de cámaras ni priorización automática de feeds; se visualizan en orden predefinido o aleatorio.

3. **Fallas operativas y de resiliencia**  
   - La lógica de control de acceso (tags, PIN, biometría) depende exclusivamente del servicio central. Al fallar la conexión, el edificio queda sin control, salvo por soluciones manuales.  
   - Cada edificio tiene hardware distinto (distintas marcas de cámaras, tótems, lectores), lo que dificulta la uniformidad del software.

4. **Necesidades emergentes**  
   - **Control móvil**: Agentes y residentes quieren ver cámaras en vivo desde su teléfono y recibir notificaciones de llamadas o intentos de acceso.  
   - **Accesos temporales**: Generar códigos o accesos temporales para visitas desde la app.  
   - **Reconocimiento facial y analítica de video**: Incorporar modelos de IA para detectar movimiento, presencia humana, e incluso reconocer rostros.  
   - **Automatización de integraciones**: Conectar la aplicación de monitoreo con el CRM, de modo que, por ejemplo, al recibir un incidente, se genere automáticamente un ticket en el CRM.  
   - **Gestión centralizada de clientes**: El CRM actual no centraliza toda la historia de interacciones (solicitudes de soporte, registros de acceso, eventos de video).

5. **Requerimientos no funcionales clave**  
   - **Alta disponibilidad**: El sistema debe estar operativo casi todo el tiempo; no pueden producirse caídas frecuentes.  
   - **Baja latencia en video**: La transmisión de cámaras en vivo debe ser fluida, con latencia mínima (menos de 200 ms).  
   - **Seguridad y trazabilidad**: Los accesos físicos y cambios de configuración deben registrarse de forma inalterable.  
   - **Operación local con conectividad limitada**: Cada edificio debe poder validar tags o PINs aún si no hay internet, asumiendo capacidades reducidas (por ejemplo, solo tags).  
   - **Escalabilidad**: A medida que crezca la base de edificios y usuarios, el sistema debe poder desplegar más instancias de servicios sin reescribir componentes enteros.

## Motivación del rediseño
- La arquitectura actual impide desplegar nuevas funcionalidades ágilmente.
- La heterogeneidad de componentes y proveedores dificulta el mantenimiento.
- WolfSys desea consolidar todo el flujo de información (video, CRM, control de acceso) en una plataforma coherente.
- Se busca sentar las bases para incorporar analítica avanzada y mejorar la eficiencia operativa.
- El rediseño no solo debe atender las necesidades presentes, sino contemplar la visión futura: escalabilidad a miles de edificios, adopción de IA y expansión de servicios.

## Desafíos principales
1. **Compatibilidad con hardware diverso**  
   - Dispositivos de distinta antigüedad y proveedor deben continuar funcionando bajo la nueva plataforma.  
   - Todos hablan protocolos IP (HTTP, RTSP), pero es necesario un intermediario (Edge Service) que facilite la interoperabilidad.

2. **Garantizar funcionamiento en entornos desconectados**  
   - Implementar un componente local (Embedded Access Controller) que almacene reglas de acceso y logs cuando no haya conexión.  

3. **Seguridad y gestión de datos sensibles**  
   - Manejar flujos de video en tiempo real con cifrado, proteger la privacidad de los residentes y cumplir regulaciones locales.  
   - El CRM debe cumplir con estándares de confidencialidad para datos personales.

4. **Evolución gradual y migración**  
   - No se puede reemplazar de golpe toda la plataforma; debe diseñarse un plan de migración que permita coexistir con el sistema antiguo hasta que el nuevo esté listo.  
   - Definir puntos de integración (APIs, colas de mensajes) que sirvan como “puentes” entre lo viejo y lo nuevo.

## Alcance de este caso de estudio
- Definir la **arquitectura lógica y física** de la nueva plataforma (diagramas C4, despliegue, secuencia).  
- Establecer **Architectural Decision Records (ADRs)** para justificar cada elección de tecnología o patrón arquitectónico.  
- Documentar los **principios de diseño**, **módulos principales** (Videovigilancia, CRM, Control de Acceso, Interfaz Móvil, Interfaz Administrativa) y sus interrelaciones.  
- Proponer un esbozo de **plan de migración** (no implementación detallada).  
- Elaborar la **visión general** (visión.md), el **contexto de negocio y técnico** (este archivo), y un **índice organizativo** (índice.md) que guíe la consulta de todos los documentos generados.
# Contexto del Caso de Estudio WolfSys
Este documento resume y describe el contexto de la compañía, así como sus requisitos (en esencia el documento con la letra del problema).
## Descripción de WolfSys
WolfSys es una empresa tecnológica dedicada al desarrollo y operación de soluciones de portería virtual para edificios residenciales y oficinas. Su propuesta reemplaza al portero tradicional mediante una combinación de dispositivos físicos (cámaras, lectores de tags, sensores de movimiento y tótems con micrófono, audio y visor de video) conectados a una plataforma digital que permite controlar los accesos de forma remota, segura y eficiente.

- Actualmente, más de 500 edificios utilizan los servicios de WolfSys para gestionar accesos peatonales y vehiculares, monitorear ingresos en tiempo real y facilitar la comunicación entre visitantes y residentes.  
- Esto abarca cerca de 50.000 usuarios: residentes, funcionarios, personal de servicio y administración.

## Historia y Estado Actual de la Plataforma
- **Origen centrado en videovigilancia:**  
  La plataforma original se diseñó para brindar videovigilancia remota mediante el producto “Tumimeras”. Con el tiempo, se han añadido nuevas funcionalidades (CRM, control de acceso, notificaciones), pero sin un diseño arquitectónico congruente.  
- **Problemas derivados del crecimiento orgánico:**  
  - Las implementaciones “sobre la marcha” llevaron a un sistema con alto acoplamiento y baja coherencia, lo que dificulta modificaciones o el despliegue ágil de nuevas funciones.  
  - A pesar de estas deficiencias, el sistema ha funcionado satisfactoriamente y permitido el crecimiento de la empresa (hasta ahora).

## Demandas y Necesidades Emergentes
En los últimos años, han surgido nuevas exigencias del mercado y del equipo interno de WolfSys:

1. **Interacción móvil con el portero virtual:**  
   - Los clientes quieren atender al portero directamente desde el celular y ver cámaras en vivo desde la aplicación móvil.  
2. **Acceso temporal para visitas:**  
   - Generar códigos o accesos temporales (PINs) desde la app.  
3. **Reconocimiento facial y analítica de video:**  
   - Usar modelos de análisis de imagen para detectar movimiento, presencia humana o reconocimiento facial.  
4. **Automatización de integraciones:**  
   - Actualmente, ciertas integraciones (p. ej., contacto con cliente desde el software de cámaras) son manuales; se busca automatizarlas.

## Limitaciones de la Arquitectura Actual
- **Heterogeneidad de componentes y proveedores:**  
  - Dispositivos físicos (cámaras, tags, tótems) y servicios de backend muy acoplados que dificultan el despliegue ágil de nuevas funcionalidades.  
- **Variabilidad en entornos y condiciones de red:**  
  - Cada edificio cuenta con dispositivos diversos y condiciones de conectividad distintas, lo que exige una solución capaz de operar parcialmente ante fallos de red o servicios centrales.  
- **Escasa alineación con la visión futura:**  
  - La arquitectura actual no facilita la incorporación de módulos de IA para reconocer comportamientos de cada edificio, registro de eventos y reportes avanzados, etc.

## Decisión de Rediseño
Por todo lo anterior, WolfSys ha decidido iniciar un rediseño profundo de su plataforma. El objetivo es alcanzar una arquitectura que sea:

- **Flexible y escalable,** para soportar la cantidad creciente de edificios y usuarios.  
- **Resiliente,** capaz de funcionar con funcionalidades básicas incluso ante fallos de conectividad o energía.  
- **Extensible,** permitiendo incorporar futuras capacidades de IA, scoring, automatización, y nuevos servicios con mínimo impacto en el sistema existente.

## Requerimientos Funcionales y No Funcionales

### 1. Videovigilancia
- WolfSys despliega cámaras en accesos, espacios comunes y perímetros.  
- Los streams de video se procesan con “Tumimeras” (licencia) para transmisión a escala.  
- Cada stream requiere autenticación que identifique la cámara origen.  
- Existen endpoints HTTP en Tumimeras para listar y acceder a feeds en vivo.  
- **Atributo crítico:** baja latencia en la transmisión de video.

### 2. Agentes de Vigilancia
- Nuevo objetivo: construir una aplicación única que integre toda la información relevante para agentes de vigilancia:
  - Contexto de edificio (historial de eventos, información del residente).  
  - Control de cámaras, intervención de voz, apertura de puertas, registro de incidentes, escalamiento a móvil/policía.  
- **Scoring de cámaras:**  
  - Priorizar feeds según criterios (ubicación, tipo de edificio, criticidad, detección de movimiento, presencia humana, reconocimiento facial).  
  - Inicialmente manual, con vistas a alimentar el scoring con modelos de IA o reglas contextuales.

### 3. CRM
- Debe centralizar la información de cada edificio y su historial:  
  - Infraestructura física instalada.  
  - Registros de accesos, solicitudes de soporte, generación de PINs, revisiones de video, etc.  
- El CRM servirá como fuente para otros módulos (aplicación móvil, panel de vigilancia).  
- **Ejemplo de uso:**  
  - Un residente solicita un video ocurrido hace dos semanas; el sistema debe permitir buscar, registrar y resolver la solicitud desde el CRM.

### 4. Control de Acceso
- Múltiples métodos de acceso:  
  - Llave digital (tag), código PIN, intervención remota (agente o residente), biometría (huella/reconocimiento facial).  
- Lógica de validación “centralizable” pero capaz de operar parcialmente sin conectividad (cada edificio debe funcionar de forma autónoma con capacidades mínimas, p. ej. solo tags).  
- Sistemas UPS en edificios para mantener la operación ante cortes eléctricos.  
- Todos los accesos (tags, códigos) deben gestionarse desde distintas aplicaciones y llevar un registro riguroso e inalterable.

### 5. Aplicación Móvil para Usuarios
- Módulo que permita a residentes y administradores:  
  - Ver cámaras en vivo.  
  - Recibir notificaciones de intentos de ingreso o llamadas desde el portero.  
  - Generar accesos temporales (PINs) y abrir puertas a distancia.  
- Debe integrarse con el CRM y el sistema de monitoreo, reflejando datos en tiempo real.

### 6. Interfaz Administrativa
- Permitir la gestión de usuarios, propiedades e instalaciones (hardware).  
- Control de configuraciones sensibles (tipos de usuario, servicios contratados, equipos en cada punto).  
- Registro de todas las actividades de administración (auditoría).

### 7. Otras Consideraciones Técnicas
- WolfSys lleva más de 5 años instalando porteros con distintos terminales (comportamientos y protocolos variados), pero todos ofrecen conectividad IP (Ethernet) y usan encodings comunes de audio y video.  
- Las cámaras se conectan por Wi-Fi o Ethernet con protocolos y codecs estandarizados.  
- Se debe permitir dar de alta cámaras y dispositivos variados dentro de un mismo edificio.  
- **Almacenamiento de video:**  
  - Videos de hasta 3 meses en “modo caliente” (acceso instantáneo).  
  - Videos mayores a 3 meses pueden archivarse para almacenamiento a menor costo, manteniendo históricos para entrenar modelos en el futuro.

---

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

---

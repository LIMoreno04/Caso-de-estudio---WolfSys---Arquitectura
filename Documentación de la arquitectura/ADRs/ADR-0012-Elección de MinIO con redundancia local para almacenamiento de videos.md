# ADR-0012: Elección de MinIO con redundancia local para almacenamiento de videos

## ID

0012

## Título

Elección de MinIO con redundancia local para almacenamiento de videos

## Fecha

08/06/2025

## Estado

Decidido

## Contexto

El sistema debe almacenar grandes volúmenes de videos generados por cámaras de vigilancia y otros dispositivos, tanto en tiempo real como en modo archivo (“almacén frío”). Inicialmente se consideró el uso de servicios de almacenamiento en la nube como Amazon S3 para esta tarea.

Sin embargo, uno de los requerimientos clave es que los videos estén disponibles localmente en los servidores WolfSys, sin depender de conexión a internet ni de servicios externos. Además, es necesario asegurar la **redundancia y durabilidad** de los archivos ante posibles fallas de hardware.

## Decisión tomada

Se optó por utilizar **MinIO** como solución de almacenamiento tipo S3, desplegada en los servidores locales de WolfSys, con configuración de **redundancia automática** mediante múltiples discos o nodos replicados.

## Factores decisivos

1. **Autonomía total**: El sistema funciona incluso sin conexión externa.
2. **Compatibilidad S3**: MinIO es API-compatible con S3, permitiendo integrar librerías y herramientas comunes.
3. **Alto rendimiento**: MinIO ofrece excelente rendimiento para cargas de lectura/escritura en video.
4. **Redundancia configurable**: Permite definir replicación por nodos o por discos, según necesidad.
5. **Costo cero de terceros**: No se incurre en costos por transferencia ni almacenamiento en la nube.
6. **Open source y autoalojado**: Facilita la gestión y el control completo del entorno de almacenamiento.

## Alternativas consideradas

- **Amazon S3**: servicio maduro y escalable, pero dependiente de conectividad y con costos por uso.
- **Backblaze B2 / Wasabi**: opciones más económicas que S3, pero igualmente remotas.
- **Ceph**: solución distribuida muy robusta pero con mayor complejidad operativa que MinIO.
- **NAS tradicional**: no ofrece interfaz S3 ni características modernas de objetos o replicación nativa.

## Pros y Contras

**Pros:**

- Control total sobre los datos y la infraestructura.
- Evita dependencia de servicios externos.
- Permite escalabilidad futura sin cambiar el modelo de integración.
- Reducción de costos a largo plazo.
- Compatible con sistemas existentes que usan APIs S3.

**Contras:**

- Requiere más esfuerzo de mantenimiento (monitoring, backups, upgrades).
- Necesidad de dimensionar correctamente la infraestructura local.
- Menor tolerancia a fallos globales si no se combina con backup remoto.

## Consecuencias

El componente de almacenamiento de videos (“La Heladera”) se implementará usando MinIO con redundancia local. En la documentación y diagramas, este servicio se clasifica como **servicio interno** autoalojado y cumple el rol de cold storage. Se evitarán dependencias de servicios cloud, y se establecerán rutinas de verificación de integridad, backup y failover para garantizar la resiliencia de los datos almacenados.

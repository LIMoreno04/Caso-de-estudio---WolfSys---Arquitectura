# Modelo Operacional – Arquitectura WolfSys

Este documento describe la arquitectura del sistema WolfSys, orientada a la gestión, procesamiento y almacenamiento eficiente de videos provenientes de dispositivos de vigilancia, con integración de acceso y servicios internos.

---

## Componentes Principales

### 1. Dispositivos Cliente
- **Móvil, PC**  
  Interactúan con el sistema mediante el frontend. Acceden a métricas, reportes, visualización de videos y gestión operativa.

---

### 2. Servidores WolfSys

#### Frontend
- Aplicación web de acceso para usuarios y operadores.
- Hospedada en un servidor dedicado para garantizar disponibilidad.

#### Servidores de Procesamiento
- **Procesamiento 1 y 2**: 
  - 16 núcleos, 64GB RAM, 2TB SSD.
  - Se encargan del análisis de video, transcodificación, detección de eventos y procesamiento paralelo.
- **Módulo Removible**:
  - 4 núcleos, 16GB RAM, 512GB SSD.
  - Permite procesamiento portátil o redundante.

#### Edge Gateway
- Servidor físico Linux, 8 núcleos, 32GB RAM, 1TB SSD.
- Funciona como punto de integración con el Edificio Local.
- Administra flujos entrantes de datos en tiempo real.

#### Servidor de Control
- 8 núcleos, 32GB RAM, 512GB SSD.
- Coordina tareas de monitoreo, alertas y balanceo de carga.

---

### 3. Bases de Datos

#### Main: DB de Videos
- 8 núcleos, 64GB RAM, 20TB SSD RAID.
- Almacenamiento primario de video proveniente de las tumimeras.
- Capacidad diseñada para grandes volúmenes de datos.

#### Réplica: DB de Videos
- Espejo en RAID para asegurar disponibilidad y tolerancia a fallos.

#### DB Principal
- 8 núcleos, 2GB RAM, 1TB SSD.
- Manejo de datos de configuración, usuarios, logs, accesos.

#### “La Heladera” (almacenamiento secundario)
- 8 núcleos, 16GB RAM, 1TB SSD.
- Nodo para almacenamiento intermedio o recuperación de video menos consultado.

---

### 4. Tumimeras (Nodos de Video Caliente)
- 4 núcleos, 8GB RAM, 2TB SSD.
- Dispositivos de vigilancia que graban y almacenan video localmente antes de enviarlo al sistema.
- Conectados directamente a la DB de Videos para descarga de datos en caliente.

---

## Edificio Local

#### Gateway Edificio
- CPU 4 núcleos, 8GB RAM, 64GB SSD.
- Puente entre dispositivos físicos (acceso, audio) y el Edge Gateway.

#### Edge Controller
- CPU 2 núcleos, 2GB RAM, 16GB SSD.
- Controla dispositivos de audio, porteros automáticos, alertas.

#### DB Local
- Almacenamiento mínimo para logs locales o eventos temporales.

---

## Justificación Técnica

### Almacenamiento
- Las bases de datos RAID de 20TB fueron dimensionadas considerando cargas de video provenientes de hasta cientos de tumimeras.
- Se estima un consumo de hasta 100MB diarios por cámara, lo que equivale a 18TB anuales para 200 cámaras.

### Procesamiento Distribuido
- Dos servidores de procesamiento trabajan en paralelo para tareas intensivas.
- El módulo removible permite traslado o recuperación portátil.

### Redundancia y Alta Disponibilidad
- Replicación entre bases de datos.
- Edge Gateway interconectado con edificio local y servidores internos.

--

## Modelo operacional

![Modelo operacional](Diagramas/Exportados/Modelo-operacional.png)
<!-- Asegurate de que el archivo esté en la misma carpeta o ajustá la ruta -->

---

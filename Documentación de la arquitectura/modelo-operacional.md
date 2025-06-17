# Modelo Operacional

Este documento describe la arquitectura del sistema WolfSys, orientada a la gestión, procesamiento y almacenamiento eficiente de videos provenientes de dispositivos de vigilancia, con integración de acceso y servicios internos.

---

## Componentes Principales

### 1. Dispositivos Cliente
- **Móvil, PC**  
  Interactúan con el sistema mediante el frontend. Acceden a las aplicaciones web/mobile.

---

### 2. Servidores WolfSys

#### Frontend
- Aplicación web/mobile de acceso para usuarios, administrativos y agentes de vigilancia.
- Hosteado en un servidor.

#### Servidores de Procesamiento
- **Procesamiento 1 y 2**: 
  - 64 núcleos, 128 GB RAM, 512 GB SSD.

- **Módulo Removible**:
  - 4 núcleos, 16 GB RAM, 256 GB SSD.

#### Edge Gateway
- 16 núcleos, 64 GB RAM, 256 GB SSD.
- Administra flujos entrantes de datos en tiempo real.

#### Servidor de Control
- 8 núcleos, 32 GB RAM, 512 GB SSD.
- Coordina tareas de monitoreo, alertas y balanceo de carga.

---

### 3. Bases de Datos

#### Main: DB de Videos
- 8 núcleos, 64 GB RAM, 8x30 TB HDD con RAID 6.
- 180 TB de almacenamiento efectivo.
- Almacenamiento en frío primario de videos provenientes de tumimeras.
- Capacidad diseñada para grandes volúmenes de datos.

#### Réplica: DB de Videos
- Espejo de la anterior para asegurar disponibilidad durante las transferencias, y para mayor tolerancia a fallos.

#### DB Principal
- 8 núcleos, 16 GB RAM, 4 TB SSD con RAID 1.
- 2TB de almacenamiento efectivo.
- Manejo de datos de configuración, usuarios, logs, accesos.


---

### 4. Tumimeras
- 32 núcleos, 128 GB RAM, 8x4 TB SSD en RAID 6.
- 24 TB almacenamiento efectivo.
- Almacenamiento de hasta 3 meses de videos en caliente.
- Conectado directamente a el Edge Gateway para transmisión de videos almacenados en caliente.
- Conectado directamente a la DB de Videos para descarga de datos a frío.

---

### 5. "La Heladera" (Módulo de Enfriamiento de Video)
- 32 núcleos, 256 GB RAM, 1 TB SSD.
-  **Almacena:**
   - El `.mp4.zst` en una de las **DB Históricas de Video (MinIO)**.
   - Los metadatos en una **DB de Datos Históricos (PostgreSQL)**, referenciados por la misma ID.

---

## Edificio Local

### Gateway Edificio
- CPU 4 núcleos, 8 GB RAM, 64 GB SSD.
- Puente entre dispositivos físicos (acceso, audio) y el Edge Gateway.

---

### Edge Controller
- CPU 2 núcleos, 2 GB RAM, 16 GB SSD.
- Controla dispositivos de audio, porteros automáticos, alertas.

---

### DB Local
- CPU 4 núcleos, 8 GB RAM, 512 GB HDD.
- Almacenamiento mínimo para logs locales o eventos temporales.
- Se basa en SQLite, el hardware específico dependerá del edificio y sus necesidades.

---


## Justificación Técnica

### **Almacenamiento**  
  Los almacenamientos para videos fríos y calientes, con 180 TB y 24 TB efectivos respectivamente, se dimensionaron para manejar cargas de cientos de edificios, estimando un consumo de hasta 100 MB diarios por cámara (aprox. 91 TB anuales, o 22 TB por trimestre, para 2 500 cámaras).  
  Adicionalmente, el uso de RAID ofrece beneficios en seguridad, resiliencia y mejoras de performance.   
### **La Heladera**  
  Dada la tarea intensiva de retirar cientos de GB de videos cada noche, convertirlos a formato .zst, y enviarlos a ser archivados; se le asigna importantes cantidades de RAM y capacidad de procesamiento a este módulo en concreto.  
### **Procesamiento Distribuido**  
  Dos servidores de procesamiento en paralelo ejecutan tareas intensivas (cada una tiene tareas preasignadas, no es paralelismo puro), mientras que el módulo removible facilita futuras actualizaciones (p. ej. incorporación de scoring con IA).  
### **Redundancia y Alta Disponibilidad**  
  La réplica de la base de datos de videos asegura continuidad operativa durante tareas de mantenimiento o fallos, especialmente mientras trabaja La Heladera.  
### **En cuanto a las conexiones**  
  Se ven claramente representadas en el diagrama. La idea principal es que si la conexión transmitirá videos, debe ser 25 GbE, adicionalmente usando HTTP Live Streaming para los streams en vivo; y para las demás conexiones, según su nivel estimado de demanda se les asigna 10 GbE o 1 GbE.

--

## Modelo operacional

![Modelo operacional](Diagramas/Exportados/Modelo-operacional.png)


# ADR-0009: Gestión del almacenamiento en frío mediante el componente "La Heladera"


## ID
0009

## Título
Gestión del almacenamiento en frío mediante el componente "La Heladera"

## Fecha
07/06/2025

## Estado
Tomada 

## Contexto
El sistema mantiene videos en almacenamiento en caliente, 
donde están disponibles por un período máximo de 3 meses para acceso rápido. Una vez superado ese período, 
dichos videos ya no necesitan estar disponibles de forma inmediata, pero deben persistir a largo plazo para 
consultas con menor frecuencia. Esto requiere una solución de almacenamiento en frío, 
optimizada para bajo costo y menor prioridad de acceso.

## Decisión tomada
Se decidió incorporar el componente La Heladera como responsable de gestionar el almacenamiento en frío de 
los videos históricos. Sus responsabilidades incluyen:
- Consultar periódicamente la base de datos de Tumimeras para identificar videos cuya antigüedad haya superado los 3 meses.
- Extraer esos videos de la base de datos de Tumimeras.
- Convertir los videos al formato de archivo adecuado (en nuestro caso, una representación en string de bits puros del video).
- Almacenar dichos archivos en la base de datos de almacenamiento en frío.

## Factores decisivos
1. Desacoplar el almacenamiento en caliente y en frío, evitando que un solo componente maneje ambas lógicas.
2. Optimizar recursos: mover a almacenamiento de menor costo los videos que no requieren acceso frecuente.

## Alternativas
- Tumimeras gestiona todo el ciclo de vida, incluyendo el archivado: Rechazada para evitar acoplar responsabilidades múltiples en un solo componente y mantener simple su diseño.
- Usar un sistema de archivos o almacenamiento externo directamente: Considerado, pero se prefirió una solución integrada al ecosistema actual del sistema, con control del formato y proceso de migración.

## Pros y Contras

**Pros:**
- Separación clara de responsabilidades: Tumimeras se enfoca en el acceso reciente, 
mientras que La Heladera administra la persistencia a largo plazo.
- Reducción de costos: se minimiza el uso de almacenamiento en caliente, que es más costoso, 
al migrar contenidos antiguos.

**Contras:**
- Retrasos en el acceso: los videos almacenados en frío pueden requerir más tiempo para ser recuperados.
- Mayor complejidad operativa: se requiere una tarea o proceso adicional para monitorear y migrar videos.
- Duplicación temporal de datos: puede haber un período en el que el video esté tanto en Tumimeras como en
 el almacenamiento en frío hasta que se complete la migración y eliminación.

## Consecuencias
- Uso eficiente del almacenamiento en caliente: al mover automáticamente los videos antiguos fuera de Tumimeras, se libera espacio y se previenen sobrecargas en el sistema de acceso rápido.
- Reducción de costos operativos: al utilizar un almacenamiento más barato para contenido que no requiere acceso inmediato, se optimiza el gasto en infraestructura.
- Mayor durabilidad de los datos: los videos migrados pueden conservarse por tiempo indefinido sin comprometer el rendimiento de los servicios activos.
- Escalabilidad del sistema: se facilita el crecimiento del sistema sin comprometer la eficiencia del almacenamiento en caliente.
- Modularidad: separar la lógica de archivado en un componente dedicado (La Heladera) permite mantener y evolucionar cada parte del sistema de forma independiente.
  

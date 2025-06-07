# ADR-008: El componente Tumimeras se encarga de gestionar el almacenamiento de los videos históricos.

## ID
0008

## Título
Gestión de los videos históricos.

## Fecha
[DD/MM/AAAA]

## Estado
Tomada 

## Contexto
El sistema necesita que los videos grabados estén disponibles para su acceso de forma rápida y eficiente durante los primeros 3 meses posteriores a su creación. Se considera almacenamiento en caliente aquel que permite un acceso inmediato y con baja latencia a dichos videos.

## Decisión tomada
Se decidió que el componente Tumimeras será responsable de gestionar el almacenamiento en caliente de los videos, garantizando su disponibilidad durante los primeros 3 meses luego de su grabación. 

## Factores decisivos
1. Necesidad de acceso con una demora de hasta 3 segundos a videos durante los primeros 3 meses.
2. Se desea mantener a Tumimeras enfocado únicamente en la gestión en caliente.
3. Simplificación de la arquitectura al centralizar el almacenamiento temporal en un único componente.
4. Optimización del rendimiento en la lectura de videos recientes.

## Alternativas
- Almacenamiento compartido o distribuido entre varios componentes: Se evaluó permitir que otros componentes gestionaran el almacenamiento histórico, pero esto generaba duplicación de responsabilidades y complejidad adicional en la arquitectura.
- Tumimeras como gestor completo del ciclo de vida (incluyendo migración post-3 meses): Se descartó esta opción para mantener Tumimeras enfocado en el acceso y gestión en caliente. Encargarle también la migración o eliminación complicaría su diseño y lo acoplaría innecesariamente con otras capas de almacenamiento.

## Pros y Contras de [nombre de la decisión]

**Pros:**
- Centralización de responsabilidades: Tumimeras concentra la lógica de acceso y almacenamiento de los videos recientes, simplificando la arquitectura.
- Acceso rápido y consistente: Se garantiza baja latencia en el acceso a los videos recientes.

**Contras:**
- Responsabilidad limitada: Otra parte del sistema deberá encargarse explícitamente del manejo de los videos que superan los 3 meses, lo cual requiere coordinación.
- Requiere planificación de almacenamiento: Debe dimensionarse cuidadosamente el espacio necesario para almacenar hasta 3 meses de video, lo cual puede ser costoso dependiendo del volumen.

## Consecuencias
La consulta de contenido durante los primeros 3 meses posteriores a su generación será rápida y consistente, ya que el propio componente se encarga tanto de recibir la consulta por los videos históricos, como del almacenamiento.

## Comentarios (opcional)
[Cualquier detalle adicional o nota.]

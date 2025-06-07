# ADR-0006: Unificación de la gestión de datos en un único componente

## ID

0006

## Título

Unificación de la gestión de datos en un único componente Gestor de identidades.
## Fecha

06/06/2025

## Estado

Tomada

## Contexto

El sistema gestiona información relacionada con usuarios (residentes, técnicos, administrativos), dispositivos (puertas, lectores), accesos y edificios. Inicialmente se consideró separar la lógica de gestión de cada uno de estos dominios en módulos independientes.

Sin embargo, estos datos están fuertemente relacionados: un residente está asignado a un edificio, las puertas pertenecen a edificios, y los accesos se definen a partir de esa relación. Separar estos dominios generaría un alto nivel de acoplamiento entre módulos, afectando la mantenibilidad y consistencia de la información.

## Decisión tomada

Se decide unificar la gestión de estos datos en un único componente: el **Gestor de identidades**, que será responsable de la administración de usuarios, dispositivos y edificios de forma centralizada.

## Factores decisivos

1. Alta interdependencia entre los datos.
2. Evitar acoplamiento innecesario entre servicios separados.
3. Facilitar la consistencia de la información.
4. Simplificar la lógica de actualización, validación y sincronización.

## Alternativas

- **Separar la gestión por dominio (usuarios, edificios, dispositivos, etc.):** genera múltiples servicios interconectados con relaciones cruzadas complejas.
- **Unificar en un único componente (opción elegida):** reduce la complejidad de integración y evita problemas de sincronización.

## Pros y Contras de la decisión tomada

**Pros:**

- Evita duplicación de datos y lógica redundante.
- Facilita la implementación de reglas de negocio coherentes.
- Reduce la complejidad de sincronización entre servicios.

**Contras:**

- Mayor volumen de responsabilidad en un solo componente.
- Requiere cuidado en el diseño para evitar convertirse en un monolito inflexible.

## Consecuencias

El Gestor de identidades será el componente central que administra usuarios, dispositivos y edificios. A futuro, si el sistema crece significativamente, podría considerarse desacoplar algunas funcionalidades, pero partiendo desde una base unificada se garantiza consistencia y menor complejidad inicial.

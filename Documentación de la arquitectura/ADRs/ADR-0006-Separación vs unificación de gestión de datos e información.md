# ADR-0006: Separación vs unificación de gestión de datos e información

## ID

0006

## Título

Separación vs unificación de gestión de datos e información

## Fecha

06/06/2025

## Estado

Tomada

## Contexto

El sistema manipula distintos tipos de datos: usuarios, puertas, accesos, eventos, videos, etc. Es necesario decidir si todo se maneja de forma centralizada o si separamos la lógica de gestión de cada dominio.

## Decisión tomada

Se opta por una **separación de dominios** para la gestión de datos e información, de modo que cada tipo de entidad (CRM, videos, accesos) sea tratado con servicios y modelos propios.

## Factores decisivos

1. Reducción del acoplamiento.
2. Facilita cambios y mantenimiento por partes.
3. Refleja los distintos actores y sus contextos.

## Alternativas

- **Unificar toda la gestión en un único modelo o servicio:** más rápido al principio pero menos escalable.
- **Separar dominios de gestión (opción elegida):** organiza mejor el crecimiento del sistema.

## Pros y Contras de la decisión tomada

**Pros:**

- Escalabilidad.
- Facilita la implementación de reglas específicas por dominio.
- Mejor organización del código y de la base de datos.

**Contras:**

- Mayor cantidad de servicios o módulos.
- Requiere una buena definición de límites de cada dominio.

## Consecuencias

Cada módulo del sistema (CRM, control de accesos, video) tendrá su propia lógica de negocio y persistencia, posiblemente separados en clases, APIs o incluso microservicios si el proyecto escala.

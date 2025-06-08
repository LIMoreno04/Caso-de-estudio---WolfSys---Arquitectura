# ADR-0005: Selección preliminar de patrón de arquitectura por capas

## ID

0005

## Título

Selección preliminar de patrón de arquitectura por capas

## Fecha

06/06/2025

## Estado

Decidido

## Contexto

El sistema requiere una estructura clara, escalable y mantenible que permita abordar diferentes dominios funcionales (acceso, video, CRM) sin perder cohesión. Se busca facilitar la comprensión del sistema por parte del equipo de desarrollo y permitir una futura evolución hacia arquitecturas más desacopladas.

En base al diseño actual, que ya distribuye responsabilidades en capas bien definidas (presentación, borde, servicios, negocio y datos), se considera adoptar el patrón de arquitectura en capas como base organizativa.

## Decisión tomada

Se elige provisionalmente una arquitectura basada en capas, siguiendo el estilo Multi-Tier. El diseño actual contempla al menos cinco capas explícitas:

- **Capa de presentación**: aplicaciones móviles, web y administrativa.
- **Capa de borde**: gateway unificado (B.O.B.) que centraliza el ingreso al sistema.
- **Capa de servicios de aplicación**: APIs que exponen las funcionalidades a través de interfaces REST.
- **Capa de negocio**: módulos internos (CRM, control de acceso, scoring, etc.) donde reside la lógica de dominio.
- **Capa de datos**: almacenamiento persistente centralizado y distribuido.

## Factores decisivos

1. Separación clara de responsabilidades entre capas.
2. El diagrama actual ya sigue este patrón naturalmente.
3. El equipo tiene experiencia con esta estructura.
4. Facilita el testing, el desarrollo distribuido y el mantenimiento.
5. Permite escalar componentes de forma gradual (ej. migración a microservicios en el futuro).

## Alternativas

- **Arquitectura en capas (opción preseleccionada):** patrón clásico, ya reflejado de forma natural en la estructuración del modelo de componentes. Cada subsistema (acceso, CRM, video, etc.) se encuentra alineado con una capa del sistema (presentación, borde, aplicación, negocio, datos), facilitando su implementación y mantenimiento.
- **Arquitectura de microservicios:** opción considerada, pero descartada en esta etapa debido a la complejidad de orquestación, monitoreo, despliegue y comunicación que introduce. Si bien algunos componentes podrían beneficiarse de mayor independencia, actualmente el equipo prioriza una estructura más simple y coherente con el diseño existente.


## Pros y Contras de la decisión tomada

**Pros:**

- Estructura clara, conocida y ampliamente aceptada.
- Favorece el crecimiento progresivo del sistema.
- Baja complejidad inicial.
- Compatible con una futura transición a arquitectura orientada a servicios o hexagonal.

**Contras:**

- Riesgo de acoplamiento entre capas si no se mantiene una separación estricta.
- Posible dificultad para responder a cambios altamente dinámicos o necesidades de escalabilidad granular.

## Consecuencias

La organización del sistema se realizará según esta estructura en capas. Cada módulo se asignará a una capa según su responsabilidad. El diseño actual, reflejado en el diagrama de componentes, ya sirve como base inicial. Esta decisión podrá revisarse si aparecen cuellos de botella o necesidades de mayor desacoplamiento, particularmente si ciertos módulos evolucionan hacia microservicios o servicios independientes.

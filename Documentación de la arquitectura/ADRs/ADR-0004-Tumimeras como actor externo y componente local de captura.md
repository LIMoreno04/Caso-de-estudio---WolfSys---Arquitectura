# ADR-0004: Tumimeras como actor externo y componente local de captura

## ID

0004

## Título

Tumimeras como actor externo y componente local de captura

## Fecha

06/06/2025

## Estado

Rechazada

## Contexto

Tumimeras es una empresa externa que solicita los videos capturados en el edificio. Por privacidad y seguridad, se debe establecer un límite claro entre el sistema interno y Tumimeras. Es necesario definir cómo se integrará sin comprometer otros componentes del sistema.

## Decisión tomada

Se define a Tumimeras como actor externo, con un componente físico/software instalado en el edificio encargado de capturar los videos y enviarlos exclusivamente a Tumimeras.

## Factores decisivos

1. Separación de responsabilidades y control de acceso.
2. Privacidad de la información almacenada en el CRM.
3. Simplicidad en la integración con actores externos.

## Alternativas

- **Acceso directo de Tumimeras a todo el sistema:** se descartó por riesgos de seguridad y privacidad.
- **Compartir video desde un componente dedicado (opción elegida):** permite encapsular el acceso y limitarlo solo a lo necesario.

## Pros y Contras de la decisión tomada

**Pros:**

- Seguridad y aislamiento de los datos sensibles.
- Claridad en la arquitectura y responsabilidades.

**Contras:**

- Requiere mantener un componente adicional en el edificio.
- Puede agregar latencia o fallas si ese componente no funciona correctamente.

## Consecuencias

Tumimeras no tendrá acceso a la base de datos ni al CRM. Toda la interacción se limita al componente de captura, que será mantenido por Wolfsys. Esto también facilita cumplir con normativas de privacidad.

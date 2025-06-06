# ADR-0003: Unificación de la interfaz administrativa para múltiples roles

## ID

0003

## Título

Unificación de la interfaz administrativa para múltiples roles

## Fecha

06/06/2025

## Estado

Tomada

## Contexto

En el sistema participan distintos perfiles administrativos: 
- Administrativos de Wolfsys
- Administrativos del edificio
- Personal técnico de Wolfsys

Todos requieren acceso a funciones administrativas, aunque con distintos alcances. Se necesita una forma de gestionar estas interfaces sin duplicar desarrollo ni complejizar el mantenimiento.

## Decisión tomada

Se decide tener una única interfaz administrativa compartida, diferenciando el acceso y las funcionalidades disponibles mediante roles y permisos.

## Factores decisivos

1. Reutilización de componentes de frontend.
2. Reducción de la complejidad del mantenimiento.
3. Consistencia visual y funcional entre perfiles administrativos.
4. Facilidad para aplicar control de acceso basado en roles.

## Alternativas

- **Interfaces separadas por perfil:** requeriría mantener múltiples interfaces similares, aumentando el tiempo de desarrollo y la posibilidad de inconsistencias.
- **Interfaz común con control por roles (opción elegida):** permite condicionar vistas y acciones según el rol, sin multiplicar el código base.

## Pros y Contras de la decisión tomada

**Pros:**

- Facilita el mantenimiento del frontend.
- Mejora la experiencia de usuario por la coherencia de la interfaz.
- Permite escalar fácilmente a nuevos roles o permisos.

**Contras:**

- Mayor lógica condicional en la interfaz.
- Posibilidad de errores si no se gestionan correctamente los permisos.

## Consecuencias

Habrá que definir claramente los roles del sistema y asociar cada componente o acción a un conjunto de permisos. Esto requiere una implementación sólida del control de acceso y testing riguroso para evitar fugas de permisos entre perfiles.

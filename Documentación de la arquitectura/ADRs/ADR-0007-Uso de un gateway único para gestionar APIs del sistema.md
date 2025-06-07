# ADR-0007: Uso de un gateway único para gestionar APIs del sistema

## ID

0007

## Título

Uso de un gateway único para gestionar APIs del sistema

## Fecha

06/06/2025

## Estado

Tomada

## Contexto

El sistema cuenta con distintos servicios que manejan funcionalidades específicas: datos del CRM, video, y peticiones de acceso. En un inicio, se evaluó separar estos flujos por medio de gateways distintos para lograr mayor aislamiento. Sin embargo, este enfoque incrementa la complejidad operativa.

## Decisión tomada

Se decide utilizar un **único gateway centralizado**, llamado **B.O.B. – Border Operations Broker**, encargado de gestionar y enrutar todo el tráfico hacia las APIs correspondientes: API Datos, API Videos y API Peticiones.

## Factores decisivos

1. Simplificación de la arquitectura.
2. Evita duplicación de lógica de autenticación, autorización y monitoreo.
3. Permite aplicar políticas de seguridad centralizadas.
4. Las APIs ya están separadas por dominio funcional, por lo tanto, el gateway puede delegar la separación lógica internamente.

## Alternativas

- **Gateways separados por tipo de flujo:** permite un control más fino por canal, pero introduce complejidad y redundancia.
- **Gateway único (opción elegida):** simplifica el enrutamiento y gestión del sistema.

## Pros y Contras de la decisión tomada

**Pros:**

- Arquitectura más simple y mantenible.
- Un solo punto para aplicar reglas de seguridad y auditoría.
- Facilita el monitoreo unificado de accesos.

**Contras:**

- Aumenta la responsabilidad del gateway único.
- Si no se configura bien, puede convertirse en un cuello de botella o punto único de falla.

## Consecuencias

El sistema operará con un único componente de entrada: **B.O.B.**, quien autenticará, autorizará y derivará las solicitudes a la API correspondiente. Las APIs seguirán separadas internamente (datos, videos, peticiones), pero no se utilizarán gateways específicos para cada una. Esto reduce el acoplamiento externo y simplifica el mantenimiento.


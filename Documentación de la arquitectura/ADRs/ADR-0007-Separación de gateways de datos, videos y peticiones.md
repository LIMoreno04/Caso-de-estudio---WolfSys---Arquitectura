# ADR-0007: Separación de gateways de datos, videos y peticiones

## ID

0007

## Título

Separación de gateways de datos, videos y peticiones

## Fecha

06/06/2025

## Estado

Tomada

## Contexto

El sistema tiene múltiples entradas y salidas de información: datos del CRM, video capturado, y peticiones de acceso. Se necesita asegurar que cada actor acceda solo a lo que le corresponde, en especial Tumimeras, que solo debería recibir video.

## Decisión tomada

Se decide implementar **gateways separados** para cada tipo de acceso (datos, video, peticiones), permitiendo aislar el tráfico y aplicar políticas de seguridad diferenciadas.

## Factores decisivos

1. Seguridad: evitar que un actor externo acceda a datos internos.
2. Claridad: cada componente interactúa con un gateway dedicado.
3. Escalabilidad: permite crecer cada canal de forma independiente.

## Alternativas

- **Gateway único para todos los flujos:** más simple, pero con mayor riesgo de acceso no autorizado o errores de enrutamiento.
- **Gateways separados por dominio (opción elegida):** mejor control, a costo de mayor complejidad inicial.

## Pros y Contras de la decisión tomada

**Pros:**

- Aumenta la seguridad del sistema.
- Facilita el monitoreo y logging por canal.
- Posibilita limitar recursos según el tipo de tráfico.

**Contras:**

- Agrega complejidad en el despliegue y mantenimiento.
- Requiere definir contratos de comunicación para cada gateway.

## Consecuencias

Tumimeras interactuará exclusivamente con el gateway de video. El sistema interno y el CRM usarán otros gateways. Se implementarán reglas de firewall, autenticación y autorización diferenciadas para cada uno.

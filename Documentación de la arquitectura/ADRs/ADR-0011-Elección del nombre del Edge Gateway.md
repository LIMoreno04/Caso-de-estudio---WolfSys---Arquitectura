# ADR-0011: Elección del nombre del Edge Gateway

## ID

0011

## Título

Elección del nombre del Edge Gateway

## Fecha

07/06/2025

## Estado

Decidido

## Contexto

En el diseño del sistema, el Edge Gateway cumple un rol clave como punto de entrada, coordinación y ruteo entre los clientes (mobile/web), las APIs y los servicios internos. Al definir este componente, se buscó asignarle un nombre identificable, representativo y fácil de usar tanto en la documentación técnica como en la comunicación entre el equipo.
Se propusieron múltiples alternativas que combinan siglas funcionales con un toque creativo y memorable.

## Decisión tomada

Se eligió **B.O.B. – Border Operations Broker** como nombre oficial del Edge Gateway.

## Factores decisivos

1. Es corto, simple y fácil de recordar.
2. Encaja con el estilo amigable y accesible que el equipo desea mantener.
3. Resume claramente su función como broker de operaciones en el borde.
4. La sigla es pronunciable y permite una fácil adopción informal (“Bob”).

## Alternativas consideradas

- **W.A.R.D. – WolfSys API Routing Daemon**
- **C.E.R.B.E.R.O. – Concentrador Edge de Ruteo, Balanceo y Enrutamiento de Requerimientos de Operación**
- **S.A.M. – Sistema de Administración de Mensajes**
- **L.O.B.O. – Link Operations and Backend Orchestrator**
- **R.A.M.O.N. – Router Avanzado de Mensajes y Operaciones de Nodo**
- **P.A.C.O. – Proxy de Administración y Coordinación de Operaciones**
- **G.A.T.O. – Gateway de Administración y Tráfico Operacional**
- **T.I.T.O. – Túnel de Integración de Tráfico y Operaciones**
- **E.N.L.A.C.E. – Enrutador de Nivel Lógico para Administración Centralizada de Entradas**
- **N.E.X.O. – Nodo Edge para el eXchange de Operaciones**
- **R.E.D.I.R.E.C.T. – Router Edge de Direccionamiento Inteligente de Requerimientos, Eventos, Comunicaciones y Tráfico**

## Pros y Contras de la decisión tomada

**Pros:**

- Nombre corto y memorable.
- Fácil de usar en código, documentación y charlas informales.
- Refleja adecuadamente la función de intermediación del componente.

**Contras:**

- Menos descriptivo que otras opciones más largas.
- Puede no transmitir por sí solo el nivel de complejidad/responsabilidad del componente.

## Consecuencias

Todos los documentos, diagramas y referencias al Edge Gateway adoptarán el nombre **B.O.B.**. Esto facilitará la estandarización y la comunicación. En caso de necesidad futura, se podrá redefinir el acrónimo sin cambiar el identificador base “BOB”.

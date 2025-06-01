# ADR-0002: Selección de herramienta para diagramas de arquitectura

## ID
0002

## Título
Elección de draw.io para diseño de diagramas

## Fecha
20/05/2025

## Estado
Tomada

## Contexto
En el mismo contexto del proyecto de Arquitectura de Aplicaciones, además de registrar decisiones en texto, necesitamos una herramienta para crear y mantener diagramas arquitectónicos (componentes, despliegue, casos de uso, estado, etc.). Dicha herramienta debe ser:

- **Fácil de usar**, con curva de aprendizaje baja.  
- **Gratuita** o con plan sin coste para usos académicos.  
- **Exportable** a formatos comunes (PNG, SVG, PDF).  
- **Integrable** con repositorios de código o sistemas de documentación.  
- **Colaborativa**, permitiendo revisiones y edición compartida.  
- **Flexible**, soportando tanto diagramas simples como arquitecturas complejas.

## Decisión tomada
Se opta por **draw.io** como herramienta principal para diseñar y versionar los diagramas de arquitectura.

## Factores decisivos

1. **Curva de aprendizaje baja**  
   draw.io ofrece una interfaz visual muy intuitiva, arrastrar y soltar componentes, plantillas predefinidas de UML, C4, redes y más. La mayoría de los usuarios se familiarizan en minutos.

2. **Completamente gratuito y Open Source**  
   A diferencia de otras herramientas, draw.io no exige licencia ni suscripción para acceder a todas sus funcionalidades, lo cual es ideal para un entorno académico.

3. **Versionado en texto**  
   Los archivos se guardan en formato XML (o el propio formato .drawio), pero pueden exportarse como un JSON sencillo o incrustarse en Markdown, facilitando su seguimiento en Git.

4. **Integración con repositorios y GitLab**  
   Permite guardar diagramas directamente en repositorios (GitHub, GitLab, Bitbucket) o incrustarlos en wikis como Confluence, sin necesidad de software adicional.

5. **Colaboración en tiempo real**  
   Su edición en línea conectada a Google Drive, OneDrive o Git permite a varios usuarios trabajar simultáneamente.

6. **Flexibilidad y extensibilidad**  
   Se pueden utilizar bibliotecas de iconos, importar PlantUML o exportar a XML/PNG/SVG/PDF. También existen plugins para VS Code y otras IDEs que permiten su renderización y edición automática sin salirse del programa.

## Alternativas

- **Lucidchart**  
  - Descripción: Herramienta online profesional con interfaz muy pulida.  
  - Motivo de descarte:* Plan gratuito limitado en número de objetos; la versión educativa aún requiere registro institucional y puede caducar.

- **Paint (Windows)**  
  - Descripción: Editor de gráficos básico.  
  - Motivo de descarte: No está especializado en diagramas, carece de plantillas y funcionalidades de conectores automáticos.

- **Google Drawings**  
  - Descripción: Editor vectorial integrado en Google Drive.  
  - Motivo de descarte: Muy limitado en bibliotecas de íconos y formas de arquitectura; colaboración posible, pero diseño poco ágil.

- **Archi**  
  - Descripción: Herramienta Open Source centrada en el estándar ArchiMate.  
  - Motivo de descarte: Fuerte enfoque en ArchiMate, lo que añade complejidad y no cubre diagramas UML o C4 de forma nativa.

- **Mermaid**  
  - Descripción: Lenguaje de marcado de texto para diagramas dentro de Markdown.  
  - Motivo de descarte: Ideal para diagramas sencillos en documentación, pero poco visual y menos interactivo para diagramas complejos.

- **StarUML**  
  - Descripción: Herramienta de modelado UML de escritorio.  
  - Motivo de descarte: Versión completa de pago tras período de prueba; gratuito sólo limitado.

- **PlantUML**  
  - Descripción: Genera diagramas a partir de texto UML.  
  - Motivo de descarte: Requiere aprender sintaxis de PlantUML; menos inmediato para usuarios que prefieren arrastrar y soltar.

- **Cacoo**  
  - Descripción: Editor online colaborativo.  
  - Motivo de descarte: Plan gratuito muy restringido, marca de agua y límite de revisiones.

- **Canva**  
  - Descripción: Plataforma de diseño gráfico general.  
  - Motivo de descarte: No optimizada para diagramas técnicos; necesita buscar y ajustar elementos manualmente.

## Pros y Contras de draw.io

**Pros:**
- Interfaz gráfica intuitiva y rápido arranque.  
- 100% gratuito, sin limitaciones.  
- Integración con Git, Google Drive y OneDrive.  
- Exportación a múltiples formatos (PNG, SVG, PDF, XML).  
- Plantillas para UML, C4, redes, bases de datos y más.  
- Plugins disponibles para VS Code y otras IDEs.

**Contras:**
- Almacena diagramas en XML, lo que puede dificultar la lectura a ojo en casos muy complejos.  
- Aunque hay edición colaborativa, dependiente de la plataforma (Google/OneDrive/Git); no es tan fluido como un editor puramente en la nube.

## Consecuencias

1. Los diagramas de la arquitectura se mantendrán junto al código, en el mismo repositorio, usando archivos `.drawio` o exports SVG/PNG.  
2. El equipo aprenderá una única herramienta, evitando dispersión entre varios formatos y reduciendo curva de aprendizaje.  
3. Se podrán incluir diagramas directamente en la documentación Markdown y en wikis corporativas sin conversión manual.  
4. Al usar una solución gratuita y abierta, no habrá barreras de acceso ni dependencias comerciales.  
5. Se deberá acordar un estilo común (colores, formas, nomenclatura) para los diagramas, ya que draw.io no impone un estándar visual único.


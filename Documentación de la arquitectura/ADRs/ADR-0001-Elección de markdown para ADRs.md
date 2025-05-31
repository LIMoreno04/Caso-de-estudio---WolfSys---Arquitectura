# ADR-0001: Selección de herramienta y formato para ADRs

## ID
0001

## Título
Selección de Markdown con formato predefinido para los ADRs

## Fecha
20/05/2025

## Estado
Tomada

## Contexto
Como trabajo universitario para la materia Arquitectura de Aplicaciones se solicitó registrar las decisiones de diseño utilizando ADRs. Se requiere que la herramienta/formato elegida ofrezca:
- Facilidad de uso y curva de aprendizaje baja.
- Licencia gratuita.
- Compatibilidad con sistemas de control de versiones (por ejemplo, Git) y con entornos de desarrollo (por ejemplo, VS Code).
- Integración sencilla con otros softwares.
- Soporte para trabajo colaborativo.
- Versionado conjunto con el código fuente.

## Decisión tomada
Se opta por **Markdown** como formato para todos los ADRs del proyecto.

## Factores decisivos

La decisión de utilizar Markdown se tomó principalmente porque es un formato muy sencillo, accesible y versátil. A continuación se detallan los factores más importantes que influyeron en esta elección:

1. Facilidad de uso  
   Markdown es extremadamente fácil de aprender. No requiere conocimientos técnicos ni instalación de programas específicos. Cualquier persona puede comenzar a escribir en este formato usando un editor de texto simple, como el Bloc de notas o VS Code. Además, al ser texto plano, se evita cualquier distracción relacionada con el formato, centrándose solo en el contenido.

2. Es gratuito  
   No hay que pagar licencias, ni registrarse en ninguna plataforma. Markdown es un estándar abierto, lo cual es ideal para un trabajo académico donde se busca evitar costos innecesarios.

3. Versionado y control de cambios  
   Markdown se guarda como archivos de texto, por lo que se puede integrar fácilmente con herramientas de control de versiones como Git. Esto permite tener un historial claro de cada modificación, quién la hizo y por qué, de forma similar a cómo se gestiona el código de un proyecto, todo centralizado y en el mismo repositorio.

4. Compatible con editores y herramientas de desarrollo  
   Markdown funciona bien con los entornos de desarrollo que ya usamos, como Visual Studio Code o GitHub, los cuales ofrecen su renderización de forma nativa.

5. Integración con otras herramientas  
   Markdown puede combinarse con sistemas de documentación, sitios web o presentaciones de manera muy directa, sin necesidad de convertir a formatos complejos dado que al final del día sigue siendo texto plano.

En resumen, se trata de una solución simple, gratuita y eficaz, ideal para el entorno académico y especialmente adecuada para mantener la documentación del proyecto accesible junto al código.

---

## Alternativas

A continuación se presentan las herramientas y formatos que se evaluaron antes de tomar la decisión, junto con los motivos por los que fueron descartados:

- Notion  
  Aunque es una herramienta moderna y fácil de usar, funciona exclusivamente en línea y no permite guardar los archivos en nuestro propio repositorio con facilidad. No se integra bien con Git, lo cual limita su uso para proyectos versionados. Además, al ser una plataforma cerrada, dependemos de su servicio y estructura.

- Structurizr  
  Esta herramienta está más orientada a crear diagramas y modelos de arquitectura visual. Aunque es útil para ciertas representaciones, no está pensada para redactar documentos como los ADRs. Su enfoque es demasiado específico para nuestro propósito.

- ADR Tools  
  Son conjuntos de herramientas diseñadas especialmente para escribir ADRs, pero suelen requerir configuraciones e instalaciones adicionales. Esto puede ser una barrera innecesaria para el grupo, dado que Markdown por sí solo ya cumple bien con nuestros requisitos sin complicaciones.

- Obsidian  
  Obsidian es una aplicación de escritorio que usa Markdown y permite organizar notas. Sin embargo, su uso colaborativo no es tan simple, ya que no tiene sincronización en tiempo real y depende mucho del trabajo individual. Para un grupo que necesita colaborar frecuentemente, esto es una limitación.

- Google Docs  
  Muy útil para escritura colaborativa, pero tiene dos problemas principales: los documentos no se almacenan como archivos de texto plano (requieren estructuras innecesariamente más complejas, como pdf o docx) y no se pueden versionar con Git fácilmente. Además, dependen completamente de una conexión a internet y del ecosistema de Google.

- Microsoft Word  
  Mantiene las limitaciones de Google Docs; con el agregado de exigir una licencia completa de Office para su mayor provecho (como el uso de la app de escritorio), lo que puede generar desigualdad en el trabajo.

- Confluence  
  Es una herramienta poderosa para empresas, pero requiere una configuración avanzada, cuenta con un costo en muchas de sus versiones, y no es sencilla de usar si nunca se ha trabajado con ella. Demasiado compleja para nuestras necesidades actuales.

- Sparx Systems Enterprise Architect  
  Esta herramienta está pensada para modelar arquitecturas de sistemas grandes con diagramas UML. No es adecuada para simplemente escribir decisiones arquitectónicas de forma narrativa o textual. Además, es un software de pago y bastante pesado.

- MediaWiki  
  Es el mismo sistema que usa Wikipedia, pero instalarlo y mantenerlo requiere experiencia en servidores web. No es práctico para un grupo de estudiantes que necesita una solución rápida y sencilla, además de que tampoco se integra bien con los editores de código ni con Git de forma natural.

---


## Pros y Contras de Markdown

**Pros:**
- Extrema sencillez y curva de aprendizaje mínima.
- Formato de texto plano, versionable con Git.
- Soporta edición en cualquier IDE o editor ligero.
- Integración nativa con pipelines de documentación estática (por ejemplo, MkDocs, GitBook).
- Gratuito y open source.
- Permite colaboración mediante repositorios y pull requests.

**Contras:**
- Ausencia de interfaz gráfica dedicada para edición colaborativa en tiempo real.
- No ofrece plantillas ni metadatos estructurados avanzados sin extensiones adicionales, por lo que se debe definir y acordar del uso de un formato común (el de este ADR).


## Consecuencias

Al haber elegido Markdown como formato para registrar los ADRs, se establecen una serie de efectos prácticos que impactan positivamente en la forma en que se va a trabajar durante el proyecto:

1. Los ADRs estarán guardados como archivos de texto plano junto al resto del código del proyecto. Esto facilita mantener toda la documentación centralizada, versionada y accesible desde un solo lugar.

2. El trabajo en equipo se organiza mediante repositorios (por ejemplo en GitHub o GitLab), donde cada integrante puede editar, revisar y comentar los ADRs usando herramientas que ya conocen, como los pull requests.

3. Se reduce el tiempo de aprendizaje. Todos los integrantes del equipo pueden comenzar a documentar rápidamente sin necesidad de aprender a usar una nueva herramienta, plataforma o lenguaje, es solo texto plano con el formato predefinido.

4. Se establece desde el principio una estructura base común para todos los ADRs, lo cual promueve la coherencia y facilita su lectura y mantenimiento a lo largo del desarrollo del proyecto. Esta es la presente en el actual ADR-0001.

5. Como limitación, se deberá ser riguroso en respetar este formato común, ya que Markdown no impone ninguna estructura por sí mismo. Esto exige acordar buenas prácticas entre los integrantes del equipo (como el uso de encabezados, secciones y numeración de los ADRs).

## Comentarios
Se tendrá un apartado en el repositorio específicamente dedicado a los ADRs, de los cuales además se resuelve un formato predefinido de Markdown a utilizar:
- **# ADR-`ID de 4 dígitos`:** `Título del ADR`  
    (Por ejemplo: `# ADR-0001: Selección de herramienta para los ADRs`)

    - **## ID**  
        - `ID de 4 dígitos` (Ejemplo: `0001`)
    
    - **## Título**  
        - `Título breve y claro de la decisión arquitectónica`
    
    - **## Fecha**  
        - `Fecha de la última modificación del ADR, en formato DD/MM/AAAA`  
          (Este campo debe actualizarse cada vez que se modifique el archivo)

    - **## Estado**  
        - Una palabra que indique el estado de la decisión. Algunos valores posibles:  
            - `Por decidirse`
            - `Tomada`
            - `Implementada`
            - `Rechazada`
            - `Obsoleta`

    - **## Contexto**  
        - `Descripción clara y completa del problema o situación que llevó a tener que tomar una decisión.`
        - `Puede incluir antecedentes, objetivos, restricciones, y aspectos que se deben considerar en forma de lista no ordenada, con subtítulos en negrita.`

    - **## Decisión tomada**  
        - `Descripción concreta y directa de la decisión que se tomó (o se planea tomar).`

    - **## Factores decisivos**  
        - `Lista ordenada de las razones principales que justifican la decisión.`  
          Ejemplo:
          1. `Motivo principal 1`
          2. `Motivo principal 2`
          3. `...`

    - **## Alternativas**  
        - `Lista no ordenada de las opciones que se consideraron antes de tomar la decisión, con una breve explicación de cada una y del por qué fueron descartadas.`  
          Ejemplo:
          - `Alternativa 1: Descripción y motivo de descarte.`
          - `Alternativa 2: Descripción y motivo de descarte.`
          - `...`

    - **## Pros y Contras de `decisión tomada`**  
        - `Lista separada de ventajas y desventajas asociadas a la decisión tomada.`  
          Ejemplo:

          **Pros:**
          - `Ventaja 1`
          - `Ventaja 2`

          **Contras:**
          - `Desventaja 1`
          - `Desventaja 2`

    - **## Consecuencias**  
        - `Efectos que tendrá esta decisión sobre el proyecto.`
        - `Pueden ser cambios en la forma de trabajo, nuevas herramientas que deberán usarse, posibles limitaciones, mejoras en la organización, etc.`
    
    - **## Comentarios (opcional)**  
        - `Cualquier tipo de información adicional que pueda ser útil.`
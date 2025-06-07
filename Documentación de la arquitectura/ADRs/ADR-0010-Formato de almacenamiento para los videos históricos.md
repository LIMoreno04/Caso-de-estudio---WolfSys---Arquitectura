# ADR-0010: [Descripción breve de la decisión]

## ID
XXXX

## Título
[Título breve y claro de la decisión arquitectónica tomada]

## Fecha
07/06/2025

## Estado
Tomada 

## Contexto
El sistema requiere conservar los videos históricos (más allá de los 3 meses desde su creación) 
en una solución de almacenamiento en frío. Para facilitar su persistencia, portabilidad y compatibilidad con 
sistemas de almacenamiento más simples o no especializados en medios, es necesario definir un formato estándar 
y flexible para representar estos archivos.

## Decisión tomada
Se decidió que los videos migrados al almacenamiento en frío serán representados como strings de bits puros. 
Es decir, la totalidad del contenido binario del archivo de video se serializa como una secuencia continua de 
0s y 1s, almacenada como texto plano según el sistema de almacenamiento.

Este formato es generado por el componente La Heladera al extraer los videos desde Tumimeras y convertirlos 
antes de almacenarlos.

## Factores decisivos
1. Neutralidad tecnológica: los strings de bits pueden almacenarse en múltiples bases de datos, sistemas de archivos o 
servicios de almacenamiento en la nube sin requerir compatibilidad con formatos multimedia.
2. Simplicidad de lectura y escritura: se facilita la implementación de herramientas para inspeccionar, transferir o 
respaldar archivos.
3. Portabilidad: el formato no depende de ninguna librería de terceros ni metadatos específicos del sistema original.
4. Evita corrupción de datos: no hay riesgo de interpretación errónea por parte de las base de datos que procesan binarios de manera especial.

## Alternativas
- [Alternativa 1: descripción y motivo de descarte]
- [Alternativa 2: descripción y motivo de descarte]

## Pros y Contras de [nombre de la decisión]

**Pros:**
- [Ventaja 1]
- [Ventaja 2]

**Contras:**
- [Desventaja 1]
- [Desventaja 2]

## Consecuencias
[Descripción de los efectos prácticos que tendrá esta decisión.]

## Comentarios (opcional)
[Cualquier detalle adicional o nota.]

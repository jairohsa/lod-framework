# Publicación de Datos Abiertos Enlazados del POA - Ejemplo 2025

Este repositorio contiene un ejemplo práctico de cómo transformar y publicar como datos abiertos enlazados (Linked Open Data) la información del Plan Operativo Anual (POA) de un Gobierno Autónomo Descentralizado (GAD) en Ecuador. Los datos son una muestra que ha sido modificada con fines investigativos por lo tanto no se debe considerar como datos oficiales de una institución.

---

## Contenidos del repositorio

- **poa-data-example-2025.csv**  
  Archivo en formato tabular (CSV) con las actividades planificadas para el año 2025. Incluye columnas como tipo de actividad, ubicación geográfica (provincia, cantón, parroquia), presupuesto por trimestre, etc. Se puede importar a OpenRefine agregando previamente una columna "locationCode" con un codígo unico para la ubicación.

- **poa-linked-data-2025.ttl**  
  Versión transformada del CSV al formato RDF en sintaxis Turtle, lista para ser publicada como Linked Open Data.

- **poa-ontology-v5.ttl**  
  Ontología desarrollada para estructurar y describir el contenido del POA. Incluye clases, propiedades y etiquetas reutilizando vocabularios como SKOS, FOAF y DCTERMS. Se puede abrir en Protégé.

---

## Pasos para visualizar los datos

### 1. Subir los archivos a Apache Jena Fuseki

1. Abrir Fuseki y crear un nuevo **dataset llamado `poa`**.
2. Dentro de ese dataset:
   - Cargar la ontología (`poa-ontology-v5.ttl`) en el **named graph**:  
     `http://planificacion.org/poa/ontology`
   - Cargar los datos (`poa-linked-data-2025.ttl`) en el **named graph**:  
     `http://planificacion.org/poa/data/2025`

---

### 2. Consultar los datos con YASGUI

Con YASGUI puedes conectarte al endpoint SPARQL de Fuseki, escribir consultas y visualizar los resultados como tablas, gráficos o enlaces navegables. Ejemplo:

SELECT  (STRAFTER(STR(?actividad), "BudgetedActivity/") AS ?codigoActividad) ?label (xsd:decimal(?q1) AS ?Q1) (xsd:decimal(?q2) AS ?Q2) (xsd:decimal(?q3) AS ?Q3) (xsd:decimal(?q4) AS ?Q4) ?l
 WHERE {
  GRAPH <http://planificacion.org/poa/data/2025> {
    ?actividad a poa:BudgetedActivity ;
               rdfs:label ?label ;
               poa:plannedAmountQuarter1 ?q1 ;
               poa:plannedAmountQuarter2 ?q2 ;
               poa:plannedAmountQuarter3 ?q3 ;
               poa:plannedAmountQuarter4 ?q4 .
  }
}
ORDER BY RAND()
LIMIT 5

---

PREFIX poa: <http://planificacion.org/poa#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT (STRAFTER(STR(?actividad), "BudgetedActivity/") AS ?codigoActividad)
       ?label ?canton ?parroquia
WHERE {
  GRAPH <http://planificacion.org/poa/data/2025> {
    ?actividad a poa:BudgetedActivity ;
               rdfs:label ?label ;
               poa:hasLocation ?ubicacion .
    OPTIONAL { ?ubicacion poa:locatedInProvince ?provin-cia . }
    OPTIONAL { ?ubicacion poa:locatedInCanton ?canton . }
    OPTIONAL { ?ubicacion poa:locatedInParish ?parroquia . }
  }
}
LIMIT 5


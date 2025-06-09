---
layout: default
title: Queries
---

# Queries

This section presents a selection of the SPARQL queries used in the project to retrieve data from the MiC dataset. A full explanation of the procedure and analysis is available in the [Methodology](./methodology) section.

### 1. Searching for castles in the dataset

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

PREFIX cis: <http://dati.beniculturali.it/cis/>

PREFIX arco: <https://w3id.org/arco/ontology/arco/>


SELECT DISTINCT ?castello ?label WHERE {

  ?castello a cis:CulturalInstituteOrSite ;
  
            rdfs:label ?label .
            
  FILTER(REGEX(LCASE(STR(?label)), "castello"))
  
}

ORDER BY ?label

LIMIT 300


### 2. Retrieving cultural assets containing a specific keyword

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

PREFIX arco: <https://w3id.org/arco/ontology/arco/>


SELECT ?bene ?nome

WHERE {

  ?bene rdfs:label ?nome .
  
  FILTER(CONTAINS(LCASE(STR(?nome)), "fenis"))
  
}

LIMIT 50


### 3. Inspecting the properties of a specific resource

SELECT ?property ?value

WHERE {

  <http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827> ?property ?value
  
}


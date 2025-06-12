---
layout: default
title: Queries
---

# Queries

This section presents the SPARQL queries used in the project to retrieve data from the [ArCo ontology](https://dati.beniculturali.it/lode/extract?lang=it&url=https://raw.githubusercontent.com/ICCD-MiBACT/ArCo/master/ArCo-release/ontologie/arco/arco.owl). A full explanation of the procedure and analysis is available in the [Methodology](./methodology) section.

### 1. Searching for castles in the dataset

```sparql
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
```

### 2. Retrieving cultural assets containing a specific keyword (Fénis)

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

PREFIX arco: <https://w3id.org/arco/ontology/arco/>


SELECT DISTINCT ?bene ?nome

WHERE {

  ?bene rdfs:label ?nome .
  
  FILTER(CONTAINS(LCASE(STR(?nome)), "fenis"))
  
}

LIMIT 50
```

### 3. Inspecting the properties of the Fénis Castle

```sparql
SELECT DISTINCT ?property ?value

WHERE {

  <http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827> ?property ?value
  
}
```

### 4. Check for creation date of the Fénis Castle

```sparql
PREFIX arco: <https://w3id.org/arco/ontology/arco/>
PREFIX core: <https://w3id.org/arco/ontology/core/>
PREFIX cis: <http://dati.beniculturali.it/cis/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX ti: <https://w3id.org/arco/ontology/time/>
PREFIX l0: <https://w3id.org/italia/onto/l0/>

SELECT DISTINCT ?culturalProperty ?label ?startDate
WHERE {
  ?culturalProperty a arco:CulturalProperty ;
                    rdfs:label ?label ;
                    l0:hasTime ?time . 
  FILTER(CONTAINS(LCASE(STR(?label)), "fenis"))
  ?time a ti:TimeInterval ;
        ti:hasBeginning ?startDateEntity .
  ?startDateEntity ti:inXSDDate ?startDate .
}
LIMIT 10
```

### 5. Check for the commissioner of the Fénis Castle

```sparql
PREFIX arco: <https://w3id.org/arco/ontology/arco/>
PREFIX l0: <https://w3id.org/italia/onto/l0/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?culturalProperty ?label ?agentLabel ?roleLabel
WHERE {
  ?culturalProperty a arco:CulturalProperty ;
                    rdfs:label ?label .
  FILTER(REGEX(LCASE(STR(?label)), "fenis"))
  {
    ?culturalProperty arco:hasAgentRole ?agentRole .
  }
  UNION
  {
    ?culturalProperty arco:hasCommissioning ?agentRole .
  }
  ?agentRole arco:hasAgent ?agent .
  OPTIONAL { ?agent rdfs:label ?agentLabel . }
  OPTIONAL { 
    ?agentRole arco:role ?role .
    OPTIONAL { ?role rdfs:label ?roleLabel . }
  }
  FILTER(REGEX(LCASE(STR(?roleLabel)), "builder|commissioner"))
}
ORDER BY ?agentLabel
LIMIT 10
```

### 6. Check for the conservation status of the Fénis Castle
```sparql
PREFIX arco: <https://w3id.org/arco/ontology/arco/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?status ?statusLabel WHERE {
  <http://dati.beniculturali.it/cis/resource/CulturalInstituteOrSite/100827> arco:hasConservationStatus ?status .
  
  OPTIONAL {
    ?status rdfs:label ?statusLabel .
  }
}
```

### 7. Check for the image of the Fénis Castle
```sparql
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX arco: <https://w3id.org/arco/ontology/arco/>
PREFIX dc: <http://purl.org/dc/elements/1.1/>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX l0: <https://w3id.org/italia/onto/l0/>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?image ?representation ?title
WHERE {
  VALUES ?castello {
    <http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827>
  }

  OPTIONAL {
    ?castello foaf:depiction ?image .
  }
  OPTIONAL {
    ?castello arco:hasRepresentation ?representation .
    OPTIONAL { ?representation foaf:depiction ?image . }
    OPTIONAL { ?representation dc:title ?title . }
  }
  OPTIONAL {
    ?castello arco:hasDigitalRepresentation ?representation .
    OPTIONAL { ?representation foaf:depiction ?image . }
    OPTIONAL { ?representation dc:title ?title . }
  }
}
```

### 8. Check for cultural events at the Fénis Castle
```sparql
PREFIX arco: <https://w3id.org/arco/ontology/arco/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?event ?label WHERE {
  <http://dati.beniculturali.it/cis/resource/CulturalInstituteOrSite/100827> arco:hasCulturalEvent ?event .

  ?event rdfs:label ?label .

  FILTER (REGEX(?label, "festival|day|fiera", "i"))
}
ORDER BY ?label
LIMIT 20
```

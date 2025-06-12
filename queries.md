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


SELECT ?bene ?nome

WHERE {

  ?bene rdfs:label ?nome .
  
  FILTER(CONTAINS(LCASE(STR(?nome)), "fenis"))
  
}

LIMIT 50
```

### 3. Inspecting the properties of the Fénis Castle

```sparql
SELECT ?property ?value

WHERE {

  <http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827> ?property ?value
  
}
```

### 4. Check for creation date of the Fénis Castle

```sparql
PREFIX arco: &lt;https://w3id.org/arco/ontology/arco/&gt;
PREFIX core: &lt;https://w3id.org/arco/ontology/core/&gt;
PREFIX cis: &lt;http://dati.beniculturali.it/cis/&gt;
PREFIX rdfs: &lt;http://www.w3.org/2000/01/rdf-schema#&gt;
PREFIX ti: &lt;https://w3id.org/arco/ontology/time/&gt;
PREFIX l0: &lt;https://w3id.org/italia/onto/l0/&gt;

SELECT DISTINCT ?culturalProperty ?label ?startDate
WHERE {
?culturalProperty a arco:CulturalProperty ;
rdfs:label ?label ;
l0:hasTime ?time .
FILTER(CONTAINS(LCASE(STR(?label)), &quot;fenis&quot;))
?time a ti:TimeInterval ;
ti:hasBeginning ?startDateEntity .
?startDateEntity ti:inXSDDate ?startDate .
}
LIMIT 10
```

### 5. Check for the commissioner of the Fénis Castle

```sparql
PREFIX arco: &lt;https://w3id.org/arco/ontology/arco/&gt;
PREFIX l0: &lt;https://w3id.org/italia/onto/l0/&gt;
PREFIX rdfs: &lt;http://www.w3.org/2000/01/rdf-schema#&gt;

SELECT DISTINCT ?culturalProperty ?label ?agentLabel ?roleLabel
WHERE {
?culturalProperty a arco:CulturalProperty ;
rdfs:label ?label .
FILTER(REGEX(LCASE(STR(?label)), &quot;fenis&quot;))
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
FILTER(REGEX(LCASE(STR(?roleLabel)), &quot;builder|commissioner&quot;))
}
ORDER BY ?agentLabel
LIMIT 10
```


### 

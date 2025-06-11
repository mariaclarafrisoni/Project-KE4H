---
layout: default
title: Methodology
---

# Methodology

In this section, we describe the steps taken to build our project. The main goal was to extract meaningful cultural heritage data using semantic web technologies and to enrich it through the use of [large language models (LLMs)](https://en.wikipedia.org/wiki/Large_language_model).

We decided to focus on **castles** because they represent a fascinating and well-documented part of Italian cultural heritage, and offered an interesting variety of data to explore and visualize.

## Step 1: Identifying Relevant Cultural Heritage Sites
To begin, we compiled a reference list by searching the internet for the [“10 most beautiful castles in Italy”](https://www.travel365.it/classifica-castelli-piu-belli-italia.htm). This list provided a baseline for comparison with existing data in the [ArCo knowledge graph](https://dati.beniculturali.it/lode/extract?lang=it&url=https://raw.githubusercontent.com/ICCD-MiBACT/ArCo/master/ArCo-release/ontologie/arco/arco.owl).
Next, we executed a **SPARQL query** to retrieve all entries classified as castles in Italy from the ArCo dataset. The query filtered cultural institutes or sites whose label contained the term <u>“castello”</u> (castle):

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

We then compared this list with the manually curated list of well-known castles to identify overlaps and gaps.
![image](https://github.com/user-attachments/assets/b3c11938-1c69-46e5-8d40-a59f1eace610)

## Step 2: Selecting a Case Study – Castello di Fénis
Among the castles that appeared in both the online and SPARQL-generated lists, we selected the [**Castello di Fénis**](https://en.wikipedia.org/wiki/F%C3%A9nis_Castle), located in the [Aosta Valley](https://en.wikipedia.org/wiki/Aosta_Valley). This castle was chosen because it appeared to have <u>incomplete or missing data</u> within the ArCo dataset, making it a suitable case study for enrichment. 

To investigate the existing data about the **Castello di Fénis**, we performed a more targeted query to retrieve all records that included “Fénis” in their label: 

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

![image](https://github.com/user-attachments/assets/ac3c021e-a0c0-42ac-8f5a-8026273f1433)

This query allowed us to identify all the entities associated with “Fénis” in the dataset, ultimately leading us to the official **IRI** of Castello di Fénis:
http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827
Once the official IRI was identified, we constructed the following **SPARQL query** to extract all properties and values directly linked to this specific resource:

```sparql
SELECT ?property ?value
WHERE {
  <http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827> ?property ?value
}
```

![image](https://github.com/user-attachments/assets/241ab1a9-d2f1-434d-946e-11ca8a4cfde0)

This query enabled us to explore the complete set of information currently available about the castle in the ArCo knowledge graph, which helped us assess what data was already present and what needed to be added or updated during the enrichment phase.






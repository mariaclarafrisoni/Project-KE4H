---
layout: default
title: Methodology
---

# Methodology

In this section, we describe the steps taken to build our project. The main goal was to extract meaningful cultural heritage data using semantic web technologies and to enrich it through the use of [Large Language Models (LLMs)](https://en.wikipedia.org/wiki/Large_language_model).

We decided to focus on **castles** because they represent a fascinating and well-documented part of Italian cultural heritage, and offered an interesting variety of data to explore and visualize.

## Step 1: Identifying Relevant Cultural Heritage Sites
To begin, we compiled a reference list by searching the internet for the [“10 most beautiful castles in Italy”](https://www.travel365.it/classifica-castelli-piu-belli-italia.htm). This list provided a baseline for comparison with existing data in the [ArCo knowledge graph](https://dati.beniculturali.it/lode/extract?lang=it&url=https://raw.githubusercontent.com/ICCD-MiBACT/ArCo/master/ArCo-release/ontologie/arco/arco.owl).
Next, we executed a **SPARQL query** to retrieve all entries classified as castles in Italy from the ArCo dataset. The query filtered cultural institutes or sites whose label contained the term "<u>castello</u>" (castle):

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

We then compared the list displayed in the image below with the manually curated list of well-known castles to identify overlaps and gaps.


![img1](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/Img%201.png?raw=true)

## Step 2: Selecting a Case Study – Castello di Fénis
Among the castles that appeared in both the online and SPARQL-generated lists, we selected the [**Castello di Fénis**](https://en.wikipedia.org/wiki/F%C3%A9nis_Castle), located in the [Aosta Valley](https://en.wikipedia.org/wiki/Aosta_Valley). This castle was chosen because it appeared to have <u>incomplete or missing data</u> within the ArCo dataset, making it a suitable case study for enrichment. 

To investigate the existing data about the **Castello di Fénis**, we performed a more targeted query to retrieve all records that included “Fénis” in their label: 

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

![img2](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%202.png?raw=true)

This query allowed us to identify all the entities associated with “Fénis” in the dataset, ultimately leading us to the official **IRI** of Castello di Fénis:

http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827

Once the official IRI was identified, we constructed the following **SPARQL query** to extract all properties and values directly linked to this specific resource, helping us understand what data was already present and what needed to be added:

```sparql
SELECT DISTINCT ?property ?value
WHERE {
  <http://dati.beniculturali.it/mibact/luoghi/resource/CulturalInstituteOrSite/100827> ?property ?value
}
```

![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%203.png?raw=true)

This query enabled us to explore the complete set of information currently available about the castle in the [ArCo knowledge graph](https://dati.beniculturali.it/lode/extract?lang=it&url=https://raw.githubusercontent.com/ICCD-MiBACT/ArCo/master/ArCo-release/ontologie/arco/arco.owl), which helped us assess what data was already present and what needed to be added or updated during the enrichment phase.

## Step 3: Identifying Missing Information

After analyzing the available data, we found that key information was missing from the ArCo ontology. In particular:

- <span style="background-color:#2E7D32; padding:2px 4px; border-radius:4px;">The date of construction of the castle</span>

- <span style="background-color:#2E7D32; padding:2px 4px; border-radius:4px;">The commissioner</span>

- <span style="background-color:#2E7D32; padding:2px 4px; border-radius:4px;">The state of conservation and restoration history</span>

- <span style="background-color:#2E7D32; padding:2px 4px; border-radius:4px;">Any official representative image</span>

- <span style="background-color:#2E7D32; padding:2px 4px; border-radius:4px;">Upcoming or current cultural events</span>

To ensure this information was indeed missing, we executed specific **SPARQL queries** for the date of creation:

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
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2016.png?raw=true)

For the commissioner:
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
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2017.png?raw=true)

For the conservation status:
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
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2025.png?raw=true)

For the image:
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
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2026.png?raw=true)

And for the cultural events:
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
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2027.png?raw=true)

## Step 4: Enrichment Using Large Language Models (LLMs)

To enrich the dataset, we prompted two LLMs - [Chat GPT](https://chatgpt.com/?model=auto) and [Gemini](https://gemini.google.com/app?hl=it) - using all three prompting techniques (zero-shot, few-shot and chain of thought). For more details on LLMs and prompting techniques, see the [LLMs](LLMs.md) page.

### Prompt: Date of Construction (Zero-shot)

What year was the castle of Fénis in Italy built?

#### Chat GPT

![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2018.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2019.png?raw=true)

#### Gemini

![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2021.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2022.png?raw=true)

Both the LLMs are effective. However, according to [Wikipedia](https://en.wikipedia.org/wiki/F%C3%A9nis_Castle), the first construction works started in 1320, while Gemini says 1340. Therefore, 1320 can be assumed as the date of construction of the castle. That is why we chose to go on with **Chat GPT** for the creation of the triple since it better conveys this kind of precise information as a date of construction is:

```ttl
@prefix ex: <https://example.org/resource/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

ex:castle-of-fenis 
    a <https://w3id.org/arco/ontology/arco/ArchitecturalHeritage> ;
    rdfs:label "Castello di Fénis"@it ;
    ex:builtIn "1320-01-01"^^xsd:date .
```

### Prompt: Commissioner (Few-shot)

Who built the Castello di Fénis in Aosta Valley?

#### Chat GPT

![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2023.png?raw=true)

#### Gemini

![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2024.png?raw=true)

Both answers are effective and true. But **Chat GPT** was more precise in giving us the exact name of the commissioner of the castle, that is <u>Aymon of Challant</u>, its patron and commissioner. That’s the answer we immediately found when googling ["who commissioned the construction of the Fenis castle?"](https://www.google.com/search?q=who+commissioned+the+construction+of+the+Fenis+castle%3F&oq=who+commissioned+the+construction+of+the+Fenis+castle%3F&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIHCAEQIRigATIHCAIQIRigATIHCAMQIRigATIHCAQQIRigATIHCAUQIRigAdIBBzk0MWowajSoAgCwAgA&sourceid=chrome&ie=UTF-8)

Thefeore, we asked Chat GPT to create the RDF triple for the entity: 
```ttl
@prefix ex: <https://example.org/resource/> .
@prefix arco: <https://w3id.org/arco/ontology/arco/> .
@prefix l0: <https://w3id.org/italia/onto/l0/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

ex:castle-of-fenis a arco:ArchitecturalHeritage ;
arco:hasCommissioning [
a arco:Commissioning ;
arco:hasAgent ex:aymon-of-challant
] .

ex:aymon-of-challant a l0:Agent ;
rdfs:label "Aymon of Challant"@en .
```

### Prompt: State of conservation and restoration phase (Chain of thought)
   
What is the current state of conservation of Fénis Castle in Valle d'Aosta, and what restoration work has been carried out over time? Let's think about this step by step, starting with its historical condition and moving toward modern restoration efforts.

#### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2011.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2012.png?raw=true)

#### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%209.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2010.png?raw=true)

Both answers were accurate and provided valid information. Therefore, we decided to integrate them, supplementing any missing details. Once we had developed a comprehensive scheme encompassing the main points, we proceeded to create the triples:
```ttl
@prefix arco: <https://w3id.org/arco/ontology/arco/> .
@prefix cis: <http://dati.beniculturali.it/cis/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

<http://dati.beniculturali.it/luoghi/resource/CulturalInstituteOrSite/100827>
    a cis:CulturalInstituteOrSite ;
    rdfs:label "Fénis Castle"@en ;
    arco:hasConservationStatus <http://example.org/resource/ConservationStatus/Excellent> ;
    arco:hasIntervention <http://example.org/resource/Intervention/Fenis1895> ,
                         <http://example.org/resource/Intervention/Fenis1935> ,
                         <http://example.org/resource/Intervention/Fenis1990> .

<http://example.org/resource/ConservationStatus/Excellent>
    a arco:ConservationStatus ;
    rdfs:label "Excellent"@en .

<http://example.org/resource/Intervention/Fenis1895>
    a arco:Intervention ;
    rdfs:label "Restoration by Alfredo d’Andrade (1895–1920)"@en .

<http://example.org/resource/Intervention/Fenis1935>
    a arco:Intervention ;
    rdfs:label "Restoration by De Vecchi and Mesturino (1935–1942)"@en .

<http://example.org/resource/Intervention/Fenis1990>
    a arco:Intervention ;
    rdfs:label "Preventive and digital conservation (1990s–present)"@en
```

### Prompt: Image Retrieval (Few-shot)
   
Please provide the URL (file .jpg, .png, etc.) for the official image of Fénis Castle in Valle d'Aosta. If there is no official photo, please indicate if there are any relevant historical images, recent photographs, or artistic representations. Please also provide the exact source of the image.

Example:

An officially recognized image of the Colosseum is available on Wikimedia Commons.

•	URL: https://commons.wikimedia.org/wiki/File:Colosseum_-Rome-Italy(16800139540).jpg

•	Title: Colosseum - Rome - Italy (16800139540).jpg

•	Author: Sam Valadi

•	Source: https://commons.wikimedia.org/wiki/File:Colosseum_-Rome-Italy(16800139540).jpg

•	License: Creative Commons Attribution 2.0 Generic (CC BY 2.0)

https://creativecommons.org/licenses/by/2.0/

#### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%204.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%205.png?raw=true)

#### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%206.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%207.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%208.png?raw=true)

Both responses included URLs formatted as requested, accompanied by the necessary information. However, Gemini's answer was less precise, as the cited author did not match the one listed on the source page. Consequently, the **ChatGPT response** was selected for generating the <u>triples</u>.

Now that we have:

•	Subject: cis:CulturalInstituteOrSite_100827 – the Castello di Fénis in the Italian cultural heritage linked data system.

•	Predicate: arco:hasRepresentative – the ArCo property used to indicate an official or representative image.

•	Object: https://commons.wikimedia.org/wiki/File:CastelloDiF%C3%A9nisJuly292023_06.jpg -  The URL of the image hosted on Wikimedia Commons, used as an IRI

We can proceed with the triple:
```ttl
@prefix arco: <https://w3id.org/arco/ontology/arco/> .
@prefix cis: <https://dati.beniculturali.it/cis/> .

cis:CulturalInstituteOrSite_100827
    arco:hasRepresentative <https://upload.wikimedia.org/wikipedia/commons/1/1f/CastelloDiF%C3%A9nisJuly292023_06.jpg>
```

Or if we want to add here also the author of the image and the title:
```ttl
@prefix arco: <https://w3id.org/arco/ontology/arco/> .
@prefix a-cd: <https://w3id.org/arco/ontology/context-description/> .
@prefix cis: <https://dati.beniculturali.it/cis/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ex: <https://example.org/agent/> .

cis:CulturalInstituteOrSite_100827
    arco:hasRepresentative <https://upload.wikimedia.org/wikipedia/commons/1/1f/CastelloDiF%C3%A9nisJuly292023_06.jpg> .

<https://upload.wikimedia.org/wikipedia/commons/1/1f/CastelloDiF%C3%A9nisJuly292023_06.jpg>
    rdfs:label "Castello di Fénis - July 29, 2023"@it ;
    a-cd:hasAuthor ex:Hagai_Agmon_Snir .

ex:Hagai_Agmon_Snir
    a foaf:Agent ;
    foaf:name "Hagai Agmon-Snir"@en .
```

### Prompt: Cultural Events (Zero-shot)
   
Please provide a list of any upcoming or current events taking place at Fénis Castle in Valle d'Aosta, Italy. Include event names, dates, brief descriptions and official websites if available.

#### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2014.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2015.png?raw=true)

#### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2013.png?raw=true)

After verifying the reliability of both answers, we integrated them along with the missing information and created a triple that includes the events, dates, a brief description, and the official website:
```ttl
@prefix arco: <https://w3id.org/arco/ontology/arco/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix ex: <http://example.org/resource/> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix cis: <http://dati.beniculturali.it/cis/> .

<http://dati.beniculturali.it/luoghi/resource/CulturalInstituteOrSite/100827>
    rdf:type cis:CulturalInstituteOrSite , arco:CulturalProperty ;
    arco:hasCulturalEvent ex:CastelloInFiera2025 ,
                          ex:VerticalFenis2025 ,
                          ex:MothersDay2025 ,
                          ex:MedievalFestival2025 ,
                          ex:ValentinesDay2025 .

ex:CastelloInFiera2025
    a arco:CulturalEvent ;
    rdfs:label "Castello in Fiera 2025"@en ;
    arco:hasStartingDate "2025-04-05"^^xsd:date ;
    arco:hasEndingDate "2025-04-06"^^xsd:date ;
    arco:hasDescription "A lively medieval-themed fair set in the grounds of Fénis Castle, featuring artisan stalls, traditional food tastings, falconry exhibitions, equestrian shows, street performers, and creative workshops for children."@en ;
    arco:hasURL <https://www.lovevda.it> .

ex:VerticalFenis2025
    a arco:CulturalEvent ;
    rdfs:label "Vertical di Fénis 2025"@en ;
    arco:hasStartingDate "2025-05-01"^^xsd:date ;
    arco:hasDescription "The 8th edition of this challenging mountain running event, also serving as the Italian FIDAL National Championship for vertical kilometer races. It draws elite athletes from across the country."@en ;
    arco:hasURL <https://www.rendezvous-vda.it> .

ex:MothersDay2025
    a arco:CulturalEvent ;
    rdfs:label "Mother’s Day – Free Entry 2025"@en ;
    arco:hasStartingDate "2025-05-12"^^xsd:date ;
    arco:hasDescription "To celebrate Mother's Day, all mothers visiting with their children are granted free admission to Fénis Castle and other regional heritage sites."@en ;
    arco:hasURL <https://www.visitmonterosa.com> .

ex:MedievalFestival2025
    a arco:CulturalEvent ;
    rdfs:label "Festa Medievale – Torneo Le Cors dou Heralt 2025"@en ;
    arco:hasStartingDate "2025-07-26"^^xsd:date ;
    arco:hasEndingDate "2025-07-27"^^xsd:date ;
    arco:hasDescription "A grand medieval reenactment festival hosted in Fénis, featuring historical tournaments, costumed parades, artisan demonstrations, fire performances, a medieval banquet, and guided night tours of the castle."@en ;
    arco:hasURL <https://www.gruppostoricodifenis.it> .

ex:ValentinesDay2025
    a arco:CulturalEvent ;
    rdfs:label "Valentine’s Day – Free Entry 2025"@en ;
    arco:hasStartingDate "2025-02-13"^^xsd:date ;
    arco:hasDescription "Couples are invited to celebrate Valentine’s Day with complimentary entry to Fénis Castle and other cultural sites in the region."@en ;
    arco:hasURL <https://www.visitmonterosa.com> .
```

For further details on the SPARQL queries used and the RDF triples generated during the project, please refer to the [queries](queries.md) and [triples](triples.md) pages.
























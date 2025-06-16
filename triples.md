---
layout: default
title: Triples
---

# Triples

Below are example [RDF triples](https://en.wikipedia.org/wiki/Semantic_triple) describing the [Fénis Castle](https://en.wikipedia.org/wiki/F%C3%A9nis_Castle), its features, conservation status, and cultural events connected to it. For a detailed explanation of the process behind the creation and use of these triples, please refer to the [Methodology](#methodology) section.

## 1. Construction Date

```ttl
@prefix ex: <https://example.org/resource/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

ex:castle-of-fenis 
    a <https://w3id.org/arco/ontology/arco/ArchitecturalHeritage> ;
    rdfs:label "Castello di Fénis"@it ;
    ex:builtIn "1320-01-01"^^xsd:date .
```
## 2. Commissioner

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

## 3. Conservation Status and Interventions

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
   
   rdfs:label "Preventive and digital conservation (1990s–present)"@en .
``` 

## 4. Image Representation and Author

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

   rdfs:label "Fénis Castle - July 29, 2023"@en ;
   
   a-cd:hasAuthor ex:Hagai_Agmon_Snir .
   

ex:Hagai_Agmon_Snir

   a foaf:Agent ;
   
   foaf:name "Hagai Agmon-Snir"@en .
``` 


## 5. Cultural Events Associated with Fénis Castle

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
   
   rdfs:label "Medieval Festival – Torneo Le Cors dou Heralt 2025"@en ;
   
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



---
layout: default
title: LLMs
permalink: /llms/
---

# LLMs

In this section, we illustrate how we used [Large Language Models (LLMs)](https://en.wikipedia.org/wiki/Large_language_model), specifically [ChatGPT](https://chatgpt.com/?model=auto) and [Gemini](https://gemini.google.com/app?hl=it), to enrich the [knowledge graph](https://dati.beniculturali.it/lode/extract?lang=it&url=https://raw.githubusercontent.com/ICCD-MiBACT/ArCo/master/ArCo-release/ontologie/arco/arco.owl) on the [Fénis Castle](https://en.wikipedia.org/wiki/F%C3%A9nis_Castle).  
We applied different prompting strategies depending on the type and complexity of the missing information.

To know more about the overall process and the selection of the case study, see the [Methodology](./methodology.md) section.

## Date of Construction

**Prompting Technique:** *Zero-shot*  
We asked the LLMs to provide the year in which the Fénis Castle was built, without offering any examples.

**Prompt:**  
What year was the Castle of Fénis in Italy built?

**Goal:** To retrieve a reliable construction date, verify it using external sources, and use it to enrich the KG.

**LLM Comparison:**  
ChatGPT and Gemini provided different dates (1320 vs 1340). After cross-checking with official sources, we selected 1320 as the most consistent.

### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2018.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2019.png?raw=true)

### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2021.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2022.png?raw=true)

## Commissioner

**Prompting Technique:** *Few-shot*  
We provided a few examples to guide the model in identifying who commissioned or built the castle.

**Prompt:**  
Who built the Castello di Fenis in Aosta Valley?

**Goal:** To identify the historical figure responsible for the castle's construction and compare the precision of both LLMs' answers.

**LLM Comparison:**  
Both LLMs gave valid answers, but ChatGPT provided the exact name "Aymon of Challant," which matched the verified historical record.

### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2023.png?raw=true)

### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2024.png?raw=true)

## Representative Image

**Prompting Technique:** *Few-shot*  
We asked for a representative image of the castle, requesting metadata such as URL, author, and license.

**Prompt:**  
Please provide the URL (file .jpg, .png, etc.) for the official image of Fénis Castle in Valle d'Aosta. If there is no official photo, please indicate if there are any relevant historical images, recent photographs, or artistic representations. Please also provide the exact source of the image.

**Goal:** To find a valid image hosted on a reliable source to visually enrich the dataset.

**LLM Comparison:**  
Both answers were well-structured, but Gemini’s included incorrect author data. The ChatGPT version was used.

### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%204.png?raw=true)

### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%206.png?raw=true)

## State of Conservation and Restoration

**Prompting Technique:** *Chain of thought*  
We guided the model through a multi-step reasoning process to gather details about the castle's preservation history.

**Prompt:**  
What is the current state of conservation of Fénis Castle in Valle d'Aosta, and what restoration work has been carried out over time? Let's think about this step by step, starting with its historical condition and moving toward modern restoration efforts.

**Goal:** To collect structured historical and conservation data, including key restoration phases.

**LLM Comparison:**  
Both LLMs produced accurate content. After merging and verifying, the result was used to create triples.

### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2011.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2012.png?raw=true)

### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%209.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2010.png?raw=true)

## Cultural Events

**Prompting Technique:** *Zero-shot*  
We asked a direct question without examples to extract information about events currently hosted at the castle.

**Prompt:**  
Please provide a list of any upcoming or current events taking place at Fénis Castle in Valle d'Aosta, Italy. Include event names, dates, brief descriptions and official websites if available.

**Goal:** To add timely and relevant event data to enrich the cultural context of the castle.

**LLM Comparison:**  
Both LLMs returned plausible results. After fact-checking, we combined the answers and selected verified content.

### Chat GPT
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2014.png?raw=true)
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2015.png?raw=true)

### Gemini
![image](https://github.com/mariaclarafrisoni/Project-KE4H/blob/master/img%2013.png?raw=true)


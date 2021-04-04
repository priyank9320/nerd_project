# nerd_project
Named entity recognition and disambiguation project

Tha main task this project deals with is Entity Linking ( which has several names such as Named Entity Linking (NEL), Named Entity Disambiguation (NED), Named Entity Normalisation (NEN), Named Entity Recognition and Disambiguation (NERD) ) (https://en.wikipedia.org/wiki/Entity_linking).

One way of performing Entity Linking is dividing it into 2 steps:
1. Named Entity Recognition (NER)
2. Named Entity Disambiguation (NED)

Looking at the state of the art for Entity Linking (http://nlpprogress.com/english/entity_linking.html):
1. End to End: Kolitsas et al.2018 (https://arxiv.org/pdf/1808.07699.pdf). This has official code available.
2. Disambiguation only: Mulang' et al 2020 (https://arxiv.org/pdf/2008.05190.pdf)

The state of the art technique Mulang' et al 2020 uses a knowledge graph to provided contextual informaiton to the transformer models for entity disambiguation. This makes sense as the input text that we are going to receive will be short and and might not contain enough information for the model to perform to disambiguation on the basis of just that sentence, and so storing information from other sources into a knowledge graph is an important technique.

Looking at the libraries which could be used fast to tackle tha tasks.

Found that SpaCy library provides both features NER and NED. 

Focussing on SpaCy library to perform NER.


Now, by default the SpaCy model 'en_core_web_lg' can recognize the following entities:
1. PERSON: people, including fictional
2. NORP: Nationalities or religious or political groups
3. FAC: buildings, airports, highways, bridges, etc
4. ORG: companies, agencies, institutions, etc
5. GPE: countries, cities, states
6. LOC: non-GPE locations, mountain ranges, bodies of water
7. PRODUCT: Objects, vehicles, food, etc. (Identifies only the name of a product. Doesn't identify 'plastic bottle'.)
8. EVENT: hurricanes, battels, wars
9. WORK_OF_ART: tiles of books, songs, etc (basically, identifying their names)
10. LAW: named documents made into law
11. LANGUAGE: any named language
12. DATE: absoulte or relative dates or periods
13. TIME: times which are smaller than day
14. PERCENT: percentage, including %
15. MONEY: monetary values, including the unit
16. QUANTITY: Measurements as of weight or distance
17. ORDINAL: "first", "second", etc
18. CARDINALS: Numerals that do not fall under another category 



What entities we want to recognize:
1. Company names (use ORG)
2. Company addresses (not present)
3. Serial numbers (not present)
4. Physical Goods (not present)
5. Locations (use GPE)


So, I need to train the model for 3 additional labels.

Additional training is possible in SpaCy for the NER model.

Need annotated data for this task now.

Prodigy (https://prodi.gy/) seems to be a fast way to create the annotated data.

Also, the Entity linker feature in spacy requires a annotation tool, and Prodigy works quite godd with  spacy, youtube tutorials from the creators of prodigy are available for this task.

BUT Prodigy is a paid solution. 

Trying other solutions now.

Google Cloud Platform (GCP) provides NLP API which has entity recognition feature and it also performs the disambiguation for some entities and provides a Wikipedia link (Wikification): Refer gcp_ner.ipynb for the code

# nerd_project
Named entity recognition and disambiguation project

latest code 1: nerd_full_model.ipynb

latest code 2: entity_classifier_lstm.ipynb


I used this readme file to maintain my findings as I went about the task of Entity Linking:



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

BUT Prodigy is a paid solution. Other solutions for annotation are available but it increases the complexity as less re-useable code is available.

Trying other solutions now.

Google Cloud Platform (GCP) provides NLP API which has entity recognition feature and it also performs the disambiguation for some entities and provides a Wikipedia link (Wikification): Refer gcp_ner.ipynb for the code

BUT the default entities that are being identified dont cover some of the entities we want to extract like serial numbmers, and the "product" entity identified from documents works a bit different. For example, sentence containing "plastic bottle" will identify "bottle" as the "product" but we want the entire phrase "plastc bottle" to be categorised as "product". 

So this can be achieved by training our own entity extraction using GCP AutoML API

Now going through the tutorials/dcoumentations I found that, GCP AutoML API for entity extraction requires a jsonl file (jsonl format has each line as an individual json object/document). Either you provide the annotated document in jsonl or you provide un-annotated text in jsonl format and do the annotation in the console.

I will be creating un-annotated jsonl document and creating the annotation in the console.

Preparing the jsonl document: refer ner_data.jsonl

According to the documentation available for this API at https://cloud.google.com/natural-language/automl/docs/prepare:
" 
You supply between 50 and 100,000 documents to use for training your custom model. You use between one and 100 unique labels to annotate the entities you want the model to learn to extract. Each annotation is a span of text and an associated label. Label names can be between 2 and 30 characters, and can be used to annotate between one and 10 words. We recommend using each label at least 200 times in your training data set.
"
Finidng data and annotating it manually takes time, so I am going to try to get the data annotated for only 20 examples for each label.

Actually, wihtout providing atleast 50 examples for each label we cannot train the NER in AutoML.

After the training of AutoML API, I ran it in the console and it was able to provide quite good generalisation. But the training took around 3 hours. Good thing is google sends email when the training is done. This can surely be used for Entity Recognition purpose.



Trying SpaCy and Entity Recognition and Entity Linker:
AutoML is good hassel free solution for entity recognition as you dont have to deploy it, but I also finally understood how to perfrom the complete task (NER+NED) using just SpaCy. And using SpaCy for both the components actually provided easy integration. 
Please refer to the jupyter notebook nerd_full_model.ipynb for detailed explanation of each step and some example code showing how the model can be built for all the labels mentioned in the requirements document.

Update: (Refer folder `entity_classifier`)
I earlier assumed that the input sentence will be having some context which we could while doing entity recognition and disambiguation, but now I am looking at how to tackle this problem if the input sentence is only the entity and not the entire sentence. This causes some additional problems. The enitity recognition model cannot be used in this case, as it works best only when the input sentence is a full sentence, that is some context is present. 

Since the input sentence might contain alpha-numeric words, building a word level model won't work as the vocab size required would become too huge. Therefore I used a character level model. Another advantage of using a character level model for classification is that it will be robust towards spelling mistakes. I have created a 1 layer Bi-Lstm model (`entity_classifier_lstm.ipynb`) trained on a manually created sample data file (`datafile_lstm.csv`)

After we classify the input string into one of the 5 categories, I used separate criterias for grouping the entities. For serial numbers I use the starting characters to group them, for physical goods I use embeddings for grouping, for locations embeddings are used for initial candidate generation and then input and candidate entities are further evaluated to identify similairty (`entity_classifier_lstm.ipynb`).

Next I will be looking at how to group the companies' names and their addresses.
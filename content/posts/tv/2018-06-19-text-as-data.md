---
title:  Text as data
date:  2018-06-19 18:00:00
youtube:  LZhTBxso3Ys
categories:  data-science
summary:  The first thing is to learn how to organise and categorise text. The second was how to search and retrieve the documents or fragments of them, and the third one was how to analyse the text to extract the sentiments that the authors were expressing.
lang:  en
tags:  Data science, Meta, Tv
featured_tag:  Tv
template:  video
---

# Text as Data  
Text as data... a kind of a generic name to talk about text analysis & text classification.

The idea behind this course was to teach us the basics of three things taking into consideration that in the real world, information is in significant part contained in text documents:

The first thing is to learn how to **organise and categorise** text. The second was how to **search and retrieve** the documents or fragments of them, and the third one was how to **analyse** the text to extract the sentiments that the authors were expressing.

## Lecture 1. Introduction to text  
In the first lecture, we reviewed how can text be represented as **sparse vectors**, how can we calculate different **similarity measures** between two vectors using similarity measures of sets. After that, we checked the **bag of words** representation, as well how can we go beyond working with single, tokenised words and consider pairs or triples of words using **n-grams** and another similarity measure, the **cosine similarity**. We finalised this lecture by reviewing the problems with using term frequency as the only criteria to describe our documents.

## Lecture 2. Text distributions  
As we learned in the previous lecture, using the *raw term frequency* is not the best idea, and thus, in this lecture, we saw different options to overcome this issue, such as **operating in the log space** for the term frequency, as well as how to consider the collection using something known as the **inverse document frequency or IDF**. 

## Lecture 3. Distributions and clustering  
Following with term distribution, we saw how it could be used to cluster documents in an unsupervised manner using algorithms such as k-means or hierarchical clustering.  

## Lecture 4. Language Modelling  
For the fourth lecture, we learned another approach to representing documents and that is through probabilities: the probability of a sequence of words and the probability of a word given a sequence of words, via Language Models, and how considering n-grams allows us to get better models.  

## Lecture 5. Word Vectors  
In lecture number five we learned about word embeddings, which is a somewhat more modern approach of representing words as dense vectors, created from the context of each word. 

## Lecture 6. Text classification  
Lecture six was about classification; we briefly reviewed classifiers such as Naïve Bayes, logistic regression, SVM and decision trees.  

## Lecture 7. Intro to NLP   
Natural Language Processing was the topic of the seventh lecture, in this case, things like including part of the speech tagging and dependency parsing.

## Lecture 8. More on text classification  
Lecture eight was another look at classification, reviewing some good practices to avoid over or underfitting, as well as some ethical concerns that may arise from using machine learning for real-world applications.  

## Lecture 9. More on clustering   
Just like the previous lecture, this one was about revisiting an old lecture from another perspective, in this case: clustering using **Latent Semantic Indexing**.  

## Applications
The last lectures were about applications of what we saw during the course:  

### Lecture 10. Information Extraction, Named Entity recognition and Relation Extraction

### Lecture 11. Question Answering, and QA architectures

### Lecture 12. Dialogue Systems, chatbots, slot filling

## Labs. 
The labs for this course were by far the most interesting of any other course I had this semester (don't feel bad Big Data, yours were cool as well). We worked with tools like NLTK and spaCy, and Google's version of the Jupyter Notebooks called Colab. 

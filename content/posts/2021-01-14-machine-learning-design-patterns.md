---
layout: book
title: Machine Learning Design Patterns
date: 2021-01-14 14:00:00
summary: A book about some common patterns (a pair of problem-solution) that arise when training and deploying machine learning models. 
sidebarimage: https://i.imgur.com/1olZiIn.jpg
tags: book-review, software development, machine learning, oreilly
---  

Author: Valliappa Lakshmanan, Sara Robinson, Michael Munn
ISBN: 9781098115784

As the title unveils, this book talks about some common "patterns". A pattern is a pair comprised of a common problem recurring problem and a commonly agreed generic solution that you can customise to your specific domain. 

The book seems tailored towards people with ready knowledge of common machine learning algorithms, so if you are just getting started with ML I would not recommend this book just yet. 

This is one of those "reference" books so there is no need to read it in a particular order, and instead it is one that you can keep in your bookshelf to read when the need arises. The book covers patterns related to **Data Representation** (hashed features, embeddings, feature interactions...), **Problem Representation** (when can reframing our problem help us, model ensembles, balancing...), **Model Training** (overfitting, checkpointing, hyperparameter tuning...), **Model Serving** (batch serving, model evaluation, serverless models...), **Model Reproducibility** (feature stores, model versioning, data splitting ...) and **Responsible AI**.

To be frank, I already knew of some of these patterns, however it was nice to be able to find a proper formalisation of the problem along with an explanation to why they work, their trade-offs and alternatives.

In terms of code, this being a reference book goes very light on code, with code samples written to be used in Python (TensorFlow, Keras and Sci-Kit Learn) and SQL (BigQuery ML, which I had no idea it exsisted); the authors provide a GitHub repository for it.



## Some quotes  

> For each successful result published in a research paper or a corporate blog, there are hundreds of reasonable-sounding ideas that have entirely failed.  

> (...) much of the challenge in ML is similar to one of the biggest challenges in software—resisiting the urge to build pieces that are not needed yet.  

> An ML program doesn't just have to run-it should produce accurate predictive outputs.  

> Testing a model's behavior is hard. The majority of code in an ML pipeline is not about the training pipeline or the model itself, however.  

> In reality, most datasets are a collection of approximate measurements that ignore a larger context.  

## Some ideas

 - The idea of using clustering algorithms to guide the exploratory data analysis when it comes to examine individual datapoints, rather than just randomly selecting instances.  
 - What needs to be happen with a model to be deployed on a mobile device.  
 - The idea of having a filtering model, before our actual inference model, that predicts whether the current input will yield an acceptable answer.  
 - The idea of multi-armed bandits to test variants of experiments
 - The idea of federated learning

## Some links

 - <a href="https://blog.insightdatascience.com/automated-front-end-development-using-deep-learning-3169dd086e82" target="_blank">Automated front-end development using deep learning</a>
 - <a href="https://arxiv.org/abs/1705.07962" target="_blank">pix2code: Generating Code from a Graphical User Interface Screenshot</a>
 - <a href="https://arxiv.org/abs/1604.06737" target="_blank">Entity Embeddings of Categorical Variables</a>
 - <a href="https://uchicago-cs.github.io/debugging-guide/" target="_blank">The Debugging Guide by The University of Chicago</a>
 - <a href="https://research.google/pubs/pub46555/" target="_blank">The ML Test Score: A Rubric for ML Production Readiness and Technical Debt Reduction</a>
 - <a href="https://arxiv.org/abs/1810.03993" target="_blank">Model Cards for Model Reporting</a>
 - <a href="http://sites.computer.org/debull/A18dec/p5.pdf" target="_blank">On Challenges in Machine Learning Model Management</a>
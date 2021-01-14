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

Get the book here: <a target="_blank" href="https://www.amazon.es/gp/product/1098115783/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp09-21&creative=24630&linkCode=as2&creativeASIN=1098115783&linkId=42b8cc87f60bed7f12ae29726475ce9d">Amazon España</a> &bull; <a target="_blank" href="https://www.amazon.com/gp/product/1098115783/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp0ac-20&creative=9325&linkCode=as2&creativeASIN=1098115783&linkId=2dfe90f3be1555b385bffcff45c16cba">Amazon US</a> &bull; <a target="_blank" href="https://www.amazon.com.mx/gp/product/1098115783/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp0c-20&creative=9325&linkCode=as2&creativeASIN=1098115783&linkId=9fefb5be3e6efef020c0a4abf4e9a0ac">Amazon México</a> &bull; <a target="_blank" href="https://www.amazon.co.uk/gp/product/1098115783/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp07-21&creative=6738&linkCode=as2&creativeASIN=1098115783&linkId=2cc3e4063d9c3d682e8b8fb69b5b696a">Amazon UK</a>

As the title unveils, this book talks about some common "patterns". A pattern is a pair comprised of a common problem recurring problem and a commonly agreed generic solution that you can customise to your specific domain. 

The book seems tailored towards people with ready knowledge of common machine learning algorithms, so if you are just getting started with ML I would not recommend this book just yet. 

This is one of those "reference" books so there is no need to read it in a particular order, and instead it is one that you can keep in your bookshelf to read when the need arises. The book covers patterns related to **Data Representation** (hashed features, embeddings, feature interactions...), **Problem Representation** (when can reframing our problem help us, model ensembles, balancing...), **Model Training** (overfitting, checkpointing, hyperparameter tuning...), **Model Serving** (batch serving, model evaluation, serverless models...), **Model Reproducibility** (feature stores, model versioning, data splitting ...) and **Responsible AI**.

To be frank, I already knew of some of these patterns, however it was nice to be able to find a proper formalisation of the problem along with an explanation to why they work, their trade-offs and alternatives.

In terms of code, this being a reference book goes very light on code, with code samples written to be used in Python (TensorFlow, Keras and Sci-Kit Learn) and SQL (BigQuery ML, which I had no idea it exsisted); the authors provide a GitHub repository for it.

## Some quotes

 > Rooms lit on two sides, with natural light, create less glare around peoplñe and objects; this lets us see things more intricately; and most important, it allows us to read in detail the minute expressions that flash across proples faces... -- I don't know, I found this quote to be relatable.

 > (...) your model's predictions will be a direct reflection of that data. As a result, machine learning models are often referred to as "garbage in, garbage out".

 > Training an ML model involves several artifactss that need to be fixed in order to ensure reproducibility.

 > When defininfg the goals for your model, it's important to consider the needs of different teams across an organization, and how each team's needs relate back to the model.

 > Deployment is not the end of a machine learning model's life cycle. How do you know that your model is working as expected in the wild?

 > It is important to realize that the purpose of training an ML model is to make predictions on unseen data.

## Some ideas

 - The idea of separating these two terms: input data and input features.
 - Prepare for the cold-start problem (when one-hot encoding) by reserving all zeros for out of vocabulary inputs
 - Task reframing
 - Infer ground truth labels from how the user interacts with the model's predictions
 - Bridging old data to new data
 - Lineage tracking
 - Data drift

## Some links

 - <a href="https://cloud.google.com/bigquery-ml/docs/introduction" target="_blank">BigQuery ML</a>
 - <a href="https://cloud.google.com/bigquery-ml/docs/introduction" target="_blank">TabNet</a>
 - <a href="https://www.tensorflow.org/tfx" target="_blank">TensorFlow Extended (TFX)</a>
 - <a href="https://www.kubeflow.org/" target="_blank">Kubeflow</a>
 
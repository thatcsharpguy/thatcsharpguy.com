---
layout: book
title: Building Machine Learning Powered Applications
date: 2020-08-06 14:00:00
summary: A book about what are the pieces around an application that uses machine learning at its core, a good insight into what is beyond training models.
image: https://i.imgur.com/1olZiIn.jpg
tags: book-review, software development, machine learning, oreilly
---  

Author: Emmanuel Ameisen  
ISBN: 9781492045106

This is a book about the whole process of putting a machine learning model into production, all the engineering that sometimes the data scientists are not aware of or take for granted. It does not explain any algorithms or how to train a model but what to do before and after we have already trained one. I highly recommend getting this book, especially if you are:   

 - About to start a new machine learning development
 - Coming from the engineering side of software  
 - Want to know what does it take for your new model to become production-ready

The book itself is light on code; there is a GitHub repo that the author uses to showcase an entire use case throughout the whole book. However, it is possible to just read the book without the need to look at the code since it is not the main reason someone would buy this book.

Get the book: <a href="https://www.amazon.com.mx/gp/product/149204511X/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp0c-20&creative=9325&linkCode=as2&creativeASIN=B0842ZD3Y7&linkId=98deaff870842d73df0163ccca58f9ab" target="_blank">Amazon Mexico</a> &bull; <a href="https://www.amazon.es/gp/product/149204511X/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp09-21&creative=24630&linkCode=as2&creativeASIN=149204511X&linkId=9b9ef741a6bc51f0bfc98fefa52c2f70" target="_blank">Amazon España</a> &bull; <a href="https://www.amazon.com/gp/product/149204511X/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp0ac-20&creative=9325&linkCode=as2&creativeASIN=149204511X&linkId=0ba6e75cc6eac0ea16a7a2237a8730c9" target="_blank">Amazon US</a> &bull; <a href="https://www.amazon.co.uk/gp/product/149204511X/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=csharp07-21&creative=6738&linkCode=as2&creativeASIN=149204511X&linkId=c36cfd931ec5c6ec4b1423198154fa51" target="_blank">Amazon UK</a>

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
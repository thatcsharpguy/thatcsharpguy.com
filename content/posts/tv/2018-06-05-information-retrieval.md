---
title:  Information Retrieval
date:  2018-06-05 18:00:00
youtube:  pONlcxVRRnE
categories:  c-sharp
summary:  Information retrieval is a field concerned with the structure, analysis, organisation, storage, searching and retrieval of information.
featured_image:  featured.png
lang:  en
images_folder:  /tv/hola-mundo/
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

# Information retrieval   
Well, the title is self-explanatory but a good definition is the following: information retrieval is a field concerned with the structure, analysis, organisation, storage, searching and retrieval of information. In this case, we focused on textual information.

We started by looking at the...  

## Architecture of IR systems
And its three components: Query terms, a collection of documents and the retrieval system. Then we continued with the critical parts of its operation:

### Document processing  
We need to prepare the documents to be retrieved, and this is done through a series of steps: the first one is **tokenisation** that is: converting sentences into words, **stopword removal**: getting rid of highly frequent words and finally, **conflation/stemming** taking similar words and transform them into a single unique symbol. 

All of this to create a structure called the **Inverted index**.

### Inverted index
The inverted index is a structure that maps words to the documents where they appear. I won't go into much detail about inverted indexes in this video but if you want to know more, just let me know in the comments.

### Retrieving 
Finally, we took a look at one of the basic algorithms for document retrieval given a query: the *best-match* algorithm that only considers whether a document contains or not a word of the query we are evaluating. However, most of the time this approach is not a good idea, and we then reviewed **term weighting**.

## Term Weighting  
The idea behind term weighting is to give each word a value that represents its importance for a document. To understand a bit more this idea we first studied the **Zipf's law** and how it applies to vast collections of texts.

After that, we learned about the heuristic of using something known as **Inverse Document Frequency** to take into consideration the assumption that if a term appears many times in different documents in the collection, it is less representative of a single document. 

## Vector Space Model
We also saw another way to represent documents: as high dimensional vectors, where each one of these dimensions represents the words in our whole vocabulary. Operating in a vector space allows us to compute similarities between other documents and queries.

However, what's the idea behind building a retrieval system if there is no way to evaluate it?

## Evaluation  
With this in mind, we took a look at how are retrieval systems evaluated. Starting from the creation of **test collections**, that are extensive collections of documents accompanied by queries and relevance assessments, provided by humans.

Metrics such as Precision @ rank R, Precision at standard recall levels, and Average precision and their averaged values, like Mean Average Precision and Mean Reciprocal Rank or the Discounted Cumulative Gain.

## Relevance Feedback  
Then we saw how to improve, or at least try to improve, the results our system outputs by using feedback from the user, something called **relevance feedback**. Specifically, three different kinds of feedback: **Explicit feedback**, **implicit feedback** and *pseudo-relevant feedback* and their relationship to **query expansion**. 

Following the trend of involving the user in the retrieval process we looked into **Interactive IR**, and some other ways to involve the user in the process of information retrieval, mainly through the user interface of the results output. After this, we went back to other, in this case, *advanced retrieval models*...

## Advanced Retrieval Models
All the retrieval models we had seen so far were... simple. 

In this section, we started taking into account probability and considering the relevance estimation as a classification problem. Using retrieval models like BM25 or Language models, or even some more advanced ones like PL2, part of the Divergence From Randomness framework. This, also considering proximity between terms in an attempt to improve search results.

## Learning to Rank 
Moreover, we can use machine learning to refine the final ranking shown to the user. We reviewed the *cascade-like* pipeline to filter documents to prepare our machine learning models what kind of features we can use and the three kinds of learning tasks: Pointwise, Pairwise and Listwise.

For a while, we focused on **web search** and its challenges. Like personalising the results for each user, or considering the context the user is in to resolve ambiguities. Keeping on with web search, we studied a way to get documents through **web crawling** which is not an easy task. You need to consider issues like respecting the servers you're querying or withstand traps in the web or malformed htmls, not to mention the fact that we need to keep track of the documents we've downloaded,  and that we can't do it at high scale with a single computer.

Then we went back to evaluation...

## Online evaluation   
The evaluation that we studied earlier in the course is known as offline evaluation, but a more exciting and challenging task is to evaluate our system while real users are using it. We reviewed two techniques: A/B testing and Interleaving.

## IR infrastructures and efficiency
After all we saw, we had to review how to make them efficient by reviewing compression techniques for the index such as **pruning** or **unary or gamma coding**, as well as caching techniques for queries, terms or documents. We saw some of the infrastructures and how they affect the performance and evaluation of the queries that are issued to the system.

## Real-time IR  
We saw the necessity and challenges of performing some of the tasks we previously saw but using streams of data, such as tweets or facebook status updates. 

## Applications Beyond Search   
To finalise the course, we were introduced to other applications such as Text summarisation, real-time event detection, Recommendations and Question Answering.

So that was about it, on the practical side of things we worked with Terrier, with is a retrieval system developed here at the University of Glasgow.

---
title:  Machine Learning (foundations)
date:  2018-08-07 18:00:00
youtube:  7CYF9pd39gQ
categories:  data-science
summary:  This course was a practical introduction to the foundations of machine learning. 
lang:  en
tags:  Data science, Meta, Tv
featured_tag:  Tv
template:  video
---

Yes, finally, this is the last video in the series about what I was doing here at the University of Glasgow. And as I said, it is about Machine Learning.  

This course was purely mathematical, and the lectures were a bit weird, most of the time the lecturer was just doing the maths operations... I, in particular, was not used to that kind of lectures.  

However, let's discuss what the lectures were about, again everything was nicely organised into weeks:

During the **week 1 and 2 we focused on Linear regression** while being introduced to some of the machine learning concepts like supervised and unsupervised learning. As well as the fact that we need to make  **assumptions** when approaching a problem of learning.

As for the mathematical side, we created a **linear model**, that is a function of our input values and a set of parameters. Machine learning aims to find the "best" values for those parameters.

To find the "best" values is necessary to define a measure of performance, and in the case of regression, we can do this through the **loss** function. We can do this using several techniques, but one of them is using partial derivatives.

Once we have calculated the parameters, we can start making predictions by plugging new input values into our model.  

During the first two weeks we only saw how to deal with simple models. **In week three**, we started to deal with more complex models involving more than two parameters.

Here is when the vector and matrix representation of a problem is useful as they can be used to represent a cleaner an compact way all the variables and parameters involved in a problem.

We also reviewed the concepts of **complexity of a model**, **generalisation**, **over-fitting** and how to use **cross-validation** to make an efficient use of our data and improve our models.

During the **fourth week** we started approaching the linear modelling problem from a different angle by introducing **noise into the model**, this, considering that our model could be represented by a line with noise added. As an introductory step we had a quick review of what **random variables** are.

This is a different approach to obtain the parameters of our model as in this case we are not using the loss function. Instead, we are **maximising the likelihood** of obtaining the target values of the inputs in our models. 


## Week 5 and 6: Going Bayesian  
The theme of the **fifth and sixth weeks** was the Bayesian method.

We defined a **prior** and **likelihood**, and we used the Bayesian rule was used to compute the **posterior** density. Here we assumed that the prior is conjugate to likelihood to make the computation tractable. This allows us to find the posterior distribution parameter's that generate our data.

The professor walked us through some of the exercises with different distributions of prior and likelihood. Also, he briefly introduced the idea of using marginal likelihood as a possible model selection criterion.

We also saw how to calculate confidence in our parameters and predictions.

**Weeks number 7 and 8** were devoted to supervised classification algorithms that is, classify a set of labelled data points. We covered two classes of algorithms: those that produce probabilistic outputs and those that produce non-probabilistic outputs. 

We learned about **k-nearest neighbours** a fast non-probabilistic classifier which is sensitive to class imbalance, that is, having more examples of one label than any other, and outliers, extreme values.

**Bayes classifier** a probabilistic classifier that makes the decision based on Bayes rule. The main idea behind the algorithm is to decide the prior and likelihood densities and then compute the predictive probabilities.   

**Logistic regression** a probabilistic binary classifier whose output is the probability of a new object belonging to a particular class. In this lecture, the professor introduced three approximation techniques to work with difficulties when trying to apply. The three techniques that we looked at were **point estimate using MAP**, density approximation using **Laplace approximation** and a sampling technique known as **Metropolis-Hastings**. I’m not going into more details about these techniques.

The last classifier we reviewed was **support vector machines**, an algorithm that finds the decision boundary which maximises the margin between two classes of objects. 

As part of these lectures we reviewed the intuition of how to represent data that is non-linearly separable by projecting it in some other space.

The content of these two lectures helped us in understanding the mathematical concept and geometrical interpretation behind several classification algorithms. These insights help us to describe the models and also to decide which algorithm to choose for a specific use-case.

The **final two weeks** we learned about **unsupervised learning**, particularly the task of clustering. That is group sets of unlabelled data points. In these two lectures we covered two algorithms. 

The first one was **K-means** that is a simple non-probabilistic clustering algorithm. It is very simple, but it is also sensitive to initialisation and may not be so helpful as it converges to local minima around the initial cluster centres. 

The second one was **Gaussian Mixture Models** this is a probabilistic extension of K-means for soft clustering.

## Final note  

Finally I would say that the content of the course was good, we gained a lot of knowledge about the mathematical foundations of some of the most popular machine learning models, stuff that you don’t usually see in blog posts. However, I have the feeling that the lectures could have been better delivered by our professor.
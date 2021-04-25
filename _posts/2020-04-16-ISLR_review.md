---
title: " ISLR review: An ideal textbook to start your ML journey"
excerpt: "This article is for those eager to study Machine Learning or start Kaggle, but confused by all the available resources. I give a review of the textbook Introduction to Statistical Learning and write about why you should pick it up."
categories:
  - Machine Learning
tags:
  - book review
  - machine learning
permalink: /ISLR_review
published: true
comments: true

---

Are you wondering what to study next? Do you feel like your Machine Learning foundations are a bit shaky?  Would you like to start Kaggle, but don't know how exactly a decision tree works? If yes, then you are in a similar situation to me a couple of months ago. 

In this article, I am writing about my experiences working through the textbook Introduction to Statistical Learning in R (James, Witten, Hastie, Tibshirani, 2013), abbreviated as ISLR. I will shortly explain my background, who and what I recommend the book for, and some tips for getting the most out of it.

### Why I picked ISLR

I have started my Machine Learning journey about a year ago, when bored with lockdown I did Andrew Ng's famous [Machine learning course on Coursera](https://www.coursera.org/learn/machine-learning). While I have loved the course and it gave me a foundation to work with, I felt like I needed more knowledge about different techniques, model selection and Python packages to use. I was looking to start Kaggle, so I decided to do the [Kaggle Learn](https://www.kaggle.com/learn) courses. I got discouraged fairly quickly, when one of the first Kaggle Learn tutorials was about implementing a decision tree, but didn't explain what it was or how it worked under the hood. After half an hour of frantic searching and reading all too shallow blog posts, I decided I needed something where knowledge is more detailed and organized - a textbook. 

When picking the textbook, I was looking for the following:
- Introductory/undergrad level, easy to follow and not intended for PhD students
- Goes into details of the Maths, but explains intuition as well
- Introduces all basic ML techniques
- Practical, has coding exercises to implement what I have learnt 
- Can be completed within 2-3 months

After some looking around on Reddit, I found that the most recommended intro book seemed to be ISLR. It also satisfied all the requirements I was looking for, so I gave it a go.

### All about ISLR

The good thing about this book is that it's freely available to download from the [authors' website](https://www.statlearning.com/). You can just take a peek and decide whether you like it or not before committing to an expensive textbook. However, I do recommend getting a physical copy for working through it.

As the title says, ISLR introduces Statistical Learning techniques with examples and exercises in R. But what is Statical Learning? The book defines it as a "vast set of tools for *understanding data*" In general there is a fight going on between Statistics and ML, you can read about it in [this very entertaining post](http://brenocon.com/blog/2008/12/statistics-vs-machine-learning-fight/). Concerning this book, there is a slight cultural difference to usual ML resources, sometimes different terminologies (e.g. "Lasso/Ridge" instead of "L1/L2"), some focus on classical Statistics concepts (like p-values and hypothesis testing), but it mostly explains methods you could usually classify as ML algorithms, such as regression, SVMs, tree-based methods and unsupervised learning concepts. 

This book was designed to be accessible to people from different industries and backgrounds, who need to work with data in their profession without necessarily having university-level Maths education.
The book does not assume more mathematical background than an introductory Maths course. It doesn't use matrix or vector notation to make it more accessible. It also doesn't expect any previous R experience, and carefully explains every line of code, including writing functions and making plots. 


The books is split into 10 Chapters, starting with an introductory chapter explaining the notation, bias/variance trade-off, and introducing R. 
After the first chapter, all further chapters are around a selected topic, slowly building up from Linear Regression to more complicated concepts, such as Random Forests and Hierarchical Clustering. Every chapter has Labs at the end that guide you through implementing the methods taught in the chapter, and further conceptual and coding exercises to deepen your understanding.

In every chapter, the focus is on a high-level understanding of the concept and good intuition. I found that the level of details was sufficient for what I needed the book for, to confidently use the implementation of a method in my own projects. It compares similar methods by listing pros and cons, and gives examples when one method would be better over another method. It is illustrating every method with simple datasets and plausible real-life examples, which further helps intuition. I have particularly liked the easy to understand plots and graphs on nearly every page. Whenever something is explained, there is a plot for it illustrating the concept on data. 

### Who is ISLR recommended for?

I think you would definitely get a lot of value out of reading the book, if you: 

- want one single source of information to explain the basics of most popular ML methods instead of spending loads of time searching for relevant articles and tutorials of varying quality
- want to do Kaggle, but feel intimidated by all the available ML methods to use on your data
- know how the algorithms work in theory, but don't know how to start implementing them
- you want a preparatory textbook that explains intuition, before jumping into the Maths heavy grad-level textbooks
- want to remind yourself of the Maths behind algorithms before Data Science interviews. The material in this book very helpful (and sufficient) for several Data Scientist grad job interview online rounds.

### Some tips 

1. Porting  ISLR to Python
This book is in R, and lots of people, including me, don't use R for their data science projects. However, if you are Team Python like me, this book is still amazing. You can read it and take on a challenge, like I did: solve the labs in Python instead.

This gives you a framework to practice the theoretical concepts by following the exercises, but less hand-holding: you can't just retype the R code to complete the labs, you have to first figure out how to do them. This will mean getting deep into Numpy, scikit-learn and StatsModels library documentation, and understanding the parameters in each function to get the same behavior as in R. In all honesty, doing this provided at least as much benefit and learning for me as reading the book did. 

The good thing is, if you are stuck, you can always just look up someone else's solution, as many people have already done this. (Here's [my GitHub repo with the Python labs](https://github.com/alexandrasouly/ISLR-but-python), but you can always just google "ISLR in Python" and find a dozen repos.)

2. Certified online course + videos
I prefer books to videos, but we are all different.
If you learn better from videos, you don't have to worry - the authors have recorded a course based on this book to supplement your reading. 
The 15 hours Youtube playlist is linked from [here](https://www.dataschool.io/15-hours-of-expert-machine-learning-videos/).  
If you get more motivated by working towards a certificate that you can purchase to show it on your CV, that option also exists. Stanford offers the course [here on edX](https://www.edx.org/course/statistical-learning), with additional exercises and feedback if you decide to purchase the certificate. 

3. Time frame
If you want to keep yourself accountable, here's an estimate how you could set yourself goals to finish the book:
- I estimate the book can be finished in 10 weeks, with about 5-6 hours of work per week (8-10 if you are porting to Python by yourself)
- Going through the text of a chapter takes about 2 hours if you are taking notes, half an hour for the conceptual exercises, one hour for the lab in R (or up to 4 in Python for me), and 1-2 hours for the coding exercises. Of course there are shorter and longer chapters, but this is my rough estimate for the average.

Working through a textbook alone can be a bit demotivating - no reminders and deadlines turning red like on online courses. I recommend you set yourself a goal for finishing each chapter by some specified date to keep yourself motivated.

### What to do next?

If you have finished the book, you should feel a bit more comfortable applying Machine Learning methods to solve your chosen problems. You are probably ready to start learning more and practicing on Kaggle if you want. I recommend starting out with the [Tabular Playground Series](https://www.kaggle.com/c/tabular-playground-series-apr-2021) if you are looking for a gentle competition introduction. I can wholeheartedly recommend the article [Progressively approaching Kaggle by Rohan Rao](https://towardsdatascience.com/progressively-approaching-kaggle-f58db71a42a9) if you would like to know more on how to approach that.

If you just can't get enough of the Maths behind this technique and have a solid foundation on Linear Algebra and Calculus, I recommend you go and check out (The Elements of Statistical Learning)[https://web.stanford.edu/~hastie/ElemStatLearn/](ESL). ISLR is technically the introductory book to ESL, which goes into the Maths *a lot more*, and is intended more to be a reference book for PhD students and researchers than something you just read through.

I hope you found this review useful, and I wish you good luck on your Machine Learning journey! 
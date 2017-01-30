---
layout: post
title:  "Getting started with similarity"
date:   2017-01-30 21:18:17 +0100
categories: similarity
---
One of the most fundamental concepts in my research is textual similarity: what is it that makes two texts similar or dissimilar? This is a well studied problem in Natural Language Processing and I will present here some of my findings from my initial research of the field. This is only intended to be a summary for my own sake, so if you're here looking for in-depth analysis, you are in the wrong place.

# Why Similarity?

I am interested in similarity because I want to explore communication and the transmission of ideas in society. My assumption is that similarity can be a useful tool for measuring the degree of influence of any given text. For example, if a report by a lobby agency is widely read and accepted by bureaucrats, we might expect that they will reuse elements of this report when they come to write a report on the same subject matter. But how can we measure similarity? This is the job of Natural Language Processing.

# Bag O'Words

The most basic paradigm for Text Mining is to treat documents simply as bags of words, i.e. collections of word tokens devoid of their semantic context. For example, the sentence "Robespierre killed lots of people", would be converted into the array `['robespierre', 'killed', 'lots', 'of', 'people']` or, with some pre-processing (stemming, sorting and stopwords removal), `['kill', 'lots', 'people', 'robespierre']`.

The first thing I notice is the aforementioned loss of semantic context. To illustrate this problem, we can demonstrate that the sentence above will be equivalent to another sentence with a completely different meaning; if we move the words around a bit we get the sentence 'Lots of people killed Robespierre' which will be reduced to the exact same array once processed, meaning that in the eyes of your algorithm, the sentences will be identical.

This is strange. Despite this, Bag O'Words is widely used, so it must work pretty well in most scenarios. I am aware that there are other approaches but I haven't begun to explore them yet.

# Enter The Vector

Once we have our bag o' words, the next step is to represent it as a numeric vector. This is a common trick in Data Mining and Machine Learning and allows us to do all sorts of peculiar things to our innocent document, including computing similarity using geometric operations. In this representation, each word is a dimension and each document contains numbers indicating their frequency. Let's imagine we have another sentence with the text 'Lots of people zapped Robespierre'.


| | kill  | lot | people  | robespierre  | zap |
|-----------------------|
|**doc1**  | 1  | 1  | 1  | 1  | 0 |
|**doc2**  | 0  | 1  | 1  | 1  | 1 |
{:.table}

As we can see, each document has been reduce to ones and zeroes, representing the presence or absence of a token. Now that we have converted our docs, we can start to compute similarity.

# The Maths bit

The thing about vectors is that they can be represented as points in graphs. Normally, we think of graphs in terms of two or three dimensions, but we can actually use any number of dimensions in reasoning about vectors, even though we cannot represent these visually. Now since they can be represented as points, we can also compute their distance from each other. Just as the distance between two cars can be measured using spatial dimensions, we can also measure the distance between two documents using their tokens as dimensions. In this context, we can then begin to discuss appropriate methods for measuring distance.

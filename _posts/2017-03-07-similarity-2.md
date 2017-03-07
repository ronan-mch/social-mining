---
layout: post
title: "More similarity - beyond Bag O'Words"
categories: similarity
date: 2017-03-07
---
In my previous posts I looked at some ways of measuring document similarity and applied these to a concrete example. Now, as I mentioned, Bag O' Words is cool and everything, but it's a bit naive.

In general, it has no concept of meaning; a word is just a string of text and has no inherent semantic value. This means that Bag O' Words will slip up whenever you have a word with multiple meanings (for example __bank__ can be the side of a river or a company that lends money) or when multiple words have the same or similar meanings (such as computer and laptop for example). Think of the following example sentences: "The bank lent me money for the house", "I have to pay my mortgage", "She left her jacket on the river bank". If we remove stopwords and structure these in a term matrix we'll get something like this:
<table class="table">
<thead>
<tr>
<td>Document</td>
<td>bank</td>
<td>house</td>
<td>jacket</td>
<td>left</td>
<td>lent</td>
<td>money</td>
<td>mortgage</td>
<td>pay</td>
<td>river</td>
</tr>
</thead>
<tbody>
<tr>
<td>Doc1</td>
<td>1</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>Doc2</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>1</td>
<td>0</td>
</tr>
<tr>
<td>Doc3</td>
<td>1</td>
<td>0</td>
<td>1</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
</tr>
</tbody>
</table>

In this example, the first two sentences are pretty similar semantically, they both concern loans to buy houses, but according to Bag O' Words they're not at all similar because they don't have any common terms. The third sentence on the other hand is about something completely different, but is going to be marked as somewhat similar to sentence one because they have the common term __bank__. Silly Bag O' Words!

Another issue with Bag O' Words is that it can lead to some pretty hefty term matrices over larger corpora, as each unique term will lead to another column in the matrix and further load on the computer's memory and also longer processing time as computations may have to be done on each column (note that the first problem is somewhat mitigated by using sparse matrices in which only the matrix cells with content are stored).

## So what's the solution?

Latent Semantic Analysis is a method that seeks to overcome these limitations of Bag O' Words by deriving semantic features from a weighed term matrix. It relies on the assumption that words of similar meaning will be collocated. In practice it does a matrix decomposition, which means transforming one matrix into three component matrices, one of which will map the original documents a new set of compound features based on the original terms. This set of compound features can be used to identify the primary categories within a corpus and the new compound feature matrix can be used to assess similarity and distance in a more robust way.

In my next post, I'll assess the method in practice.

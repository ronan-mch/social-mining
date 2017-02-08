---
layout: post
title:  "Getting started with similarity"
categories: similarity
date: 2017-02-08 20:50 +0100
---
One of the most fundamental concepts that I'm researching is *textual similarity*: what is it that makes two texts similar or dissimilar? This is a well studied problem in Natural Language Processing and I will present here some of my findings from my initial research of the field. This is only intended to be a summary for my own sake, so if you're here looking for in-depth analysis, you are in the wrong place.

## Why Similarity?

I am interested in similarity because I want to explore communication and the transmission of ideas in society. My assumption is that similarity can be a useful tool for measuring the degree of influence of any given text. For example, if a report by a lobby agency is widely read and accepted by bureaucrats, we might expect that they will reuse elements of this report when they come to write a report on the same subject matter. But how can we measure similarity? This is the job of Natural Language Processing.

## Bag O'Words

The most basic paradigm for Text Mining is to treat documents simply as bags of words, i.e. collections of word tokens devoid of their semantic context. For example, the sentence "Robespierre killed lots of people", would be converted into the array `['robespierre', 'killed', 'lots', 'of', 'people']` or, with some pre-processing (stemming, sorting and stopwords removal), `['kill', 'lots', 'people', 'robespierre']`.

The first thing I notice is the aforementioned loss of semantic context. To illustrate this problem, we can demonstrate that the sentence above will be equivalent to another sentence with a completely different meaning; if we move the words around a bit we get the sentence 'Lots of people killed Robespierre' which will be reduced to the exact same array once processed, meaning that in the eyes of your algorithm, the sentences will be identical.

This is strange. Despite this, Bag O'Words is widely used, so it must work pretty well in most scenarios. I am aware that there are other approaches that attempt to preserve the semantic content of documents but I haven't begun to explore them yet.

## Enter The Vector

Once we have our bag o' words, the next step is to represent it as a numeric vector. This is a common trick in Data Mining and Machine Learning and allows us to do all sorts of peculiar things to our innocent document, including computing similarity using geometric operations. In this representation, each word is a dimension and each document contains numbers indicating their frequency. Let's imagine we have another sentence with the text 'Lots of people zapped Robespierre'.


| | kill  | lot | people  | robespierre  | zap |
|-----------------------|
|**doc1**  | 1  | 1  | 1  | 1  | 0 |
|**doc2**  | 0  | 1  | 1  | 1  | 1 |
{:.table}

As we can see, each document has been reduced to ones and zeroes, representing the presence or absence of a token. Now that we have converted our docs, we can start to compute similarity.

## The Maths bit

The thing about vectors is that they can be represented as points in space. Normally, we think of graphs in terms of two or three dimensions, but we can actually use any number of dimensions in reasoning about vectors, even though we cannot represent these visually. Now since they can be represented as points, we can also compute their distance from each other. Just as the distance between two cars can be measured using spatial dimensions, we can also measure the distance between two documents using their tokens as dimensions. In this context, we can then begin to discuss appropriate methods for measuring distance.

### Method 1: Euclidean Distance

The classic method for measuring distance is Euclidean distance which some of us may dimly recall from school days. In this method, the distance between two points, $$a$$ and $$b$$ (or documents in our case) is given by the following formula:

$$\sqrt{(b_1-a_1)^2+(b_2-a_2)^2...(b_n-a_n)^2}$$

### Method 2: Inverse Cosine Similarity

If we think about our documents as vectors, cosine similarity measures the angle between these vectors. This is useful in our case because the resulting distance measure will not be affected by the scale of the vectors, i.e. if one document is bigger than the other, cosine similarity will still help us derive a meaningful distance measurement. The formula for cosine similarity involves some rather unpleasant matrix operations:

$$ cos(\theta) = \frac{A \bullet B}{||A|| ||B||} $$

We can subtract the output from 1 in order to convert it to a distance metric.

### Method 3: Jaccard Similarity Score

Jaccard Similarity is another nice one. If you imagine the documents as sets, it basically measures the overlap (intersection) of the sets as a proportion of the total length of the combined sets (the union). This means however that it will be sensitive to document scale, so it won't be the right metric in all situations:

$$ J(A,B) = \frac{|A \cap B|}{|A \cup B|} $$

Again, this can be converted to a distance metric as above.

## But which to choose?

So now I've presented three metrics (and there are many many more), but which metric should we use? The answer to this question, as to more or less any other metric related question, is it depends. My impression is that cosine similarity is widely used precisely because it is less sensitive to scaling. Jaccard or Euclidean measures may be more appropriate when comparing texts of a fixed length (tweets would be a good example of this).

We can demonstrate this with an example taken from the [DARIAH Text Analysis tutorial](https://de.dariah.eu/tatom/working_with_text.html). Let us take three sentences of varying length, the first two from Jane Austen's Emma (I think) and the last from Wikipedia:

{% highlight python %}
corpus = [
    "Indeed, she had a rather kindly disposition.",
    "The real evils, indeed, of Emma's situation were the power of having rather too much her own way, and a disposition to think a little too well of herself;",
    "The Jaccard distance is a way of measuring the distance from one set to another set."
    ]
{% endhighlight %}

Before we start coding, we should ask, what do we expect the results to be given the content of the sentences? Since the first two documents are prose sentences discussing a woman's personality, my intuition is that they should have a high similarity (or a low distance) while they should be dissimilar to the last sentence, which has a completely different subject. So let's see how it looks when we run the metrics on the corpus.


{% highlight python %}
cv = CountVectorizer(stop_words='english')
X = cv.fit_transform(corpus)

# Euclidean Distance

euclidean_distances(X)
=> array([[ 0.        ,  3.16227766,  3.60555128],
        [ 3.16227766,  0.        ,  4.35889894],
        [ 3.60555128,  4.35889894,  0.        ]])

# Inverse Cosine Similarity

np.set_printoptions(suppress=True)
1 - cosine_similarity(X)
array([[ 0.        ,  0.7763932 ,  1.        ],
        [ 0.7763932 ,  0.        ,  0.90465374],
        [ 1.        ,  0.90465374,  0.        ]])

# Jaccard function jerry-rigged to work on matrices

from sklearn.metrics import jaccard_similarity_score

sim_matrix = np.zeros((3,3))

for idx, doc in enumerate(X):
    scores = [0,0,0]
    for num, other_doc in enumerate(X):
        score = jaccard_similarity_score(doc.toarray()[0], other_doc.toarray()[0])
        scores[num] = score
    sim_matrix[idx] = scores    

print(1 - sim_matrix)
[[ 0.          0.66666667  0.46666667]
 [ 0.66666667  0.          0.86666667]
  [ 0.46666667  0.86666667  0.        ]]
{% endhighlight %}

Comparing the output of these three functions helps to illustrate the issues mentioned above. As we can see, the Euclidean method finds a large distance between the first document and the other documents, with the second document being only slightly closer. The Inverse Cosine method (note its output is normalised to being beween 0 and 1) also shows a large distance, but the second is substantially closer to the first document than the third document. The inverse of the Jaccard index shows the first and second document being further away than the first and last document! This is presumably a consequence of document2 being substantially larger than documents 1 and 3.

Based on this, we can conclude that Cosine Similarity gives the best results for our test problem. In the next post, I will look at applying some of these metrics to a real life political drama. Stay tuned.

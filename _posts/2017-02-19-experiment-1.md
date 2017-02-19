---
layout: post
titles:  "Experiment 1 - Similarity"
date: 2017-02-19 22:40 +0100
---
Based on the techniques described in my previous post, I decided to see if they could be applied to a recent controversy in Danish politics.

In 2016, it emerged that the Danish agricultural lobby organisation (Bæredygtig Landbrug) had paid Copenhagen Business School to write a report on the Danish agricultural sector's comparative advantage (or lack thereof) vis a vis neighbouring countries, focusing on the impact of environmental regulations on competitiveness. While it is not unusual for a lobby organisation to pay for research, what was unusual in this case was that the researcher in question appeared to be in close contact with the organisation and to be actively suppressing data that questioned his findings. The report's conclusions differed substantially from previous research done by COWI (a consulting company) and Copenhagen University. An [article about the case](http://www.dr.dk/nyheder/indland/mails-afsloerer-uvildig-cbs-rapport-kopierede-fra-landbrugslobby) is available at DR in Danish.

With this backdrop, I thought it could be interesting to see if our similarity tools would reveal this orientation in the report, my hypothesis being that it would be more similar to reports published by agricultural lobby organisations than by reports published by other academic institutions.

## Naive Bag O'Words

To test this hypothesis, I retrieved a handful of Danish language reports within roughly the same area. I then converted these to text using Apache Tika, created a term matrix and calculated cosine similarity using tools from scikit-learn. The results were not particularly striking: the report in question is most similar to a report by another agricultural lobby organisation (Landbrug & Fødevarer) but the next most similar report is one of those done by COWI.

<image src="/images/distance1.jpg" />

The image shows these distances projected onto a plane using multidimensional scaling. While a line forms between the lobby organisations' reports and the CBS report on the one hand, it seems frivolous to read too much into this, given for example, that the KU report is almost as close to the LF report as the CBS report is.

## Second effort: Stemming + bigrams

I made an effort at cleaning the results to see if that had an impact on the results. First I stemmed all the words (stemming is normalising a word to its root by removing inflections, for example, *eaten* and *eating* both become *eat* and thus are functionally equivalent for Bag O'Words algorithms) then I created a Document Term Matrix using bigrams instead of single terms. This means that the term matrix was based on groups of two words that were found to be adjacent in the text rather than single words. The result of this process is shown below.

<image src="/images/distance2.jpg" />

As we can see, this echoes the result of the first analysis; the CBS report is not noticeably more similar to the reports from the agricultural lobby group than it is to the other academic articles from COWI and Copenhagen's University.

## Conclusion

My hypothesis that the CBS report would be shown to be more similar to the Landbrug & Fødevarer documents than the KU and COWI reports was contradicted by the evidence. I was a bit disappointed by this result, but I should have expected it. The Cosine Similarity method is based on the words used in the documents: it says nothing about the conclusions of the reports or of putative ideological bias. In this case, good old fashioned journalism outperformed Text Mining, or at least this version of Text Mining.

I also probably need a larger dataset in order to derive meaningful conclusions. Unfortunately, I fear the set of possible documents is quite small given the highly specialised subject matter and the relatively small language base. While there has been a lot written about agriculture in Danish, it is important to get documents with a similar form in order to minimise differences caused by a different register corresponding to the specific document type.

Ah well, learning experience and all that.

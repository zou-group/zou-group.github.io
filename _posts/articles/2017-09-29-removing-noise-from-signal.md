---
layout: page
title: "3 Ways to Remove Noise from Data/Signal"
teaser: "De-noising signal and data is one of the most important problems in data science and electrical engineering. Here, I provide a high-level breakdown of three ways to think about this problem."
header: no
comments: true
categories: article
published: true
---

<center>
<img src="/images/headphones.jpg" style="width:50%"></img>
<small>Removing noise from data is an important first step in machine learning. What can data scientists learn from noise-canceling headphones?</small>
</center>

As data scientists and researchers in machine learning, we usually don't think about how our data is collected. We focus on analysis, not measurement. While that abstraction is useful, it can be dangerous if we're dealing with noisy data. A dirty dataset can be a bottleneck that reduces the quality of the entire analysis pipeline.

In this post, I'm not going to talk about data collection. But I do want to talk about **what to do if you are given a dirty dataset.** Are there any steps you can easily take to improve the quality of your data? I will mention 3 high-level ideas to *denoise* data. As we'll see, each of these methods will have an analogue in signal processing, as electrical engineers have been thinking about this problem for a long time!

## 1. Get More Data

The first and simplest approach that you could do is to ask the person who gave you dirty data to give you *more of it*. Why does having more data help? Won't it also increase the amount of noise we have to deal with? 

Well, to answer that question, let's go back to 1948 when **Claude Shannon discovered a quantitative relationship between communication capacity and the signal-to-noise ratio** of a communication channel -- an equation that started the entire field of information theory. We don't need the exact relationship here, but the proportionality is:

$$ C \propto \log_2{1 + SNR} $$ 

To put it simply, this equation is saying that the better your signal-to-noise ratio ($SNR$), the more information ($C$) you can communicate over the channel per unit time (assuming the bandwidth of your channel is held constant). Why are these two things ($C$ and $SNR$) related? Because, if you have a noisy channel, you can compensate for it by adding **redudancy**: repeatedly send your signal and have the person on the receiving end "vote" to see what your original signal was. 

In other words, suppose you wanted to communicate the digit $0$ to your friend, but when you text him, there's a small chance the $0$ gets "lost" and your friend sees a random digit instead: a $0$ or $1$. So you just text him 5 times:

$${0, 0, 0, 1, 0}$$

That way, he can know to simply take the majority vote, which produces: $0$. Now, there are more complicated schemes as we'll see next, but for our purposes, the interesting thing to note is that our basic voting scheme works even if the noise is _more_than the signal. In other words, even if the probability that a digit is lost is more than 50%, this still works, because whereas noise is random, signal is consistent. So, overall, there will still be more $0$'s  than $1$'s.

This brings us back to machine learning: why does having more dirty data help? Because the noise in the data is random (this isn't always the case; we'll relax this assumption in Point #3), having more data will cause the effects of noise to start canceling each other out, while the effect of the signal will start adding up.

## 2. Project Your Data Onto a Basis Where Your Signal is Structured

Can you do better? Well it turns out that often times you can, especially if your signal has structure to it. This structure is often times not visible in the original basis that you received your data. But a simple transformation can reveal the structure.

Perhaps the best-known example of this is the application of the **Fourier Transform** to audio signal. If you take a recording of someone speaking, it's going to have all sorts of noise in it because of background sounds. This may not be clear from the raw signal. But if break your signal [down by frequencies)](https://en.wikipedia.org/wiki/Fourier_transform), you'll see that most of the audio lies in a few frequencies. The noise, because it's random, will still be spread out across all of the frequencies. This means that we can *filter* out our noise by retaining only the frequencies with lots of signal and removing all other frequencies.

What's the connection with cleaning up data? For this part, it helps to have a  specific dataset in mind: so consider a dataset that consists of $m$ users who have read $n$ books and assigned each one a real-number rating (similar to the [Netflix Challenge](https://en.wikipedia.org/wiki/Netflix_Prize)). This dataset can be succinctly described by a matrix $A \in R^{m \times n}$. Now, suppose this data matrix is heavily corrupted (i.e. by Gaussian noise or by missing entries).

Can we clean this dataset up by finding a better basis to project this data? Yes, an easy way is to use **singular value decomposition** (SVD)! SVD allows us to write rewrite a matrix in terms of a new basis:

$$ A = \Sum_{i=1}^{k} s_i u_i v^T_i $$

Here, we can think of each $u_i v_i^T$ as a basis (which can be thought of representing some particular characteristic of the books that affects users' ratings, e.g. the 'amount of suspense' or 'whether the characters are animals'), and each $s_i$ (singular value) reflects the contribution of that basis in the decomposition of $A$. Here, $k$ represents the rank of the matrix. However, even if the matrix is full-rank, usually only the first few values of $s_i$ are large. The rest are quite small and can be ignored without significantly changing $A$. (this is the reason [PCA](https://en.wikipedia.org/wiki/Principal_component_analysis) works!)

This is really nice, because if the noise is random, it will still be spread out across all of the bases. That means we can discard all of the bases with low singular values, leaving us with a few bases that the signal is concentrated in, diluting the effect of the noise. [Many teams that did well on the Netflix Challenge](http://sifter.org/simon/journal/20061211.html) used SVD in their algorithms.

# 3. Use a Contrastive Dataset

So far, we have assumed that the noise is random, or at the very least, that it does not have any significant structure in the new bases. But what if it's not the case? Imagine, for example, that you are trying to record audio signal from someone speaking, but there is a jet engine rumbling in the background. 

If you were to take the Fourier Transform of the audio signal and only look at the frequencies with the largest values, then you might instead pick out the frequencies corresponding to the jet engine instead of the frequencies corresponding to human speech because the former can in fact be louder than the latter. In this case, it's probably more appropriate to think of the noise as a _confounding signal_ but nevertheless, fundamentally, it represents unwanted signal.

To combat this problem (and make things like noise-canceling headphones possible), electrical engineers have developed _adaptive noise cancellation_, a strategy that uses two signals: the _primary_ signal, which is just the corrupted signal, and a _background_ signal that only contains the noise. The reference is essentially subtracted from the primary to estimate the original signal.

The same kind of approach can be used to clean dirty datasets that contain significant background trends that are not of interest to the researcher. Imagine, for example, that we have gene-expression measurements from cancer patients of different ethnicities and sexes. If we directly apply SVD (or PCA) to this data, the top components or bases will correspond to the demographic variations of the individuals instead of the subtypes of cancer because the genetic variations due to the former are likely to be larger than that of the latter. But if we have a background dataset consisting of healthy patients that also contain the variation associated with demographic differences, but not the variation corresponding to subtypes of cancer, we can search for top components in our primary dataset that our absent from the background.

Our lab has developed a technique to do this analysis, called Contrastive PCA. Check out our paper on Arxiv for more details and experiments: https://arxiv.org/abs/1709.06716
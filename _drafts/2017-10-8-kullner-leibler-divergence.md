---
layout: post
title: Understanding the Kullback-Leibler divergence
author: Ricardo Faúndez-Carrasco
---

Reality is usually far more complex than the models we use to describe it. This means that whenever we make use of a model, unless it is exactly the same as the original system, there will be a net loss of information, meaning that the model will contain less information and will not perfectly reflect all the features and/or properties that it should. Obviously, we would like this loss of information to be as small as possible.

This is particularly true in Statistics and Probability theory, where many times we seek to compare how close two probability distributions (let them be $$p$$ and $$q$$) are from each other. If you have worked on machine learning, you'll probably have seen this situation a lot of times. In fact, what almost every learning algorithm out there is doing is optimizing some sort of distance between probability densities (or distributions). In light of it, we could think of the probability $$p$$ as that corresponding to the physical system we want to model, with $$q$$ a probability distribution we want to make as similar as $$p$$ as it can be. The latter, if our model is properly designed, should get closer and closer to $$p$$ as long as the set of parameters $$\theta$$ that define it are progessively tuned during training time. Thus, assuming that $$p$$ and $$q$$ are probability distributions over $$\mathcal{X}$$ with $$x \in \mathcal{X}$$, we can write them as $$p(x)$$ and $$q(x;\theta)$$, respectively. This notation is the one we shall use along this post.

![Example of the probability distribution of a variable and its fit by a Gaussian](/images/kullback_leibler/gaussian_fit.png){: .center-image }

Just for the sake of clarity, take a look at the picture above. The actual distribution $$p(x)$$ (actually an invented empirical distribution $$\hat{p}(x)$$) is shown as a set of probability masses. The model, defined by $$q(x)$$, is plotted atop of it, as an usual Gaussian distribution. As can plainly be seen, the model approximation and the true distribution clearly differ. The point here is, how we measure how much information are we loosing when we work with $$q(x)$$ instead of $$p(x)$$?

# Preliminar concept: Entropy

In Information Theory, if there's an important concept that must be clear to the read, is that of *Entropy*. It is somehow related to the thermodynamic term, though it's not the same. The entropy $$H$$ here is defined (in the discrete case) by

$$
H(p) = -\sum^N_{i=1} p(x_i)\log p(x_i)
$$

where $$N$$ denotes the number of elements in the probability density. The continuous case is completely analogous:

$$
H(p) = - \int_x p(x)\log(x) dx = - \mathbb{E}_{x \sim p} \log(x)
$$

where $$\mathbb{E}_{x \sim p}$$ denotes the expected value when $$x$$ is sampled from the probability distribution $$p$$. In order to clarify it first, though, let briefly consider the discrete case. In Computer Science, the $$\log p(x_i)$$ is usually $$\log_2 p(x_i)$$, and it is a measure of "minimum amount of bits needed to encode a piece of information". You can think of it as a way to interpret **how much information there's within a system** when extended to a general scheme.

In a similar way, the concept of *cross-entropy* roughly tells us how much information there's within a system when encoded by a probability distribution $$q$$ that is not the "true" one, $$p$$. In the discrete case it is expressed as

$$
H(p,q) = -\sum_x p(x) \log q(x)
$$

which again is easily extendible to the continuous case,

$$
H(p,q) = - \int_x p(x) \log q(x) dx = - \mathbb{E}_{x\sim p}\log q(x)
$$


# Definition of the Kullback-Leibler divergence

With these concepts in mind, we are now in position to properly define the **Kullback-Leibler Divergence**, or as it is usually found in the literature, $$D_{KL}(p\mid \mid q)$$ by:

$$
D_{KL}(p \mid \mid q) = \int_x p(x)\log p(x) dx - \int_x p(x) \log q(x) dx = \int_x p(x) \log \frac{p(x)}{q(x)} dx
$$

which can also be written as

$$
D_{KL}(p \mid \mid q) = \mathbb{E}_{x \sim p} \log p(x) - \mathbb{E}_{x \sim p} \log q(x) = \mathbb{E}_{x \sim p} \left[ \log p(x) - \log q(x) \right]
$$ 

which is easy to see is equivalent to

$$
D_{KL}(p \mid \mid q) = H(p) - H(p,q)
$$

# It is not a metric

# Equivalence with the likelihood minimization

# Relationship with the mutual information
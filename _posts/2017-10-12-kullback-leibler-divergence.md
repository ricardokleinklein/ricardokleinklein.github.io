---
layout: post
title: Understanding the Kullback-Leibler Divergence
author: Ricardo Kleinlein
---

Reality is usually far more complex than the models we use to describe it. This means that whenever we make use of a model, unless it is exactly the same as the original system, there will be a net loss of information, meaning that the model will contain less information and will not perfectly reflect all the features and/or properties that it should. Obviously, we would like this loss of information to be as small as possible.

> All models are wrong, but some are useful. -- George E. P. Box

This is particularly true in Statistics and Probability theory, where many times we seek to compare how close two probability distributions (let them be $$p$$ and $$q$$) are from each other. If you have worked on machine learning, you'll probably have seen this situation a lot of times. In fact, what almost every learning algorithm out there is doing is optimizing some sort of distance between probability densities (or distributions). In light of it, we could think of the probability $$p$$ as that corresponding to the physical system we want to model, with $$q$$ a probability distribution we want to make as similar as $$p$$ as it can be. The latter, if our model is properly designed, should get closer and closer to $$p$$ as long as the set of parameters $$\theta$$ that define it are progessively tuned during training time. Thus, assuming that $$p$$ and $$q$$ are probability distributions over $$\mathcal{X}$$ with $$x \in \mathcal{X}$$, we can write them as $$p(x)$$ and $$q(x;\theta)$$, respectively. This notation is the one we shall use along this post.

![Example of the probability distribution of a variable and its fit by a Gaussian](/images/kullback_leibler/gaussian_fit.png){: .center-image }

Just for the sake of clarity, take a look at the picture above. The actual distribution $$p(x)$$ (actually an invented empirical distribution $$\hat{p}(x)$$) is shown as a set of probability masses. The model, defined by $$q(x)$$, is plotted atop of it, as an usual Gaussian distribution. As can plainly be seen, the model approximation and the true distribution clearly differ. The point here is, how we measure how much information are we loosing when we work with $$q(x)$$ instead of $$p(x)$$?

# Preliminar concept: Entropy

In Information Theory, if there's an important concept that must be clear to the read, is that of Entropy. It is somehow related to the thermodynamic term, though it's not the same. The entropy $$H$$ here is defined (in the discrete case) by

$$ H(p) = -\sum^N_{i=1} p(x_i)\log p(x_i) $$

where $$N$$ denotes the number of elements in the probability density. The continuous case is completely analogous:

$$ H(p) = - \int_x p(x)\log(x) dx = - \mathbb{E}_{x \sim p} \log(x) $$

where $$\mathbb{E}_{x \sim p}$$ denotes the expected value when $$x$$ is sampled from the probability distribution $$p$$. In order to clarify it first, though, let briefly consider the discrete case. In Computer Science, the $$\log p(x_i)$$ is usually $$\log_2 p(x_i)$$, and it is a measure of "minimum amount of bits needed to encode a piece of information". You can think of it as a way to interpret how much information there's within a system when extended to a general scheme.

In a similar way, the concept of cross-entropy roughly tells us how much information there's within a system when it is encoded by a probability distribution $$q$$ that is not the "true" one, $$p$$. In the discrete case it is expressed as

$$ H(p,q) = -\sum_x p(x) \log q(x) $$

which again is easily extendible to the continuous case,

$$ H(p,q) = - \int_x p(x) \log q(x) dx = - \mathbb{E}_{x\sim p}\log q(x) $$

# Definition of the Kullback-Leibler divergence

With these concepts in mind, we are now in position to properly define the Kullback-Leibler Divergence, or as it is usually found in the literature, $$D_{KL}(p\mid \mid q)$$ by:

$$ D_{KL}(p \mid \mid q) = \int_x p(x)\log p(x) dx - \int_x p(x) \log q(x) dx = \int_x p(x) \log \frac{p(x)}{q(x)} dx $$

which can also be written as

$$ D_{KL}(p \mid \mid q) = \mathbb{E}_{x \sim p} \log p(x) - \mathbb{E}_{x \sim p} \log q(x) = \mathbb{E}_{x \sim p} \left[ \log p(x) - \log q(x) \right] $$

which is easy to see is equivalent to

$$ D_{KL}(p \mid \mid q) = H(p) - H(p,q) $$

Thus, $$D_{KL}$$ is but the difference between the entropy of the true probability distribution (this is, the information contained on it) and the cross-entropy of $$p$$ and $$q$$. In short, we are measuring the information lost when using an artificial probability distribution $$q$$ to describe the true probability distribution $$p$$.

# It is not a metric

Because the KL divergence can be expressed as the difference between two numbers, it is said to be a [statistical distance](https://en.wikipedia.org/wiki/Statistical_distance), measuring how far two probability distributions are from each other. It's important to remark, though, that **KL divergence is not a metric**. 

> A metric on a set $$\mathbb{X}$$ is a function $$d: \mathbb{X} \times \mathbb{X} \rightarrow \mathbb{R}^+$$, where $$\mathbb{R}^+$$ is the set of non-negative real numbers such that for all $$x, y, z \in \mathbb{X}$$, $$d$$ satisfies:
1. **Non-negativity**: $$d(x,y) \geq 0$$
2. **Identity of indiscernibles**: $$d(x,y) = 0$$ if and only if $$x = y$$.
3. **Symmetry**: $$d(x,y) = d(y,x)$$
4. **Triangle inequality**: $$d(x,z) \leq d(x,y) + d(y,z)$$

It is easy to see that if both $$p$$ and $$q$$ are well-defined, condition 1 is met. It is also trivial that KL divergence does not satisfy neither condition 3 nor condition 4, and thus it is not a metric. It would make no sense to have a metric such that the distance between two objects depends on which one are we measuring from, and that's what would happen with the KL divergence.

Condition 2 poses a particularly relevant question: as you probably remember, $$D_{KL}(p\mid \mid q) = \int_x p(x) \log \frac{p(x)}{q(x)} dx$$. So, what happens when one of the distributions is equal to zero?

* When $$p(x) = 0$$, the $$\log$$ is not defined, so the KL divergence is no longer a valid measure. It is not defined at one point at least, so the solution that came up time ago and has been used ever since has been to add some noise to the original data distribution, so $$p(x) > 0$$ everywhere.
* If $$q(x) = 0$$, by the formula above, it would be $$p(x) / 0$$ with $$p(x) > 0$$, so $$\log \infty$$, which is also $$\infty$$. In other words, if there's no $$q(x)$$ in a place it was supposed to be, the distance between probability distributions becomes infinity.

KL divergence belongs to a bigger family of statistical distances, the *f-divergences*[^1], which are very well-known since they present many properties that turn out to be extremely useful in a broad range of problems. Not all them are fit to be solved by these divergences, though.

# Equivalence with the likelihood maximization

In this section I am going to focus in the actual application of the KL divergence to machine learning problems. 

Most of our models today are trained following a *maximum likelihood*-guided (ML) learning. This means that the model distribution, which we should remember depends on a set of parameters $$\theta$$, follows a process by which, by means of a tuning of its parameters, tries to imitate the behavior of another function $$p$$, which usually represents the real world data's probability distribution. Actually, what ML does is to *maximize the likelihood of making those real data observations given that set of parameters*.

For the sake of clarity, I shall change a little bit the notation here: I shall denote $$q(x)$$ by $$P_\theta(x)$$, and since in machine learning we do not know in advance the probability distribution of the data $$p(x)$$ but its empirical distribution, which is (assuming it is normalized to the unit):

$$
\hat{P}_D(x) = \sum_{i=1}^N \frac{1}{N}\delta_(x-x_i)
$$

we can state that

$$
D_{KL}(\hat{P}_D(x) \mid \mid P_\theta(x)) = \int_x \hat{P}_D(x) \log \frac{\hat{P}_D(x)}{P_\theta(x)} dx = -H[\hat{P}_D(x)] - \int_x \hat{P}_D(x) \log P_\theta(x) dx
$$

given that the entropy of the probability distribution $$\hat{P}_D(x)$$ does not depend on any $$\theta$$, when deriving the gradient of the above expression with respect to them,

$$
\nabla_\theta(D_{KL}(\hat{P}_D(x) \mid \mid P_\theta(x))) = \nabla_\theta \mathbb{E}_{\hat{P}_D(x)} \log P_\theta(x)
$$

which is

$$
\nabla_\theta \sum_x \hat{P}_D(x) \log P_\theta(x) = \nabla_\theta \sum_x \left[ \frac{1}{N} \sum_{i=1}^N \delta_(x-x_i)   \right] \log P(x \mid \theta)
$$

so then

$$
\nabla_\theta(D_{KL}(\hat{P}_D(x) \mid \mid P_\theta(x))) = \frac{1}{N} \sum_x \nabla_\theta \log P(x \mid \theta)$$

which is exactly the same as the original maximum likelihood criterion (actually, it is the log-likelihood, but it works exactly the same way).

What this fact tells us is that it doesn't matter whether you train your models using a ML-based criterion or a KL divergence-based one, because they both will end up in the same result, although for different reasons.

# A final remark

Here the *Kullback-Leibler divergence* has been introduced, and it has been shown how important it is for any AI or Machine Learning enthusiast to understand its importance. However, KL divergence and other f-divergences are not the only way the distance between two probability distribution can be measured. 

In fact, this topic has attracted a lot of attention lately due to the rising of *Generative Adversarial Networks* (GANs)[^2] and *Variational Autoencoders* (VAEs)[^3], where the way we estimate the difference between two densities is fundamental in the performance of the system. This way, authors have came up with new statistical distances, which are also metrics; I particularly recommend the *Wasserstein GAN*[^4] and the *Cramér GAN*[^5], each of them presenting very appealing properties, though the latter is generally assumed to perform better. I will not talk about them here, but just let say that whereas KL divergence does not take into account the geometry of the densities space, these metrics do, considerably brushing up the potential performance and removing many limitations currently found in many GAN's architectures.

This is a fascinating topic on which I highly recommend a deep study, for it gives you an amazing amount of knowledge on the very roots of how machine learning algorithms do work. And of course, leave a comment with any question or suggestion you have.

# References

[^1]: [Csiszár, I. and Shields, P.C., (2004): "Information Theory and Statistics: A Tutorial", Foundations and Trends in Communications and Information Theory Volume 1 Issue 4, 2004](http://www.renyi.hu/~csiszar/Publications/Information_Theory_and_Statistics:_A_Tutorial.pdf)

[^2]: [Goodfellow, I. (2016): "NIPS 2016 Tutorial: Generative Adversarial Networks". arXiv:1701.00160](https://arxiv.org/abs/1701.00160)

[^3]: [Doersch, C. (2016): "Tutorial on Variational Autoencoders". arXiv:1606.05908](https://arxiv.org/abs/1606.05908)

[^4]: [Arjovsky, M., Chintala, S., & Bottou, L. (2017): "Wasserstein GAN". arXiv:1701.07875](https://arxiv.org/abs/1701.07875)

[^5]: [Bellemare, M. G., Danihelka, I., Dabney, W., et al. (2017): "The Cramer Distance as a Solution to Biased Wasserstein Gradients" arXiv:1705.1074](https://arxiv.org/abs/1705.10743)
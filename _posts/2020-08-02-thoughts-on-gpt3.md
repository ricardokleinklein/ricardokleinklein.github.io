---
title: (Huge) Language Models are Few-Shot Learners
author: Ricardo Kleinlein
layout: post
---

The time when only humans were able to write news from scratch is over.  Although it is true that much of what GPT-3 is able to do is far better than anything we've seen before, not all that glitters is gold.

Given that for the last weeks there's been a great fuss around the excellent, realistic texts that the latest language model from [OpenAI](https://openai.com/blog/), GPT-3[^1], has shown in several applications (a curated list of resources can be found [here](https://github.com/elyase/awesome-gpt3?utm_campaign=The%20Batch&utm_medium=email&_hsmi=92268919&_hsenc=p2ANqtz--kH8K3Y0jTJVGlA2StD6kgVFqg0WYqvyvlAHrIBuDrEh4e5yv1G4tGqt-sZ7pizlgm8IdDAwJr9jSuU1h4rnaNrXannZB94sIKXO-ll5oW-7B9bts&utm_content=92268919&utm_source=hs_email)), I've thought many people might be interested in a deep analysis of the actual contribution of the paper to the field. GPT-3, as well as its predecessor GPT-2[^2], have drown much attention recently due to the potential misuse of automatically-generated fake news, as well as the implicit bias that these models seem to show due to the learning setup. This is a serious issue, since not even human volunteers could tell fake, GPT-3-generated text from real news articles!

Indeed the length, quality and credibility of some of the produced texts are jaw-dropping, but is this model an actual game-changer?  GPT-3 is not but a [Language Model](https://machinelearningmastery.com/statistical-language-modeling-and-neural-language-models/) (LM), a fundamental component of any Natural Language Processing (NLP) system. Such a model provides the probability a word will appear next given its context, which in the case of GPT-3, an autoregressive model, is the sequence of previous words in the sentence or paragraph.

![Autoregressive and bidirectional model's diagram](/images/gpt3/autoregressive.png){: .center-image }

Whereas bidirectional models make predictions based both on future and past tokens, autoregressive ones take advantage of previous predictions from the model itself.

# More should be better?

Donald Hebb, a pioneer in machine intelligence, defined connectionism in the 40s as: 

> A set of approaches in the fields of artificial intelligence, cognitive psychology, cognitive science, neuroscience, and philosophy of mind, that models mental or behavioral phenomena as the emergent processes of interconnected networks of simple units.[^3]

The aim of the authors is to test the hypothesis by which the **larger the number of trainable parameters in the LM, the more information from the corpus is kept independently of the actual training objective**. And that is one of the key points of this paper: all the experiments are devoted to the size of the model, keeping the architecture the same as in GPT-2. 

We already know that if trained over huge amounts of text, a sufficiently large LM will act as an unsupervised learner, so it learns to complete sentences, or if different languages are mixed, translate between them [^2] without explicit instructions to do so. This phenomenon is not new, yet the range at which it happens is still unknown, and GPT-3 represents another step forward. With 175 billion parameters and trained over the equivalent to reading a book a day for 11000 years, GPT-3 is the largest LM ever. 

Because of the size of the corpus, it is inevitable to have repeated samples between train and test splits (data contamination), and although it doesn't seem to have a dramatic impact on the overall performance, it sure is an important line of research that has a serious impact on some tasks, since the authors suspect ([this post](https://aiweirdness.com/post/621186154843324416/all-your-questions-answered?utm_campaign=The%20Batch&utm_medium=email&_hsmi=92268919&_hsenc=p2ANqtz-9B1lGDEgvZnbZziZ6Bikj3EBJQVczilBexKEcpJc68_Y4jHbvwhysLynUBCL9X8IF-f0O80IP8ZHySbfsaLRNDVXau70D7tmydhr4Akfel3NSJ9kg&utm_content=92268919&utm_source=hs_email) deepens in this issue) that sometimes the model generates answers based on repetitions of previous patterns rather than actual semantic/grammar inference. The results are remarkably good (close to or outperforming State-Of-The-Art in several NLP typical benchmarks) given that the model is not specifically trained for any task, showing an impressive capacity to quickly learn patterns in a weakly supervised way.

## We celebrate

Deep Learning models, and Language Models in particular, are incredibly expensive to train, and at the time of writing this, it is customary to train them over hundreds of thousands of examples for each desired output class. Having a Language Model with such a degree of adaptability that allows it to successfully approach almost any text-based problem after seeing just a few previous instances is an inspiring achievement, and opens the door to many interesting applications. Automatic generation of realistic, human-like tweets/news articles/computer code may find applicability in so many new fields it is even overwhelming to think about it!

# Future work

Size matters. Only the largest version of GPT-3 is able to successfully deliver on all the tasks, and only at a prohibitive expense of resources and time, far from the usual capacity of average IT departments or even research laboratories. Distillation [^4] by itself doesn't seem a promising path to downscale models like this. Even though anyone can request access to their API (link [here](https://forms.office.com/Pages/ResponsePage.aspx?id=VsqMpNrmTkioFJyEllK8s0v5E5gdyQhOuZCXNuMR8i1UQjFWVTVUVEpGNkg3U1FNRDVVRFg3U0w4Vi4u&utm_campaign=The%20Batch&utm_medium=email&_hsmi=92268919&_hsenc=p2ANqtz-9tsR0IDvmTdRsyasU4YS7HiNxuhocPsvR8ZZDik6K1MCpzRjqRM8u3L-ySTq_Bg1sOCu3FCL3k9uX1HK-xX8ybhboXfrV1SyLos2t3pakG2FOtby8&utm_content=92268919&utm_source=hs_email)), there's an increasing concern within the community that in this race to get the biggest model, we are forgetting other possible approaches to Artificial Intelligence. 

Words Out-Of-Vocabulary (OOV) is another serious issue; take a look at the example below:

![New words fed as input for text synthesis to GPT-3. extracted from the original paper.](/images/gpt3/new_words.png){: .center-image }

Sometimes, humans do not understand a new word at first. And that's fine. People tend to appreciate silence when the speaker does not know about the topic. It is truly amazing GPT-3 can learn to write convincingly about unreal or unseen entities, yet that is precisely its Achilles' heel: GPT-3 will write about anything you ask it for. Literally anything. This issue represents a drawback that needs much research if we want to use GPT-3 or similar models in real-time, interactive scenarios, where typos or other mistakes are common. In such cases, the current model would totally forget about the final goal of the interaction in favour of a new entity that makes no sense in that context.

## Also...

We keep struggling for symbolic reasoning in language modelling, something that is of utmost importance in everyone's daily communication with other people (for instance, in detecting gender/racial bias and/or jokes, not to mention abstract thinking [^5]).  

A line of research suggested by the authors is to expand the contextual learning of the model with other type of data. To that aim, they propose Computer Vision to be a helpful complementary information to that contained in plain text. For example, compare both descriptions of the city of Ani below. The text describes it, that's true. Yet I'm pretty sure that when looking at the picture your mind is building a far more solid representation of what you'd see there. I do hope to see a lot of work in merging modalities to create richer learning environments. 

![Pure text against combined textual and visual information](/images/gpt3/ani.png){: .center-image }

As a side note, evaluating translation between languages with a radically different grammar (EN-CH for instance) or low-resource languages (languages with but a few samples to train on such as most African languages) would be a nice contribution.

# What might be happening inside?

One great thing about neural networks is that they excel as pattern learners, no matter what the patterns are. How come? Common Deep Neural Networks usually have hundreds or thousands of millions of connections between computing nodes (neurons), which are in fact the horsepower of these models (and GPT-3 has 175 billions of such connections!). Every trainable parameter encodes a relationship between two variables, whatever these variables mean. Even in smaller nets, it has been shown that these trainable weights have the ability to automatically learn "task channels", or sets of connections that are devoted to solve specific problems at inference time [^6].  It seems unclear what GPT-3 is actually learning, and fundamental research should be geared towards that issue. 

As a matter of fact, our brain does have different regions specialized in processing different sources of information, and that labor division allows us to efficiently go through our daily lives far more smoothly than if we had to continuously process in a conscious way our breath, digestion or heart rhythm, to name a few. [^7] But is GPT-3 doing that kind of "node clustering" learning? 

We must bear in mind that only the largest GPT-3 was able to perform satisfactorily across experiments, and still it has a hard time trying to infer new problems such as basic mathematical operations. Even though more research is needed to fully understand what is going on within GPT-3, it seems unlikely at this point that the path to more general intelligence passes by larger nets. On the contrary, the age of adding depth to networks might be reaching an end, since not even the latest, absolutely insane huge language models show any hint of abstract reasoning or understanding of word symbolism.



# References

[^1]: [Brown, Tom B., et al. "Language models are few-shot learners." arXiv preprint arXiv:2005.14165 (2020).](https://arxiv.org/abs/2005.14165)

[^2]: [Radford, Alec et al. “Language Models are Unsupervised Multitask Learners.” (2019).](https://d4mucfpksywv.cloudfront.net/better-language-models/language-models.pdf)

[^3]: [Hebb, D. O. (1949). The organization of behavior: A neuropsychological approach. NewYork: Wiley.](https://books.google.es/books?hl=en&lr=&id=uyV5AgAAQBAJ&oi=fnd&pg=PP1&ots=mJuYBpBXTm&sig=jHQAqFZS7U4rX-xKpssxy2_YZV0&redir_esc=y#v=onepage&q&f=false)

[^4]: [Hinton, Geoffrey, Oriol Vinyals, and Jeff Dean. "Distilling the knowledge in a neural network." arXiv preprint arXiv:1503.02531 (2015).](https://arxiv.org/abs/1503.02531)

[^5]: [Demeter, David, and Doug Downey. "Just Add Functions: A Neural-Symbolic Language Model." arXiv preprint arXiv:1912.05421 (2019).](https://arxiv.org/abs/1912.05421)

[^6]: [Yang, G. R. et al. "Task representations in neural networks trained to perform many cognitive tasks." (2019). Nature Neuroscience. doi:10.1038/s41593-018-0310-2](https://www.nature.com/articles/s41593-018-0310-2)

[^7]: [Marcus, E. M. and Jacobson, S. (2003). Integrated Neuroscience: A Clinical Problem Solving Approach.](https://www.springer.com/gp/book/9781402071645)
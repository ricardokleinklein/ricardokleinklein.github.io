---
title: (Huge) Language Models are Few-Shot Learners
author: Ricardo Kleinlein
layout: post
---

The time when only humans were able to write news from scratch is over.  Although it is true that much of what GPT-3 is able to do is far better than anything we've seen before, not all that glitters is gold.

Given that for the last weeks there's been a great fuss around the excellent, realistic texts that the latest language model from [OpenAI](https://openai.com/blog/), GPT-3[^1], has shown in several applications (a curated list of resources can be found [here](https://github.com/elyase/awesome-gpt3?utm_campaign=The%20Batch&utm_medium=email&_hsmi=92268919&_hsenc=p2ANqtz--kH8K3Y0jTJVGlA2StD6kgVFqg0WYqvyvlAHrIBuDrEh4e5yv1G4tGqt-sZ7pizlgm8IdDAwJr9jSuU1h4rnaNrXannZB94sIKXO-ll5oW-7B9bts&utm_content=92268919&utm_source=hs_email)), I've thought many people might be interested in a deep analysis of the actual contribution of the paper to the field. GPT-3, as well as its predecessor GPT-2[^2], have drown much attention recently due to the potential misuse of automatically-generated fake news, as well as the implicit bias that these models seem to show due to the learning setup. This is a serious issue, since as the authors of the original paper report, not even human volunteers could tell fake, GPT-3-generated text from real news articles!

![Results of synthesized news detection by humans](/images/gpt3/results_news.png){: .center-image }

GPT-3 is but a **[Language Model](https://machinelearningmastery.com/statistical-language-modeling-and-neural-language-models/)** (LM), a fundamental component in any Natural Language Processing (NLP) system. **A language model predicts the probability that a given word will appear next in a text**. Word prediction is inferred from the word's context, which usually is a set of nearby words. But GPT-3 does not only predict words: it fills gaps in a paragraph, answers general knowledge questions, can perform simple mathematical operations, and much more!

![Autoregressive and bidirectional model's diagram](/images/gpt3/autoregressive.png){: .center-image }

Whereas bidirectional models like ELMo [^3] make predictions based both on future and past words, autoregressive ones like GPT-3 take advantage of previous predictions made by the model itself.  

# "As (big) as it gets"
Donald Hebb, a pioneer in machine intelligence, defined connectionism in the 40s as: 

> A set of approaches in the fields of artificial intelligence, cognitive psychology, cognitive science, neuroscience, and philosophy of mind, that models mental or behavioral phenomena as the emergent processes of interconnected networks of simple units.[^4]

The aim of the authors is to test the hypothesis by which the **larger the number of trainable parameters in the LM, the more information from the corpus is kept independently of the actual training objective**. And that is one of the key points of this paper: all the experiments are devoted to the size of the model, keeping the architecture the same as in GPT-2 but varying the number of trainable parameters within. 

It is simply unthinkable to run GPT-3 in most University research labs, let alone at home. The biggest GPT-3 model has 175 billion parameters... so the only way to access the model's predictions (and therefore replicate some of the results) is to ask for permission to the API (link [here](https://forms.office.com/Pages/ResponsePage.aspx?id=VsqMpNrmTkioFJyEllK8s0v5E5gdyQhOuZCXNuMR8i1UQjFWVTVUVEpGNkg3U1FNRDVVRFg3U0w4Vi4u&utm_campaign=The%20Batch&utm_medium=email&_hsmi=92268919&_hsenc=p2ANqtz-9tsR0IDvmTdRsyasU4YS7HiNxuhocPsvR8ZZDik6K1MCpzRjqRM8u3L-ySTq_Bg1sOCu3FCL3k9uX1HK-xX8ybhboXfrV1SyLos2t3pakG2FOtby8&utm_content=92268919&utm_source=hs_email)). 

## A great model carries a great thirst for data

We already know that if trained over huge amounts of text, a sufficiently large LM will act as an unsupervised learner, so it learns to complete sentences, or if different languages are mixed, translate between them without explicit instructions to do so [^2]. This phenomenon is not new, yet the range at which it happens is still unknown. Nonetheless, the model has been trained over  400 billion byte-pair-encoded tokens, **roughly equivalent to reading a book every day for 11000 years,** scrapped from the Internet. 

Because of that, it is almost inevitable to have repeated samples between train and test splits (**data contamination**), and although it doesn't seem to have a dramatic impact on the overall performance (figure 4.2 of the paper), it sure is an important line of research that has a serious impact on some tasks. In fact the authors mention it, possibly suspecting ([this post](https://aiweirdness.com/post/621186154843324416/all-your-questions-answered?utm_campaign=The%20Batch&utm_medium=email&_hsmi=92268919&_hsenc=p2ANqtz-9B1lGDEgvZnbZziZ6Bikj3EBJQVczilBexKEcpJc68_Y4jHbvwhysLynUBCL9X8IF-f0O80IP8ZHySbfsaLRNDVXau70D7tmydhr4Akfel3NSJ9kg&utm_content=92268919&utm_source=hs_email) deepens in this issue) that sometimes the model generates answers based on repetitions of previous patterns rather than actual semantic/grammar inference. 

Despite that side effect derived from the corpus collection procedure, apparently the results are remarkably good (close to or outperforming State-Of-The-Art in several NLP typical benchmarks) given that the model is not specifically trained for any task nor fine-tuned, showing an impressive capacity to quickly learn patterns just by conditioning it with few examples.
These sort of general-objective, pre-trained language models are becoming the rule now (an excellent description of the most relevant ones up to now can be found at [Lilian Weng's blog](https://lilianweng.github.io/lil-log/2019/01/31/generalized-language-models.html#bidirectional-language-model)).

# Nothing's free

The results shown in the paper are in some cases mind-blowing. The answer samples, even considering the cherry-picking, are impressive. Yet the bad news is, size matters, and the system prompts are often unrealistic or simply wrong. Only the largest version of GPT-3 is able to successfully deliver, and at a prohibitive expense of resources and time, far from the usual capacity of average IT departments or even most research labs, who would get similar results with smaller models. It seems a shared impression among researchers is that *“GPT-3 often performs like a clever student who hasn't done their reading trying to bullshit their way through an exam. Some well-known facts, some half-truths, and some straight lies, strung together in what first looks like a smooth narrative.”* says Julian Togelius, associate professor of Computer Science and Engineering at the New York University.

The authors suggest to reduce the model size by distillation procedures [^5], which don't look like a promising path to downscale models like these. Condensing GPT-3 into a model 10, 20 times smaller is not going to be able, at least intuitively, to synthesize text much better than other already smaller models. Perhaps it is about time to reconsider if this race for the biggest model is not blinding us from other possible approaches to large-scale language modelling. 

The paper shows some responses from the system being fed inexistent words contextualized by a brief description:

![New words fed as input for text synthesis to GPT-3. extracted from the original paper.](/images/gpt3/new_words.png){: .center-image }

The answers are sensible and require little context, that's true. Something comes to my mind, though; how would the model respond to even poorer descriptions? Or to single words, without further explanations? It seems that GPT-3 is approximating any unknown word to the context it is surrounded with. This problem might be linked to the fact that even though the model has read millions of complete books, it still requires an extremely delicate process of asking the right questions to break the wall and access the right answers. This is in strong contrast to the way our minds work, which continously build mental representations of the world based on previous knowledge, although it may be inaccurate. What's important is that our brain can retrieve that information very quickly and almost effortlessly from just a few hints, not even full descriptions. It is truly amazing GPT-3 can learn to write convincingly about unreal or unseen entities, yet that is precisely its Achilles' heel: GPT-3 will write about anything you ask it for. Literally anything, even if it is totally wrong. This issue represents a drawback that needs to be investigated if we want to use GPT-3 or similar models in real-time, interactive scenarios, where typos or other mistakes are common, not to mention new words. In such cases, I have the feeling that too often the model would forget about the final goal to focus on speaking of the unknown word instead. Therefore, I would love to see experiments regarding how GPT-3 processes invented words with shorter contexts, or how it deals with Out-Of-Vocabulary words [^6].

## Also...

We keep struggling for symbolic reasoning in language modelling, something that is of utmost importance in everyone's daily communication with other people (for instance, in detecting gender/racial bias and/or jokes, not to mention abstract thinking [^7]).  

A line of research suggested by the authors is to expand the contextual learning of the model with other type of data. To that aim, they propose Computer Vision to be a helpful complementary information to that contained in plain text. For example, compare both descriptions of the city of Ani below: 

![Pure text against combined textual and visual information](/images/gpt3/ani.png){: .center-image }

In spite of the text being quite specific about the location and main characteristics of the city of Ani, it is totally unsufficient to build an accurate representation of it in our minds. Nonetheless, the moment you see the attached image, you make a far more realistic mental model of Ani right away. I am pretty sure that multimodality is the path to follow if we really want to design richer learning setups.

Last but not least, evaluating translation between languages with a radically different grammar (EN-CH for instance) or low-resource languages (languages with but a few samples to train on such as most African languages) would be a nice contribution, not only within academia but also with direct practical applications.

# A final concern

One great thing about neural networks is that they excel as pattern learners, no matter what the patterns are. How come? Common Deep Neural Networks usually have hundreds or thousands of millions of connections between computing nodes (neurons), which are in fact the horsepower of these models (and GPT-3 has 175 billions of such connections!). Every trainable parameter encodes a relationship between two variables, whatever these variables mean. Even in smaller nets, it has been shown that these trainable weights have the ability to automatically learn "task channels", or sets of connections that are devoted to solve specific problems at inference time [^8].  It seems unclear what GPT-3 is actually learning, and fundamental research should be geared towards that issue. 

As a matter of fact, our brain does have different regions specialized in processing different sources of information, and that labor division allows us to efficiently go through our daily lives far more smoothly than if we had to continuously process in a conscious way our breath, digestion or heart rhythm, to name a few. [^9] But is GPT-3 doing that kind of "node clustering" learning? 

We must bear in mind that only the largest GPT-3 was able to perform satisfactorily across experiments, and still it has a tough time trying to infer new problems such as basic mathematical operations and it is unclear how it would perform in some of the tasks suggested in these lines. Even though more research is needed to fully understand what is going on within GPT-3, it seems unlikely at this point that the path to more general intelligence will need bigger networks. 

## At the end of the day...

Deep Learning models, and language models in particular, are incredibly expensive to train, and at the time of writing this, it is customary to train them over hundreds of thousands of examples for each desired output class. Having a language model with such a degree of adaptability that allows it to successfully approach almost any text-based problem after seeing just a few previous instances is an inspiring achievement, and opens the door to many interesting applications. Automatic generation of realistic, human-like tweets/news articles/computer code may find applicability in so many new fields it is even overwhelming to think about it!

# References

[^1]: [Brown, Tom B., et al. "Language models are few-shot learners." arXiv preprint arXiv:2005.14165 (2020).](https://arxiv.org/abs/2005.14165)

[^2]: [Radford, Alec et al. “Language Models are Unsupervised Multitask Learners.” (2019).](https://d4mucfpksywv.cloudfront.net/better-language-models/language-models.pdf)

[^3]: [Peters, M.E. et al. "Deep contextualized word representations." (2018) ArXiv, abs/1802.05365.](https://arxiv.org/abs/1802.05365)

[^4]: [Hebb, D. O. (1949). The organization of behavior: A neuropsychological approach. NewYork: Wiley.](https://books.google.es/books?hl=en&lr=&id=uyV5AgAAQBAJ&oi=fnd&pg=PP1&ots=mJuYBpBXTm&sig=jHQAqFZS7U4rX-xKpssxy2_YZV0&redir_esc=y#v=onepage&q&f=false)

[^5]: [Hinton, Geoffrey, Oriol Vinyals, and Jeff Dean. "Distilling the knowledge in a neural network." arXiv preprint arXiv:1503.02531 (2015).](https://arxiv.org/abs/1503.02531)

[^6]: [Coucheiro-Limeres, A. et al. "Attention-Based Word Vector Prediction with LSTMs and its Application to the OOV Problem in ASR." (2019) Proc. Interspeech 2019, 3520-3524, DOI: 10.21437/Interspeech.2019-2347.](https://www.isca-speech.org/archive/Interspeech_2019/abstracts/2347.html)

[^7]: [Demeter, David, and Doug Downey. "Just Add Functions: A Neural-Symbolic Language Model." arXiv preprint arXiv:1912.05421 (2019).](https://arxiv.org/abs/1912.05421)

[^8]: [Yang, G. R. et al. "Task representations in neural networks trained to perform many cognitive tasks." (2019). Nature Neuroscience. doi:10.1038/s41593-018-0310-2](https://www.nature.com/articles/s41593-018-0310-2)

[^9]: [Marcus, E. M. and Jacobson, S. (2003). Integrated Neuroscience: A Clinical Problem Solving Approach.](https://www.springer.com/gp/book/9781402071645)
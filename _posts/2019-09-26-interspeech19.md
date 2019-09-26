---
title: Predicting group-level attention from movie audio tracks
author: Ricardo Kleinlein
layout: post
---

We usually rate the impact of videos in terms of "likes" and "dislikes", but is it the best way to assess the perception you had of it? 
Probably it's not.

**Disclaimer:**
This post is intended to be an informal written version of the oral presentation I recently gave at [Interspeech 2019](https://www.interspeech2019.org/) about our paper,

*R. Kleinlein, C. Luna-Jiménez, J. M. Montero, Z. Callejas, F. Fernández-Martínez, "Predicting Group-Level Skin Attention to Short Movies from Audio-Based LSTM-Mixture of Experts Models". The 20th Annual Conference of the International Speech Communication Association - Interspeech 2019, Paper ID: 2799, Graz, Austria, Sep. 15-19 2019*

I only have good words for the staff and the organization team, who made it all possible and offered us such an enriching experience. 

- Read [here](https://www.isca-speech.org/archive/Interspeech_2019/pdfs/2799.pdf) our paper.
- Check out the samples and the dataset released in [this Github repo](https://github.com/ricardokleinklein/audio_attention).
- Link to the [special session](http://www.empathic-project.eu/index.php/ssinterspeech2019/) we submitted the paper to.
- Find all the other accepted papers in the official [ISCA archive](https://www.isca-speech.org/archive/Interspeech_2019/)


# Outline
1. Biometric signals, and in particular the **electrodermal activity (EDA)**, provide a scientifically-supported alternative to measure the perceived appraisals of an audience when presented with an audiovisual content, contrarily to current approaches that exclusively consider the opinion of the viewers.
2. We can successfully **model the attention payed by a group of people** to a movie just attending to features extracted **from that film's acoustic track**. 

# Origins and physiology of the EDA

Eccrine gland sweating is the primary thermoregulatory mechanism in human beings[^1]. 
By evaporative heat loss, the close to 2-4 million eccrine sweat glands distributed over the surface of the skin regulate the body's inner temperature.
These sweat glands are most numerous in the forehead, palms and soles (about 700 glands/cm$$^2$$), while quite scarce on the back, with less than 70 glands/cm$$^2$$.
Sweat production is regulated by neurons in the hypothalamus[^4] (Follow [this link](https://www.brainfacts.org/3d-brain#intro=true&focus=Brain-limbic_system-hypothalamus) to learn more about the hypothalamus!) meaning that the sweating activity is driven by the Central Nervous System (CNS), and therefore its variations are not dependent on any cognitive process, but rather on automatic physiological responses of the organism to external stimuli.

The sweat is quite salty because of the high concentration of Na$$^+$$ and Cl$$^-$$ ions [^2].
Because of it, any variation of the electrical properties of the skin due to the secretion of sweat by eccrine glands will be related somehow to the activity of the hypothalamus and the CNS. 
That way, we can consider electrical variations of the skin somatic markers of mind expressions, among which we can cite attention to a given stimulus[^3] [^5], understood as *"taking possesion by the mind, in clear and vivid form, of one of what seem several simultaneously possible objects or trains of thought"* [^6].

## How do we measure it?

The electrodermal activity can be measured by placing a pair of electrodes on the skin surface, usually in the palm near fingertips because of the easiness and the high concentration of sweat glands there.
Then, by applying a small current, the resistance presented by the skin between the electrodes can be recorded.
The EDA signal is often treated as the superposition of two components: 

![EDA components description](/images/interspeech2019/eda_components_text.png){: .center-image }

Perhaps the most interesting aspect about the EDA use as a biometric indicator is not only its easiness of use and cheap costs, but the fact that EDA measurements are not biased by the cognitive process of giving an opinion or a score to characterize the subjective perception.
Therefore, **EDA provides a raw insight into the subjective perceived value** of the viewer presented with audiovisual footage.

# Project workflow

There are two clearly distinguishable parts in this exposition:
first, we shall explain the way we built the database used and the required preprocessing steps, so later we can dig into the experiments carried out over that data.

![General pipeline of this section](/images/interspeech2019/pipeline.png){: .center-image }

## Collecting movies and reactions

We gathered 270 people (46.4% of male viewers, 53.6% female) in 12 sessions of about 40 minutes each.
For each session, a series of short movies alternating with commercial advertisements and blank screens were projected.
**Participants were also given satisfaction surveys for each movie**, indicating their degree of satisfaction with the movie watched in terms of *like*, *neutral* and *dislike*.
At the same time, the EDA of every individual was recorded, and integrated with that of the other participants of a session to compute the group-level EDA signal, from which we derive the phasic (emotion) and tonic (attention) components as a continuous function in time.
However, since in this study we focus on the attention, from now onwards only that part of the signal will be considered, and we will refer to it either as EDA or EDL indistinctly.

![Recordings of the attention-EDA at two different sessions](/images/interspeech2019/sessions.png){: .center-image }

Although the group-level attention-EDA may seem spiky, it actually does not change that quickly or abruptly. 
We can split the movies in smaller segments differentiating between segments of the movie for which the rate of change of the group attention increased or parts for which it decreased.
This procedure gives us **537 fragments** extracted from the films, having each of them a **binary label indicating the average rate of change of the attention** of the group as a whole.

![Visual example of a movie segmentation](/images/interspeech2019/segmentation.png){: .center-image }

We noticed that clips denoting an increasing attention tend to be longer on average than those of decreasing attention.
We hypothesize that is may be due to the nature of the movies considered. 
Since they are short clips, their structure is mostly based on concatenating parts building the narrative followed by abrupt twists, after which the attention decays rapidly.

## From audio waveform to semantic embeddings

It would be natural to seek an explanation of the impact of a film attending to its script.
Even though a combination of Automatic Speech Recognition (ASR) and Natural Language Processing (NLP) techniques is likely to do nicely here, we seek a far looser audio processing pipeline.
In particular, we look for **dense vector representations that implicitly contain some sort of semantic interpretation**.
Google released in 2017 a model capable of turning log-mel spectrograms to audio embeddings of 128-D. 
This model, named VGGish[^8], is a variant of the original VGG model[^9]; last convolutional block is removed, and instead of a 1000-D fully-connected layer a 128-D embedding layer is trained at the end of the model.
The architecture of the model is shown here:

![VGGish model](/images/interspeech2019/vggish.png){: .center-image }

The model released is trained over [**AudioSet**](https://research.google.com/audioset/)[^10], a large collection of more than 2 million Youtube clips of about 10 seconds duration.
The original released model is trained to perform audio event classification.
The ontology built for that purpose is rich in semantic relationships between classes, since it is designed as a hierarchy of more than 500 different categories.
This hierarchy links together audio events such as clapping, respiration sounds and human speech within the "Human Sounds" general class, yet they all have different representations.
Thus, a model trained to generate embeddings from such a dataset is able to distinguish between a broad range of sounds that we would expect to hear in a movie (musical instruments, human speech, traffic, nature sounds...).

# Experiments

I shall introduce the models used first, in order to fully understand the results presented later.

## Predictive model and Mixture-of-Experts

The basic architecture of our predictive models is as follows:

![Architecture of the models](/images/interspeech2019/model.png){: .center-image }

It is essentially a sequential model that goes through the array of embeddings, outputting a binary response of either increasing or decreasing attention.
The idea behind it is to progressively learn the dynamics of the aural embeddings along time. 
This structure constitutes our **canonical** model.
With it, we shall train standard models, meaning that they will see all the training data available in training time, contrarily to **expert models** that only see smaller splits of the data space.

![MEX approaches considered - genre and satisfaction](/images/interspeech2019/mex.png){: .center-image }

A fundamental issue when considering experts is that all data partitions, and consequently the training of the experts, must be homogeneous, so all the models get to see a fairly similar amount of training samples. 
In order to decide the data partitions, we look at additional sources of information we have access to:
- **The genre of the movie the clip belongs to**. Attending to the amount of clips of each genre and the total cumulative time, we distinguish between *comedy-drama-other*.
- **Satisfaction surveys**. Every participant provided us with their overall opinion on the movies watched. Therefore we can tell films from the average opinion viewers had of it (*like-neutral-dislike*). The exact distribution among experts is further explored in the paper. 

## A) Comparing the overall performance

For the sake of a fair assessment, we compare our results to those we earlier published on the same task, in which a logistic regression based on clip-level describable functionals was used instead of neural models[^11] [^12].
These functionals consider explainable features of the clips, such as intensity or fundamental frequency in the case of audio, or pixel entropy and saliency when examining visual features.
The best results were found with a combination of both aural and visual functionals.

![Comparison of the overall performance of the models considered](/images/interspeech2019/comparison.png){: .center-image }

We considered two different sets of experiments:  we shall focus on the accuracy achieved by canonical models before turning to MEX approaches.

### Canonical models

Althought the architecture of the models is always the same, as well as the hyperparameters used in training time, we experimented changing the width of the hidden vector of the LSTM cells.
Neural networks are so widely used because they learn robust representations of the input data distribution. 
That ability to extract robust information is what makes them so useful in so many contexts, yet one should be aware that there may be situations in which we oversize these neural networks, with the consequent waste of energy and time consumption.
We experimented with hidden layer sizes of 8, 16, 32, 64, 128, 256 and 512, finding that **performance significantly dropped for layer sizes outside the range 64-256**. 
Within this range, no significant difference in performance was observed.

There are, nonetheless, two results we should highlight here:
1. **Canonical models trained in loose representations of the acoustic signal alone perform significantly better than functionals that work only on aural features.**
2. What's more, **canonical models accomplish rates of accuracy comparable to those of LR based on visual features**, being vision the main source of information in human beings.

### Mixture-of-Experts

Although it seems that MEX strategies may reach far higher rates of accuracy than any other approach, due to the limited amount of data available we cannot confirm it with statistical significance.
Instead, we shall just point out that **with MEX we get rates that are at least as good as LR based on both aural and visual descriptors**.
That is indeed a good result by itself, given the huge amount of information (comparatively speaking) our network is missing.
Do not forget that the embeddings is all that the network has access to, and these vectors are generated every second of audio signal, condensing in just 128 numbers everything that's happening in the scene.

## B) Ablation study

The table below shows the detailed study of the performance of each model over every data partition considered.
This means that the expert model in comedy films has been tested over all the available data, as well as the test data corresponding to comedy, drama, and other film genres separately.

![Ablation study proving the learning of the experts](/images/interspeech2019/ablation.png){: .center-image }

As expected, not even one of the experts obtain worse classification rates than the canonical it is train from. 
On the contrary, **all of them happen to experience slight improvements over their respective data partitions** (again, due to the limited size of the database, not significantly relevant). 
This observation paves the way to further explore this sort of promising ensemble models that make use of information contained implicitly in the data, such as the movie genre or the opinion of the viewer. 
In the future, we plan to extend this analysis considering new sources of information, such as the interaction of Youtube users with the list of movies here considered.

# What we've learnt

At this point I'd like to enumerate the main conclusions we can extract from this study:

1. Biometric measurements are highly informative in terms of one's physiological and psychological state.
2. Amidst them, the electrodermal activity is a reliable, extremely easy to use indicator of some important psychophysiological reactions such as the attention motivated by external stimuli.
3. It is possible to succesfully model the EDA of an audience and use it to assess the attention they will pay to topic-free movies.
4. Although analyzing all the films with the same recurrent model yields satisfactory results, building experts that operate only over particular subsets of the data seems to be a promising alternative.

Any further ideas or questions are most welcome, please reach me out in the comments section below!

# References

[^1]: [Benarroch EE (2007). Thermoregulation: recent concepts and remaining questions. Neurology 69: 1293 --1297](https://www.ncbi.nlm.nih.gov/pubmed/17875917)

[^2]: [Sodium ion concentration vs. sweat rate relationship in humans Michael J. Buono, Kimberly D. Ball, and Fred W. Kolkhorst Journal of Applied Physiology 2007 103:3, 990-994](https://www.physiology.org/doi/full/10.1152/japplphysiol.00015.2007)

[^3]: [M. Dawson, A. Schell, and D. Filion, “The electrodermal system,” in Handbook of Psychophysiology, 01 2000, pp. 200–223.](https://www.cambridge.org/core/books/handbook-of-psychophysiology/electrodermal-system/03A88C61EE9A6ACCE3417DBB90CDC05C)

[^4]: [W. Boucsein, Electrodermal activity. Springer Science & Business Media, 2012.](https://www.springer.com/us/book/9781461411253)

[^5]: [H. Sequeira, P. Hot, L. Silvert, and S. Delplanque, “Electrical autonomic correlates of emotion,” International Journal of Psychophysiology, vol. 71, pp. 50–56, 01 2009.](https://www.ncbi.nlm.nih.gov/pubmed/18723054)

[^6]: [W. James, The pinciples of psychology. New York; Dover, 1890.](http://library.manipaldubai.com/DL/the_principles_of_psychology_vol_I.pdf)

[^7]: [M. Dawson and K. Nuechterlein. 1984. Psychophysiological Dysfunctions in the Developmental Course of Schizophrenic Disorders. *Schizophrenia Bulletin* 10, 2 (1984), 204-232](https://www.researchgate.net/publication/16470373_Dawson_ME_Nuechterlein_KH_Psychophysiological_dysfunctions_in_the_developmental_course_of_schizophrenic_disorders_Schizophr_Bull_10_204-232)

[^8]: [S. Hershey, S. Chaudhuri, D. P. W. Ellis, J. F. Gemmeke, A. Jansen, C. Moore, M. Plakal, D. Platt, R. A. Saurous, B. Seybold, M. Slaney, R. Weiss, and K. Wilson, “Cnn architectures for large-scale audio classification,” in International Conference on Acoustics, Speech and Signal Processing (ICASSP), 2017.](https://arxiv.org/abs/1609.09430)

[^9]: [Simonyan, Karen, and Andrew Zisserman. "Very deep convolutional networks for large-scale image recognition." arXiv preprint arXiv:1409.1556 (2014).]()

[^10]: [J. F. Gemmeke, D. P. W. Ellis, D. Freedman, A. Jansen, W. Lawrence, R. C. Moore, M. Plakal, and M. Ritter, “Audio set: An ontology and human-labeled dataset for audio events,” in Proc. IEEE ICASSP 2017, New Orleans, LA, 2017.](https://ai.google/research/pubs/pub45857)

[^11]: [A. Hernandez-Garcia, F. Fernandez-Martinez, and F. Diıaz-de Maria, “Emotion and attention: Predicting electrodermal activitythrough video visual descriptors,” in Proceedings of the International Conference on Web Intelligence, ser. WI ’17. New York, NY, USA: ACM, 2017, pp. 914–923.](https://www.researchgate.net/publication/318653700_Emotion_and_attention_predicting_electrodermal_activity_through_video_visual_descriptors)

[^12]: [A. Garcia-Faura, A. Hernandez-Garcia, F. Fernandez-Martinez, F. Diaz-de Maria, and R. San-Segundo, “Emotion and attention: Audiovisual models for group-level skin response recognition in short movies,” Web Intelligence and Agent Systems, vol. 17, pp. 29–40, 02 2019](https://www.researchgate.net/publication/331286760_Emotion_and_attention_Audiovisual_models_for_group-level_skin_response_recognition_in_short_movies)
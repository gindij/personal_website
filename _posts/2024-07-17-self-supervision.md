---
title: TBD
date: 2024-07-17
tags: [machine-learning]     # TAG names should always be lowercase
use_math: true
---

# Introduction
Many of the most powerful models you see out there today are built using a two-stage approach:
1. Pre-training: A model learns generally useful representations (e.g., of language or images) without reference to any particular task (e.g., sentiment analysis or image classification).
2. Fine-tuning: Using the pre-trained model as a starting point, we fine-tune the model to accomplish some additional objective, such as safety or the ability to perform some downstream task.

In theory, the benefit of this approach is that once you have a powerful pre-trained model, you can build many different task-specific and/or fine-tuned models on top of it with relatively little additional effort. But this benefit is only realized if our pre-trained model is very general and has learned useful features of language or images, which seems like it would require TONS of diverse, carefully curated data. From a machine learning perspective, this presents some thorny questions:
1. It's one thing to train a model for something specific, like classifying X-ray images of tissue as cancerous or not, but how do we set up a training objective that helps a model learn... well... very general and woefully underspecified "useful features of language or images"?
2. The most widely used and successful traditional machine learning approaches have in the past required labeled data, but if we want to unlock the use of larger and larger datasets for training, obtaining sufficient high-quality labels can be prohibitively expensive, if it's possible at all. Can we accomplish our pre-training without hand-labeled data?
3. Assuming we could get whatever quantity of labeled data we needed, what kinds of labels would be useful in gaining a general understanding of language or visual data?

To understand the significance of self-supervision, let's first revisit the foundations of fully supervised learning. This will set the stage for appreciating how self-supervised approaches build upon and extend these principles to overcome the challenges of labeled data scarcity.

# Supervised learning
From thirty-five thousand feet, supervised learning works like this: We have a dataset of $(x, y)$ pairs, where the $x$s are called examples, and the $y$s are called labels. For instance, $x$s might be images and $y$ might be a label that is 1 if the image contains a cat and 0 otherwise. In a supervised setting, during training, the model makes a cat/non-cat prediction for an image $x$, and then that prediction is checked against the label $y$. The closer the prediction -- which is usually a probabilistic score rather than binary -- is to the label, the less our parameters need to move in response to this $(x, y)$ pair.

The key here is that for the above to work, we need $y$s! As noted earlier, depending on the problem, labels are very expensive to generate. As an example, for object recognition tasks, we need to label every object of interest and put a bounding box around it... across hundreds of thousands or millions of images! This is both time consuming and hard to do!

To make the jump over to self-supervision, we need to come up with a useful objective or task for the model to learn that only requires $x$s. As a first example of how we can generate a training signal from unlabeled data, let's talk about autoencoders.

# Autoencoders
An autoencoder is a compressive architecture. That is, we use autoencoders to take some complex object and turn it into a (relatively) low-dimensional vector of numbers (an embedding) that is neural-network compatible. Assuming they capture the relevant information from the object, these representations can then be used for downstream tasks like image classification.

We do this compression in the following way. Let's say that the object you want to learn a representation of is called $x$. The autoencoder has two components:
1. An encoder $E$: maps from our high-dimensional input space into our embedding space.
2. A decoder $D$: this piece maps from the embedding space back into the input space.

During training, the model is shown many examples $x$. For each one, it computes $e = E(x)$, and then $x' = D(e)$. (Here, $e$ is what we call the *embedding* of the object.) With autoencoders, we use the original $x$ as our label! The way we determine whether or not our embedding $e$ is good is by whether it contains enough information to be re-expanded back into $x$! Mathematically, our representation is good if $\text{distance}(x, x')$ is small.

<img src="autoencoder.png" alt="drawing" width="550" height="300"/>

This is our first example of actually manufacturing a supervised problem from unlabeled data by cleverly defining our objective. In this case, we define our training objective to be that our reproductions of inputs (images, for instance) should be as close as possible to the original inputs themselves. By using this training task, we obviate the need for labels, but are nonetheless able to learn something useful.

Next, we'll look at a pair of training tasks used in conjunction to train BERT, a precursor to many of the large language models available today.

# Masked language modeling (MLM) and next sentence prediction (NSP)
In 2018, Google released a now-famous paper detailing a transformer-based language model called [BERT](https://arxiv.org/pdf/1810.04805). What is interesting about it for our purposes is the authors' choice of training tasks. Here, again, as we'll discuss, they found ways to generate a helpful training signal from the raw data itself, which in this case is text data. We will discuss each of their two tasks in turn: masked language modeling and next sentence prediction.

## Masked language modeling (MLM)
For this task, given a piece of text like
> The quick brown fox jumped over the lazy dog.

we randomly mask a small fraction of the tokens to obtain something like
> The quick **[MASK]** fox jumped over the **[MASK]** dog.

After doing its best to take the surrounding context into account, the model makes a prediction about which tokens the [MASK] tokens obscure. To be a little bit more precise, a prediction here is not just a single token like `monkey` or `brown`; it is actually a probability distribution over all possible tokens. In other words, for the first [MASK] token, the model might output something like

| Word      | Probability |
|---------- | ----------- |
| brown     | 0.07        |
| monkey    | 0.002       |
| excavator | 0.05        |
| ...       | ...         |


(In reality, the vocabulary size over which the distribution is constructed is much larger than 3; it is often on the order of 10s or 100s of thousands.)

To quantify how well we're doing at any point during training, we look at the distance between the output distribution (like the one in the table) to the "correct" distribution, where the token that was actually masked is assigned probability 1 and all other tokens are assigned probability 0.

As training progresses, with enough diverse data, the model will gradually produce spikier distributions, i.e., distributions where the probability of the right answer gets closer and closer to 1, and all other options tend to 0. Further along in training, the distribution I wrote just above might be something like:

| Word      | Probability |
|---------- | ----------- |
| brown     | 0.7         |
| monkey    | 0.0001      |
| excavator | 0.025       |
| ...       | ...         |

The BERT authors use this as one of a pair of tasks that help the model learn nontrivial statistical properties of the unlabeled training text. While masked language modeling helps the model grasp contextual relationships within a sentence, understanding the relationship between different sentences is equally crucial. This brings us to the next task used in BERT's training: next sentence prediction.

## Next sentence prediction
In addition to an ability to represent individual words well, the BERT authors also wanted the model's representations to be suited to tasks that depend on an understanding of relationships **between** two pieces of text, which is not captured by the MLM task. To mix in an emphasis on this capability, the authors added another task called next sentence prediction (NSP), which works as follows:
1. Select a sentence A from the training corpus.
2. Select a sentence B from the training corpus. With a probability of 0.5, B is the sentence immediately following A, and with a probability of 0.5, B is some other, randomly selected sentence from the corpus.
3. If B comes after A, this (A, B) pair is labeled with `IsNext`. If B is random, the pair is labeled `NotNext`.

During training, the model tries to learn to predict the correct label for each pair.

(Note for the slightly more technically inclined reader: If you think about how we would actually do this binary classification, it's not so straightforward. What we actually do is we set aside a special token, usually designated [CLS]. Each transformer layer mixes together the representations of the tokens output by the previous layer, so after the input has made its way through those layers, this [CLS] token has contextual information from the actual sentence tokens mixed into its final representation. The embedding of this [CLS] token is then fed to a binary classifier.)

During BERT's training, the authors actually use both MLM and NSP and combine the error signals from each of them to update the model parameters. One interesting thing to note here is that these tasks don't target any concrete feature of language, such as telling the difference between nouns and verbs. Rather, we specify fuzzy concepts like being able to guess words from context and being able to tell when one sentence immediately follows another, and trust that the model will have to learn important language features in order to do those things well.

With both MLM and NSP, we've seen how self-supervision can uncover rich linguistic features. The versatility of self-supervision is also evident in the domain of computer vision, where contrastive learning techniques have proven highly effective. Let's delve into one common contrastive learning training objective and its applications in generating powerful image embeddings.

# Contrastive learning
Suppose that we want to train a model $M$ to produce generic image embeddings. One way we can do this is as follows. First, we produce a triplet of embeddings:
1. Select an image $x_1$ and compute its embedding $e_1 = M(x_1)$ .
2. Apply an augmentation to $x_1$ and compute the embedding of the augmented image $e_1' = M(x_1')$. (There are a wide variety of augmentations we can apply, including crops, rotations, or color inversions.)
3. Choose another random image $x_2$ from the dataset and compute its embedding $e_2 = M(x_2)$.

Contrastive objectives try to push embeddings of similar objects (e.g., an image and its crop) closer together, while pushing embeddings of unrelated objects (e.g., two random images) apart. We can set up an objective to do this using our three embeddings as follows:

<div>
$$
    \mathcal L(e_1, e_1', e_2; M) = \max(\text{distance}(e_1, e_1') - \text{distance}(e_1, e_2) + \alpha, 0)
$$
</div>

Let's have a think about what's going on here. The function $\mathcal L$ takes positive values when
<div>
$$
\text{distance}(e_1, e_1') - \text{distance}(e_1, e_2) > \alpha.
$$
</div>

That is, when the distance between embeddings similar objects is larger than the distance between embeddings of unrelated objects by more than $\alpha$ (we choose $\alpha$), the model needs to be adjusted. We would take that positive loss and make updates to $M$'s parameters proportionally to how much each contributed to the loss. With a large enough dataset of diverse images and enough training, this leads to image embeddings that can be applied to a range of downstream applications and tasks.

# Conclusion
In conclusion, the shift from traditional supervised learning to self-supervision marks a significant evolution in machine learning, offering robust solutions to the challenges of data labeling and generalization. By creatively designing training tasks that derive supervision from the data itself, we can harness the power of large, unlabeled datasets to pre-train models that are then fine-tuned for specific applications. From autoencoders and masked language modeling to next sentence prediction and contrastive learning, each method underscores the potential of self-supervision in developing versatile and powerful models. This paradigm not only streamlines the development process but also paves the way for more adaptable and scalable AI systems, promising broader applicability and enhanced performance across diverse tasks.
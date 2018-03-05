# Machine translation
_Holger Schwenk; FAIR_
_05/03/2018_

## Plan
1. Machine translation: more than 60 years of research
2. Statistical machine translation
3. Deep neural networks: why, when and how. _Shift of paradigm_.
4. The path from neural language odels to fully neural machine translation
5. Conclusion and perspectives

# History of machine translation
1954, IBM: start
1966 ALPAC report: stop
+ triangle of Vauquois: source language $\rightarrow$ target language. Transfer of sytanx, semantic. Need of human to design rules
+ Statistical machine translation. Based on decisions (choice of a word). Source sentence $s$, target sentence $t$. Search for the best translation: $\widehat{t} = \arg \max_t \mathbb{P}(t\mid s) = \arg \max_t \mathbb{P}(s \mid t) \mathbb{P}(t)$. Where $\mathbb{P}(s \mid t)$ is the translation model, and $\mathbb{P}(t)$ is the syntax (+semantic) model.

## Comparison with Speech recognition
$$
\widehat{w} = \arg \max_t \mathbb{P}(x \mid w) \mathbb{P}(w)
$$

where we have different types of variable (w: words and x: phonemes),

## What type of examples do we need
**Parallel data** (bitexts)
Corpus of sentences and their translations (labeled data). Like European Comissions, TED talks, documentation. Other option: comparable corpora (only parts of the text are parallel, in Wikipedia, AFP news).

# SMT

## The Principle of SMT
Based on a table of possible translations (go: aller, partir, ... ; go across: traverser) and a language model. Find the best combination (with the highest score).

## Phrase-based SMT
1. Perform word alignment in both directions
2. Extract phrase (heuristic)
3. Score phrases (relative frequency)
4. Train language and reordering model; combine all models
5. Optimize weights of each model using MERT (?) training.

Step optimized individualy. No global training.

## Translation models

### Reminder
$$
\widehat{t} = \arg \max_t \mathbb{P}(t\mid s)
$$

Direct approach: count co-occurencies $p(t\mid s) = \frac{C(t, s)}{C(t)}$. Impossible since the sentences $s$ and $t4 are too different, data sparsemess. We need to decompose.

### Decomposition of $p(t \mid s)$
Decomposition into words or group of words. Alignment of such groups between $s$ and $t$.

### Word-based models
Product of alignment probabilities term. Example: $\mathbb{P}(small \mid petite) \cdot \mathbb{P}(the \mid la) \cdots$
Need to take into account the context to translate a word: (maison d'arrêt: prison).
The computation of translation $\mathbb{P}(small \mid petite)$ requires an alignment. How to match alignment ?

#### Alignment 1 to 1
#### Alignment 1 to N
#### Alignment N to 1
No symetric. The souce word is aligned to no target word. It's happen quite often: `ne pas -> not`, `will learn -> apprendra`.
#### Alignment with null
Some words can appear without correspondances.
#### Alignment with reordering
Adj + noun in english / noun + adj in french

#### Complicated alignments
Limits of word alignments. Better to align group of words. How to automatically align group of words ?

#### Model IBM1 to IBM5
Maximizing with EM procedure.

### Phrase-based models

#### Step 1
Perform IBM (word) alignments in both directions.

#### Step 2b: mark the intersection of both aligments
#### Step 2c: one tries to add lonely words
You can extend the rectangle if there is no alignment. (if there is no words in lines nor columns outside of the box)
Stop at 7 words max (based on experimental results).

#### Calculation of the phrase-table scores
This give the spoken table in principle of SMT.

#### Decoding
Look in the phrase table for all possible translations. And search for the best path $\widehat{t} = \arg \max_t \mathbb{P}(t \mid s)$. In practice: combine many log-linear models coeffficients are optimzed with `MERT`.

### Evaluation in Machine Translation: BLEU score
Criterion to tell if a model is good or bad.
First idea: human score (ask a human to score the fluency and the quality of a translation). But problem of variability.

_BLEU: Bilingual Evaluation Understudy_
Use of several reference translation of human translators.
Precision = number of words of the hypothesis wich appear in one of the references divided by the number of words in the hypothesis.
Do the same calculation for N-grams to judge sentence (and semantic). The BLEU score is the product of all N-grams score $(N=1..4)$.

Drawbacks:
+ Some problem with 0 score. We usually calculate the **BLEU score for a whole document** (to avoid 0), not a signel sentence.
+ **You cannot use the reference word several time**
+ It's only precision (no recall).  So if we have incomplete translations, the precision won't see it (precision: what words predicted are corrects). **Need to penalize too short hypothesis**. We don't need to penalize too long hypothesis (automaticaly penalized already).
+ The absolute value is difficult to judge. You only have to maximize the BLEU score.

### Incremental Improvements of Phrase-Based SMT (PBSMT)
Do erroneous source sentences in the training data affect the performance of an PBSMT system ?

_Online question 1: A, No, because they only appear rarely. B, No, because PBMST systems are robust to spelling errors. C, Yes, but only when the language of the source sentence is wrong. D, No, because wrong source words won't be used for test sentences. E, Yes, always._
No answer?

# Neural networks in machine translations
In 2016, neural models outperform PBSMT in many language paris at WMT'16.
Paradigm shift since 2014.

## Generalities

### What is the key of the sucess of NN in MT?
+ representation of words and sentences in a continuous space: the network can learn relations between the examples and generalize to unobserved events
+ deep neural networks can learn hierarchical features: the network lean automatically how to extract information, how to modify it and how to produce an output without any human guidance

### Learning hierarchical representations
Speech recognition: `wave -> spectral band -> sound -> phone -> word -> sentence`. End-to-end. No need to optimize intermediate step.
Text processing: `char -> word -> word group -> clause -> sentence -> story`

### What about Deep Neural Networkds in NLP?
+ Operate on a low level representation of the data
+ Use very deep architectures to learn hierarchical representations of the data: how to structure the input? n-grams, syntactical or semantic graphs
+ Structure the network to adapt it to the problem: Recurrent NN (LSTM, GRU) are very popular
+ Trained end-to-end: sentence generation is often ambigious, without unique solution

### Word Embeddings
Learn emneddings in a way that similar words are nearby in that space. The notion of similarity may depend on the application (there is no probably "universal word embedding")

## Continuous Space Language Model (CSLM)
`continuous` is the key here.
Three words. Embedding in the space and compute joint probability of those three words.
You learn word embedding in the same time.
Backprop training and cross-entropy, with random initialization.

## Recurrent Network for LM
Each word in conditioned on all preceding words.
LSTM to avoid gradient vanishing
A special token `NULL_WORD` is used to handle exceptions.
Large context windows allowed.

## Handling Sequences of Words
Generalize NML to NMT: encode the phrase source to some representation, and then decode. The encoder processes the source sentence and creates an compact representation.

## Before Seq2seq
RNN encocder-decoder for SMT, Cho et al.
Encoder: no output layer, no loss function: gradients are back-propagated from the decoder (all through the decoder, the encoder part only have information through the compact representation). It`s a long way!
Almost reach phrase-based Neural network systems.

## Seq2Seq
No flat anymore.
Attention mechanism.

## Neual MT: first conclusions
In princple, the decoder is an LM conditioned on the source sentence.
Long sentences encoded in one high-dimensional vector is tricky, which leads to decreses in performance in BLEU score with sentence length: the encoder doesn't remember the entire sentence (use of **attention mechanism**)

## Attention mechanism

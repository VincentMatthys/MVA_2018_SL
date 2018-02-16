# Identifying, representing and correcting words
_Benoit Sagot_
_12/02/2018_

## Processing textual data
+ macroscopic units: **sentences**
+ microscopic units: **words**

How do we represent words ?
How do we identify words ?

# How do we represent words

## Lexical sparsity
Log(frequency) prop to log(rank)

## Lexicons and thesauruses
meanings are static, limited coverage but allow to encode rich linguistic informations

## Vector representations
+ for question answering
+ for plagiarism detection
+ **to measure word similiarity**: that is the essential point we are looking (both previous questions are related to similiarity)
N.B.: similiarity can change over time

## Distributional models of meaning
_Hypothesis (distributional hypothesis_
Co-occured words are very closed in term of meaning ie two words are similar if they have similar word context

## Different kinds of vector models
1. Sparse vector representations
2. Dense vector representation (because sparse vectors can be problematic)
3. Share intuition
  + embedding in a vector space
  + meaning represented by a vector
  + meaning of a word = word embedding

## Cooccurence matrices
Cooccurence:
+ in the same document
+ in immediate context

Cooccurence matrix: frequency counts of $(word, environment)$ pairs:
+ term-document matrix
+ term-term: how many time word $i$ has word $j$ in his environment.

Two words are similar if they present "closed" vectors.

## Word-word matrices
+ $\approx(500000, 500000)$, very sparse
+ similiarity is measured using the cosine between two vectors (dot product between normalised vectors)
+ size of context windows depends on your goals. Smaller window get syntactic similariy, while larger window get semantic similarity.

## First-order vs. second-order similarity
+ first order co-occurence: what is encoded in co-occurence matrix
+ second order co-occurence: similar neighbours (not encoded directly in the matrix)

# Positive pointwise mutual information

## Problems with raw counts
Most frequent words are not the most meaningful. We would be more interested in a measure more importance to context words.

## Pointwise Mutual information (PMI)

$$
PMI(X, Y) = \log_2 \left(\frac{P(x,y)}{P(x)P(y)}\right)
$$

PMI measures the relative importance with the independent hypothesis (more or less than random: PMI can be positive or negative)

## Positive Pointwise Mutual information (PMI)

For rare words: negative PMI values. We replace negative PMI values by 0 **PPMI** (is a bird more different from a car than from ...)

## Weighting PPMI
Other biais with PPMI. Very rare words have very high PMI values. Artificially give rare words higher counts.
+ replace counts by counts to the power something (lower than 1)
+ laplacian smoothing: add 1 to all counts

$$
P_{\alpha}(c) = \frac{count(c)^\alpha}{\sum_ccount(c)^\alpha}
$$

## Beyond cosine similarity
_not discussed today_
Another way to improve raw counts instead of using PMI or PPMI: replace cosine measure by something else:
+ Jacard instead of cosine
+ Dice over cosine

working on word-document matrices, PPMI is replaced by **tf-idf**:
$$
(tf-idf_D)_{ij} = (tf_D)_{ij} \cdot (idf) \cdots
$$


## Sparse vs. dense vectors
PPMI are sparse. Cosine similarity not optimal: no information is shared between similar words. Many parameters to use (500000 dimensions)
_To reduce the dimensionality_:
+ classical method: singular value decomposition (SVD)
+ side-effect of predictive neural models (one of word2vec approaches)

### Singular value decomposition
$$
M = U \Sigma V^\intercal
$$
$\Sigma$ will identify the most important dimensions

### Latent Semantic Analysis
Removing last dimension of $\Sigma$ (latent dimensions), we get an optimal matrix in term of least square matrices. Keeping the top-k dimensions, we get a k-dimensional vector for each word. In fact, it might help to discard the first dimension (containing _garbage_).

### Does it work better than sparse vectors ?
+ denoising
+ genearlisations
+ fewer weights

# Building dense vectors using neural network

## Prediction-based embeddings
No count anymore. Predictive-wise. Try to predict which word should be at a location given the environment.
Underlying idea: we will train a neural network to perform a given word-based, auxilliary task and extract **intermediate representations** as word representations (word embeddings). **word2vec**

**word2vec**: one hidden layer.
Input and output layers used 1-hot encodding
Two types of predictions in **word2vec**:
+ given a word predict its neighbours: **skip-gram**
+ given a context surround a position, predict the word that should fill this position: **CBoW** (Continuous Bag of Words)

## Focus on skip-gams
Creates a vector for each word. Leans a matrix $W$ whose rows represent each word. Also learns a similar auxiliary matridc $C$ of context vectors (independant of words)

## Negative sampling

You can not extract negative examples. You create it by replacing the context word in such pairs with randomly selected words (randomly selected given their relative proportion: more frequent words will be more frequently selected = unigram distribution). Parameter $k$: proportion of negative examples are built for each positive example.

## Skip-Gram with Negative Sampling (SGNS)
word2vec SGNS is current stat-of-the-art.

## What SGNS learning
$$
W \cdot C \propto_\to M_{PMI}
$$
Why do we use word vectors then, and do no stand with PMI matrix ?

_Online question 1: Why is the main eason that explains that, for a few years now, word2vec's SGNS embedding have been acknowledged as the best word vectors ? Although the underlying maths are the same when d is large and the corpus size is large, SGNS performs better on real-life d's and corpus sizes ; The word2vec implementation of SGNS makes use of additional, technical tricks (that could be back-ported to SVD-like approaches); these technical tricks explain the difference ; Neural networks have become so fashionable, especially when rebranded as “Artificial Intelligence”, that switching to a neural-based approach was inevitable, even if it is not really better per se_
The correct answer is the second one.

## SGNS or SVD
word2vec does not only introduce 2 new algorithms, but also includes and tunes several hyperparameters.

## Dynamic context windows
word2vec weights cooccurence more if it closed to target word

## Context distribution smoothing
About PMI, we talked about adding an exponent to smooth. In word2vec this exponent is tuned and increased the results.

## Comparing algorithms
The impact of hyperparameters is higher than that of algorithms.
Better hyperparameters often has stronger effects than more data.
Neural-based approaches do not always outperform traditional approaches.

## Structure

# Inproving context representation
_skip_
Idea: LSTM encoding the context properly and try to use this to predict the target word.

# Identifyin words and sentences

## What is a sentence ?
Macroscopic segmentation. Can be sometimes problematic if you only rely on dots. So we don't really know what a word is.

## What is a word
+ token: typographic unit. conventional way to segment a sentence. Sequence character containing no separators and no punctuation marks.

_Online question 2: Let us consider the following (real!) french twwet: #CamelCase, TiensEcoreUnTermeQueJeNeConnaissaitPas. How many tokens does it contain ? 2; 3; 4; 5; 6; 12; 14; 15_
Just cut on whitespaces and punctuation signs (# is a punctuation sign, first token). CamelCase is one token... So 5 tokens in total

## Wordforms (or forms)
Worform is a syntactically atomic unit.
Mismatches between tokens and forms:
+ aux = à les (2 words in one token)
+ au fur et à mesure (multiple tokens corresponding to 1 form)
+ multiple tokens corresponding to multiple forms: au fur et à mesure du

## Named entities
Barack Obama: real world object that can be denoted individually. Named entity mentions have specific properties, aprt from their specific, individual denotation: e.g. adresses (subpart of language / local grammar)

## Semantic words
Meaning which can not be understood by taking the meaning of each part: for example _pomme de terre_. It can often ambiguous, for example _il a sorti la pomme de terre_. _machine à laver_ is 3 semantic words but 1 term.

## Noisy tokens

# Spelling correction
+ writing assistance
+ correction of test before NLP

## Tyes of errors
Non-word errors (no such a word in dictionnary)
Real-word errors
Typographical erros

## Edit distance
Levenshtein distance (addition, deletion, substitution)
Damereau-levenshtein distance: levenstein + swap
$80~\%$ of errors are within edit distance 1: easy to find candidate for correction.

## Candidate generation
Levenshtein finite state transducer

## Language model
Include unigram probability
Better: incorporating context, using a bi-gram model

# Practical assignment 3
**context2vec** as a language model. Predict which word should be used given the context. Generate candidate corrections by edit distance (only based on edit distance). Combine both in order to create spelling corrector.

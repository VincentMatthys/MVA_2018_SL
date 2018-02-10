# Notes

## Notations
+ $\theta$: model parameters
+ $O=(o_1,\cdots, o_t)$: observed speech frames
+ $W=(w_1,\cdots,w_n)$: word transcriptions

## Three problems of speech recognition
1. Likelihood: given parameter $\theta$ and observed $O$, compute $p(O\mid \theta)$
2. Decoding: $W^* = \arg \max_W p(O \mid W, \theta)$
3. Learning: given $O$ and $\theta$, find the best model

# Models

## Markov chain models

## Hidden markov models
_Continuous emission: find the probabilities of MCC features previously presented_

## (weighted) finite state transducers

## Model decomposition
$W^* = \arg \max_W p(O \mid W, \theta)$
$$
p(O \mid W, \theta) = p(O\mid Q) p(Q \mid W) p(W)
$$

### Acoustic model - $p(O \mid Q)$

Importance of silence HMM model (with a backward loop)

### Pronunciation Models - $p(Q \mid W)$
_e.g. prononciations of the word eleven in the state of Pennsylvanie_
Speech is a lot of reduction and variations at the same time.

#### Usign an existing phonetic dictionary (with international phonetic alphabet)
Doesn't recognize new word in corpus, not in the existing dictionary.

#### Using graphemes to phonemes systems
To extend a phonetic dictionary.

#### Phonological rules, hand crafted

#### Phonological rules, data driven

### Language models - $p(W)$

$p(w_1, \cdots, w_n) = p(w_1)p(w_2 \mid w_1)p(w_n \mid w_1, \cdots, w_{n-1})$
**Ngram hypothesis** => (Trigram) $p(w_n \mid w_1, \cdots, w_{n-1}) = p(w_n \mid w_{n-3}, w_{n-2}, w_{n-1})$
The larger the N, the better.
Estimating Ngram models from data count: $p(w_i) = \frac{c_i}{N}$
Unknowns wordss: dirty solution, replace with `<UNK>`
**Unkown Ngrams**: most ngrams have been seen 0 times. This problem grows exponentially with n. Solutions: smoothing (adding a pseudo count).

#### How to know your model is good ?
**Perplexity**: $PP(W) = 2^{H(W)}$
_alternative to Ngram models_
You can train with the eact same corpora.

# Decoding problem
$$
Q^* = \arg \max_Q p(O \mid Q)p( Q\mid W)
$$

## Dynamic programming

Somme old informations

## Computing the distance between words
_e.g. dynamic time warping_
First, **align the sequences**, Then find the minimal path
Initial conditions: 0  infinity. Follow back the arrow to have the minimal path (like in biology).

## Back to HMMs
_The problem is to decode an HMM_
### Decoding:
Viterbi algorithm, but with maxmima:
$$
v_t(j) = \max_{i=1}^N v_{t-1}(i) a_{ij}b_j(a_t)
$$

_Question: Is it possible to use dynamic programming to decode with an RNN language odel ? -Yes, -No, -Don't know. No. There is no simple recurrence relation between $p(x_j+1 \mid x_1,\cdots, x_j)$ and $p(x_j \mid x_1, \cdots, x_{j-1})$_

# Learning: parameter estimation
_EM variation / Baum-Welch algorithm_
$$
\Theta^* = \arg \max_{\Theta}p(O \mid W, \Theta)
$$

# End-to-end speech recognition
_Neil_
`When you decode you know in which HMM you are. You have one HMM / triphone`

## Connectionist temporal classification

Whithout any alignment, and juste from the speech features, reconstruct the transcription.
The most difficult part is the duration of the phonemes, which can be $10~ms$ or $100~ms$. To learn both classication and alignment: `Connectionist Temporal Classification (CTC)`.
The softmax output $Y$: alphabet + apostrophe, space, blanck, special characters.
Output of network Y seen as a graph + temporal graph (from left to right). The network optimizes the valid path from left to right. Valid path = just containing repetitions of characters.

### Decoding
Basic decoding is just to take the most likely character at each step. You pass the input in the network and take the most likely character at each step.
The only CTC criteria is less powerful than what Emmanuel presented before

## Deep acoustic models for end-to-end speech recognition

Recurrent neural network to output the distributions step by step. It exploits the dependencies along the sequence

_Question: Reccurent Neural Networks are good acoustic models as they allow modelling a sequence. However there is one aspect of speech they cannot model, which one is it? Tonal language, Co-articulation, Speaker variations. The answer was co-articulation. RNN can exploid depencies along the sequences, but like HMMs, the dependencies are coming from the past. So we'll use bidirectional recurrent neural network._

Bidirectional RNN: to take into account co-articulation.

Is this end-to-end learning ? No because speech features are hard-coded.

Can we learn the mel-filterbanks ?
Learning filter banks within a deep neural network framework (because mel-filterbanks are only convolutions on a frequency axes).
You don't have speech features you just have spectrogram.
But we don't want the network to be translation invariant. Because the spectogram (3 parts from bottom, very informative, to the stop) is not translation invariant.
N.B.: Deep Speech 2 from Baidu. No speaker normalization because enough variation in speaker in the database.


**Word Error Rate (WER):** how many words the system failed to recognize, in $\%$.

# Ressources:
+ Kaldi:
+ Wav2letter (Torch)

## Limitations of current approaches

### Challenging tyes of speech
+ accented speech. The gap exists when the accent is present.
+ noisy speech. The gap increase with the human performance when there is more noise.

problem: languages without standard orthography

### Need of data: humans vs machines
Fully unsupervised problem when children learn to speak. Learnin with 2 speakers (papa and mama) + robustness

### Threats in speech recoginition systems
+ Adversarial attack. Pixels / Waves minimal perturbations not observable for humans but completly changing the way the network interprets it.
+ Dolphin attack. The opposite. Destroy the voice commands for humans but with the particularity that the system was still able to recognize the voice commmand.

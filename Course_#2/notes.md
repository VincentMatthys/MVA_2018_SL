# Notes


## Introduction : home assistant

1. identify your voice - speaker identification
2. recongnize what you say - speak recognition
3. understand what you say - natural language processing
4. answer you with natural voice

## Outline

1. Anatomy of a speech recognition system
    + Speech and speech Features
    + Acoustic modeling
    + Language modeling
2. Handling vanability in speech
    + Gender
    + Speaker identity
    + Noise

#1. Anatomy of a speech recognition system

## Speech and speech Features

### Microphone records
Waves form: amplitude of difference in air pressure along time - $8Khz$ or $16kHz$

### Word transcription
What the sentance look like when it's written

### Phonetic transcription
This is what we want. Phonetic unit = phoneme. Beacause orthography are not necesseraly linked with the word, while phonemes are.

### Hard to train directly on the waveform
We have to etract **speech features**: representation of a signal that makes learning easir for an algorithm
Desired properties :
1. Local features: waveform highly non stationary signal
2. Spectral features: phonemes are characterized by their spectral frequency domain
3. Compact features: speech signal is high dimensional (8k 16k)

Two types of speech features:
+ mel-filterbanks
+ MFCC

### Windowing
Cut the signal into overlapping windows ($25~ms$ width, $10~ms$ between each window)

### Discrete Fourier Transform
$$
X(\omega) = \sum_{n=0}^{N-1}x_n  \exp (-i2\pi\omega\frac{n}{N})
$$
where $\omega$ frequency.

### Energy
Frequency representation: $|X(\omega)|^2$. Not adapted for phonems + windowing

### Time-frequency representation
Better for speech representation. **Spectogram** (frequency in function of time)
$$
|X(c_k, \omega)|^2 = \left|\sum_{n = -\infty}^{+\infty} x[n]h[n-c_k]exp(-i\omega n)\right|^2
$$
where $h$ is the local window used

### Compacity
We need compactness. From spectogram, we wrap the frequencies to a scale which is linear below $1000~Hz$ and logarithmic above. **Mel-filterbanks**

$$
Melfbank_j(k) = \sum_{\omega = 0}^{256} Spectrogram(k, \omega)Melfilter_j(\omega)
$$
Product from spectogram with triangles. Reduce dimension to $\sim 40$.

To **MFCC**: source-filter model
$$
x[n] = s[n] * v[n]
$$

$s$ controls the source, but we want the speech contained in $v$.
In Fourier domain:

$$
X[\omega] = S[\omega]V[\omega]
$$

$$
|X[\omega]|^2 = |S[\omega]|^2 |V[\omega]|^2
$$

$$
\log(|X[\omega]|^2) = \log(|S[\omega]|^2) \log(|V[\omega]|^2)
$$

### Discrete cosine transform

To separate the source (glottal from the vocal tract)
$$
X_k = \sum_{n=0}^{N-1}x_n \cos(\frac{\pi}{N}(n + \frac{1}{2})k)
$$
We obtain the **cepstrun** which separates the vocal tract (in the first coefficients) from the glottal source

_Question: some language such as Mandarin are tonal: depending on the pitch at which it is pronounced, a phoneme is different:_
_Answer: to have the pitch information, you need the source part. Mel-filterbanks is ok, even if you lost a bit of information. Other methods are possible, using auto-correlation for example._

### Useful software / reading:
1. Praat
2. Kaldi _most important one_
3. HTK _to practice_
4. Speech and Language Processing, Jurafsky and Martin (chapter 9.3)

## Speech recognition as a statistical problem

Find the most likely transcription:
$$
\widehat{W} = \arg \max_W P(W \mid X)
$$
Using a generativ model (**acoustic model** $P(X\mid W)$ + **language model** $P(W)$)
$$
\widehat{W} = \arg \max_W P(X \mid W) P(W)
$$

## Acoustic modeling
Generative speech features according speech content: $P(X \mid W)$.
Condition on sequences of phonemes instead of sequences of words:
$$
P(X \mid W) = \sum_{Q}P(X \mid Q)P(Q \mid W)
$$
where $Q$: valid pronounciation of W and $P(X \mid Q)$ is modeled by a HMM. In states: transcription (phonemes, words ?) and in emission:.

### Word based HMMs
One HMM per word.
Adapted for small vocabulary, like voice commands.
Transition matrices: containing probabilities of transition between words.

### Phone-based HMMs
One HMM per phoneme.
Phonetic inventory smaller than word vocabulary.
Advantage: Lots of exanples for each phoneme to train.
For a given pronounciation, a word is a concatenation of phone HMMs.

But the HMM doesn't model the dependency among phonemes (e.g. phase and face). The phonemes pronounciation depends and the next one: you move your articulators in a energy efficient way. (minimizing the energy consumption).

**One HMM per triphone.** Triphone = phoneme in context. (e.g. K after silence and before Ae). So it's more about the beginning, the middle and the end of a single previous phoneme.
Takes into account co-articulation.

We still have to find the distribution over the features space (emission matrices for a given state / triphone).
1. Multivariate gaussian. Problem: one node and you don't observe one node in speech recognition.
2. Natural extension of gaussian distributions: gaussian mixture = universal approximator:
$$
b_j \sim \sum_{m=1}^M c_{jm} \mathcal{N}(\mu_{j,m}, \Sigma_{jm})
$$

### Algorithms
1. Forward-backward algorithm for scoring: Viterbi-algorithm.
2. **Matching**: find the optimal sequence of states given the features. Find the optimal sequences of phonemes, then of words = transcription.
3. Training: Baum-Welch algorithm to adjust odel parameters.

### Replace gaussian mixture model by neural networks
To predict the state from the feature (different length in particular).
Train with cross-entropy loss.
Replaced by end-to-end training (cf course #3). (No need of alternative training between neural network and hidden markov model)

## Language modelling
_in the next class_

#2. Handling variability in speech

## Variablity in gender
Difference in average frequency of formants (formants: first part of spectogram of a given window)
You have to learn invariance from the data = **balanced dataset** half male, half female.
You can use different scales for males and females in mel-filterbanks, _id est_ use different set of filters for males and females to reduce differenes between genders.

_Question: You build a dataset to train a speech recognition for a home assistant. You have hundreds of hours, made of several speakers' speech. When splitting your training and validation/test set, should you: split it randomly, split it such taht every fidderent voices is in the training set, split it such taht speakiers in the training set are different from the ones on the testing set._
_Answer: System to work on everyone: you have to split your speakers. In that way you can proper evaluate the power of your model on different speakers. You never have the same speaker on the training and the testing sets._

## Variablity in speaker identity

Decades ago, speech recognition system had to fit your voice to learn special characteristics of your voice. Nowadays, any speech recognition system is supposed to work directly with any voice (**speaker independant**).
**Balanced dataset**, as in previous question
**Adding speaker features**, vectors are features that represent the identity of the speaker, regardless of what they say. You give it as an input of your hidden layer. To perform the classification task, the network will remove properly the information of the specifity of the speaker. At evaluation, you remove the identity feature in your architecture because your network has learnt to be invariant to speaker identity.
**Multi-tasking learning**: phonetic classification + predict identity of the speaker. The network will separate the informations in a way that the classification will be more independant of the speaker's identity.

For many applications, the speeh recorded by the microphine is noisy (e.g. GPS): lot of contamination sources. The real conditions of how you would use speech recognition are differents from the ones used to train the home assistants. See far-field speech recognition for example.
**Data augmentation**: if you have access to a training set that is clean, but want to test your system on noisy speech, you can add various noises to your dataset (white noise, natural noises, reverberation). You create an artificial noise dataset which allows you to gain in robustness.

## Variablity in noise

**Denoising algorithms** to make the speech signal very clean. In test time, you pass the signal in a denoising algorithm to remove the noise.



# Conclusion
In this class:
1. Speech and speech Features
2. Modeling speech with HHM
3. Modeling speech features with Gaussian Mixture models

Next class: language modelling, end-to-end speech recogniton, current state-of-the-art and frontiers in speech recognition

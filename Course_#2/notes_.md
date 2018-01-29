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
  
# 1. Anatomy of a speech recognition system
  
  
## Speech and speech Features
  
  
### Microphone records
  
Waves form: amplitude of difference in air pressure along time - <img src="https://latex.codecogs.com/gif.latex?8Khz"/> or <img src="https://latex.codecogs.com/gif.latex?16kHz"/>
  
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
  
Cut the signal into overlapping windows (<img src="https://latex.codecogs.com/gif.latex?25~ms"/> width, <img src="https://latex.codecogs.com/gif.latex?10~ms"/> between each window)
  
### Discrete Fourier Transform
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?X(&#x5C;omega)%20=%20&#x5C;sum_{n=0}^{N-1}x_n%20%20&#x5C;exp%20(-i2&#x5C;pi&#x5C;omega&#x5C;frac{n}{N})"/></p>  
  
where <img src="https://latex.codecogs.com/gif.latex?&#x5C;omega"/> frequency.
  
### Energy
  
Frequency representation: <img src="https://latex.codecogs.com/gif.latex?|X(&#x5C;omega)|^2"/>. Not adapted for phonems + windowing
  
### Time-frequency representation
  
Better for speech representation. **Spectogram** (frequency in function of time)
<p align="center"><img src="https://latex.codecogs.com/gif.latex?|X(c_k,%20&#x5C;omega)|^2%20=%20&#x5C;left|&#x5C;sum_{n%20=%20-&#x5C;infty}^{+&#x5C;infty}%20x[n]h[n-c_k]exp(-i&#x5C;omega%20n)&#x5C;right|^2"/></p>  
  
where <img src="https://latex.codecogs.com/gif.latex?h"/> is the local window used
  
### Compacity
  
We need compactness. From spectogram, we wrap the frequencies to a scale which is linear below <img src="https://latex.codecogs.com/gif.latex?1000~Hz%20and%20logarithmic%20above.%20**Mel-filterbanks**&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?Melfbank_j(k)%20=%20&amp;#x5C;sum_{&amp;#x5C;omega%20=%200}^{256}%20Spectrogram(k,%20&amp;#x5C;omega)Melfilter_j(&amp;#x5C;omega)&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20Product%20from%20spectogram%20with%20triangles.%20Reduce%20dimension%20to"/>\sim 40<img src="https://latex.codecogs.com/gif.latex?.To%20**MFCC**:%20source-filter%20model&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?x[n]%20=%20s[n]%20*%20v[n]&quot;&#x2F;&gt;&lt;&#x2F;p&gt;"/>s<img src="https://latex.codecogs.com/gif.latex?controls%20the%20source,%20but%20we%20want%20the%20speech%20contained%20in"/>v<img src="https://latex.codecogs.com/gif.latex?.In%20Fourier%20domain:&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?X[&amp;#x5C;omega]%20=%20S[&amp;#x5C;omega]V[&amp;#x5C;omega]&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?|X[&amp;#x5C;omega]|^2%20=%20|S[&amp;#x5C;omega]|^2%20|V[&amp;#x5C;omega]|^2&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?&amp;#x5C;log(|X[&amp;#x5C;omega]|^2)%20=%20&amp;#x5C;log(|S[&amp;#x5C;omega]|^2)%20&amp;#x5C;log(|V[&amp;#x5C;omega]|^2)&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20###%20Discrete%20cosine%20transformTo%20separate%20the%20source%20(glottal%20from%20the%20vocal%20tract)&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?X_k%20=%20&amp;#x5C;sum_{n=0}^{N-1}x_n%20&amp;#x5C;cos(&amp;#x5C;frac{&amp;#x5C;pi}{N}(n%20+%20&amp;#x5C;frac{1}{2})k)&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20We%20obtain%20the%20**cepstrun**%20which%20separates%20the%20vocal%20tract%20(in%20the%20first%20coefficients)%20from%20the%20glottal%20source_Question:%20some%20language%20such%20as%20Mandarin%20are%20tonal:%20depending%20on%20the%20pitch%20at%20which%20it%20is%20pronounced,%20a%20phoneme%20is%20different:__Answer:%20to%20have%20the%20pitch%20information,%20you%20need%20the%20source%20part.%20Mel-filterbanks%20is%20ok,%20even%20if%20you%20lost%20a%20bit%20of%20information.%20Other%20methods%20are%20possible,%20using%20auto-correlation%20for%20example._###%20Useful%20software%20&#x2F;%20reading:1.%20Praat2.%20Kaldi%20_most%20important%20one_3.%20HTK%20_to%20practice_4.%20Speech%20and%20Language%20Processing,%20Jurafsky%20and%20Martin%20(chapter%209.3)##%20Speech%20recognition%20as%20a%20statistical%20problemFind%20the%20most%20likely%20transcription:&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?&amp;#x5C;widehat{W}%20=%20&amp;#x5C;arg%20&amp;#x5C;max_W%20P(W%20&amp;#x5C;mid%20X)&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20Using%20a%20generativ%20model%20(**acoustic%20model**"/>P(X\mid W)<img src="https://latex.codecogs.com/gif.latex?+%20**language%20model**"/>P(W)<img src="https://latex.codecogs.com/gif.latex?)&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?&amp;#x5C;widehat{W}%20=%20&amp;#x5C;arg%20&amp;#x5C;max_W%20P(X%20&amp;#x5C;mid%20W)%20P(W)&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20##%20Acoustic%20modelingGenerative%20speech%20features%20according%20speech%20content:"/>P(X \mid W)<img src="https://latex.codecogs.com/gif.latex?.Condition%20on%20sequences%20of%20phonemes%20instead%20of%20sequences%20of%20words:&lt;p%20align=&quot;center&quot;&gt;&lt;img%20src=&quot;https:&#x2F;&#x2F;latex.codecogs.com&#x2F;gif.latex?P(X%20&amp;#x5C;mid%20W)%20=%20&amp;#x5C;sum_{Q}P(X%20&amp;#x5C;mid%20Q)P(Q%20&amp;#x5C;mid%20W)&quot;&#x2F;&gt;&lt;&#x2F;p&gt;%20%20where"/>Q<img src="https://latex.codecogs.com/gif.latex?:%20valid%20pronounciation%20of%20W%20and"/>P(X \mid Q)$ is modeled by a HMM. In states: transcription (phonemes, words ?) and in emission:.
  
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
<p align="center"><img src="https://latex.codecogs.com/gif.latex?b_j%20&#x5C;sim%20&#x5C;sum_{m=1}^M%20c_{jm}%20&#x5C;mathcal{N}(&#x5C;mu_{j,m},%20&#x5C;Sigma_{jm})"/></p>  
  
  
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
  
# 2. Handling variability in speech
  
  
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
  
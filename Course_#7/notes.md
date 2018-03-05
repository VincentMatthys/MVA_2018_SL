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

## 

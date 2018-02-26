# Dependency parsing
_Benoit Sagot_
_26/02/2018_

# Introduction
consituency parsing: US
dependency parsing: Tch.Rep

# Graphe-based parsing
MSTParser (software)

# Arc-standard transition-based parsing
_Algorithm MaltParser_

## Starting point
Read sentence word after word.
Linear with the length of the sentence (CFG - $O(n^3)$)

## Formalising dependency trees
+ $V$ nodes, labelled with wordforms (dummy word ROOT placed at the beginning of the sentence)
+ a set $A$ of arcs, labelled with dependecy types
+ a linear precedence order $<$ on $V$.

## Parser configurations
+ A parser configuration: triple $c=(S,Q,A)$ where:
  + $S$: stack of partially processed nodes
  + $Q$ a queue of remaining input nodes
  + $A$: a set of labelled arcs $(w_l, l, w_j)$
+ Initialisation: $(\{w_0\}, \{w_i\}, \{\})$
+ Terminaison: $(\{w_0\}, \{\}, \{A\})$

## Actions
+ shift
+ left-arc
+ right-arc
Take the right decision for action

## Example transition sequence

## Properties of the algorithm
+ Every transition sequence outputs a projective dependecy tree (**soundness**)
+ Every projective dependecy tree is output by some ttransition sequence (**completeness**)
+ There are exactly $2n$ transitions in a sentence with $n$ words.

## Deterministic parsing
If we have an **oracle** $o(c)$ that correctly predicts the next transition, parsing is deterministic

## Oracles as classifiers
An oracle can be appproximated by a (linear) classifier
$$
o(c) = argmax_t w \cdot f(c,t)
$$

## Deterministic classifier-based parsing
Take the best action at every step. Highly efficient parsing (linear time complexity), but sensitive to errors (and error propagation)

## Comparaison with MSTParser and MaltParser
+ MaltParser more accurate on short dependencies and siambiguation of core grammatical functions (richer but more sensitive with error propagation)
+ NSTParser more accurate on long dependencies and dependencies near the root of the tree

# Beam search and structured prediction

## Beam search
Maintain the $k$ best hypotheses at each step.

_Online question 1: What is the compelxity of transition-based parsing extended with beam search? $O(n)$, $O(n \log n)$, $O(n^2)$. Answer : $O(n)$, still have $2n$ transitions. But at each step, more complex but still dependinc on the linear of the length of the sequence._

## Structured prediction

## Beam size and transing iterations
Order of magnitude: arround $90~\%$ of precision, with $k=8$ or $k=16$. The proportion of sentences for which you get a perfect parse if pretty low, arround $30~\%$.

## The best of two worlds ?
+ Like graph-based dependecy parsing (MSTparser)
+ Like determnistic transition-based parsing (MaltParser)
+ Example: Zpar parser (freely available, **most heavily developed for English and Chinese**)

_How to develop a parser to adapt to a specific language ?_
Words that are not contained in your train corpus but you take into account for the evaluation. Keep it mind that the things that are presented today are purely theoritical, and practical implementation can be much more tricky.

## Even richer feature models

# Online reordering for non-projectivity

## Projectivity
+ A dependency arc is **projective** if he head (transitively) dominates all intervening words
+ Most dependency grammar theories do not assume projectivity (but many parsers do)

_Example : A hearing is scheduled on the issue today_
A hearing on the issue today should be the subject of _scheduled_, but is non continuous.

## Non-projectivity in natural languages
Between $10~\%$ and $20~\%$ of trees in all languages are non projectives. So\ it's quite important to have a way to create non-projective tree.

## Projectivity and word order
Words can always be reordered to make the tree projective.

## Parsing with online reordering
Add a new transition (shift, left-arc, right-arc) for reordering words: **shift**.
Dinamycally, online, reorder words. The parse tree you will get will be projective for a different order of words.

## Example transition sequence
_A hearing is scheduled on the issue today._

## Empirical results
+ **LAS**: labelled attension score. Right structure and right labels
+ **UAS**: unlabelled attention score. Right structure but not right labels.

## Part-of-Speech tagging for parsing
PoS Tag: Noun, Verb, ...

+ Part-of-speech tags in dependency parsing:
  + crucially assumed as input, not predicted by the parser.
+ Joint method for PoS Tagging and parsing

## Parsing with online reordering
Redefine shift transition: **ShiftAndTag** where $p$ is a PoS tag
Our parser will now:
  + tag words
  + sort words
  + create tree

## Empirical results
Small improvements.

_Online question 2: The arc-standard algorithm has several drawbacks. What is this its main drawback? Segmenting the construction of the parse into individual Left-Arc()’s and Right-Arc()’s prevents a correct handling of compound words and idioms ; the emission of Left-Arc()’s must be performed as soon as possible, which results in decisions being sometimes taken without the appropriate information ; the emission of Right-Arc()’s is sometimes postponed, which results in decisions being sometimes taken without the appropriate information. Correct answer is C_

idioms: `casser sa pipe`, complex structure with individual meaning
compound words" `pomme de terre`

# Arc-eager transition-based parsing

## Limitations of the arc-standard algorithm

The arc-standard system considered so far:
+ builds a dependency tree stricly bottom-up
+ a dependecy arc can only be added between two nodes if the dependent node has already found all its dependents
+ As a consequences, it is often necessary to postpone the attachment of right dependens

## The problem of arc-standard on an example
_La température a un très gros effet sur la concentration_
We may have extracted from the tree bank that _effet_ must indicate _sur_. We had to eat the all sentence before creating the right-arc between _a_ and _effet_.

## The arc-eager system
+ We will modify the basic set of actions in order to always add an arc at the earliest possible opportunity.
+ Shift remains the same
+ **Left-arc is rewritten and subjected to a stricter condition**
+ **Right-arc is changed**, it does not discard $w_i$ anymore. We will decompose right-arc into 2 actions: creating the arc and pospotne the reduction of $w_i$ to another new action
+ **Reduction**

## Drawbacks of the arc-eager algorithm
+ The arc-eager system has a weaker soundness result than the arc-standard system. Not guaranty to have a $2n$. Bounded by $3n$.
+ it does not guarantee the output to be a dependency tree, only a sequence of (unconnected) trees (a forest). If it happens, just chose one of the root of one of the different trees you have created and attach it to other trees.

# Extending arc-eager: the case of disfluencies
_deal with specific phenomena_

## Drawbacks of the arc-eager algorithm
Two issues for spoken language processing:
+ ASR errors
+ Speech Disfluencies:
  + $10~\%$ of the words in conversational speech are disfluent
  + an extreme case of noisy input (_cf video_).
+ Error propagation from these two errors can wreak results.

## Disfluencies
Three types:
+ filled pauses (FP): e.g. uh, um
+ discourse markers and parentheticals (DP): e.g. I meanm you know
+ Reparandum (edited phrase): e.g _I want a flight to Boston (reparandum) uh (FP) I mean (DP) to Denver (reparir)_

## Processing disfluent sentences
Firt edit the sentence, and then parse it. However it is not necessary the best option. Using the joint architecture, disfluency detection and parsing.

# Syntax and formal grammars
_Benoît Sagot_
_19/02/2018_

## Syntax
Branch of linguistics that studies the structural properties of sentences.
How words organized themself into sentences? It has no direct connection with meaning.

### Syntax and NLP
Crossroads between:
+ _syntax per se_: introspection, corpus studies (take a look with different statistics on it to extract pattern), psycholinguistics / neurolinguistics (ask questions to people and establish survey to different stimuli / try to understand what is happening in the brain while processing language)
+ formal grammars: what formal devices do we need to represent such structures?
+ language resources: treebanks (collection of sentences for which syntactic structures has been manualy added) ; syntactic lexicons (ressource that contains a lot of ressources about the syntactic properties of wordforms)

## Parsing
To build the syntactic structure of sentence is called parsing.
It's a key step in NLP systems. Parsing will be the topic of the next class and today's assignment

# Syntactic structures

## Structures in trees

### A first version Constituent structure
Underlying idea: sequnces of words belonging together form **constituents** in a **hierarchical way**. Forming trees

### How do we label the nodes?
_((the) boy) likes ((a) girl)_
build constituents so each one has exactly one non-bracketed word, called its head.
((the) boy): noun phrase, _boy_ being the head of _the_, and beeing a noun.
**PoS tagging**: clustering task

### Constituency trees
some limitations
More useful to insert PoS as immediate ancestor of leaf node
**Lexical anchors**: special connection of _the boy likes a girl_ to the trees.

### Dependencies
Underlying idea: each word is governe by another word, except for the main word of the sentence. A link between a word and its governor is a dependency. Such links can be labelled with dependency types.
This is a dependency tree.

## Dependencies Vs constituents
Dependency to constituency: mising label for internal nodes
Constituency to dependency: mising the head information for each constituent.

### Constituency trees: specifying heads
+ With two types of edges.
+ Head percolation table: specifying rules (NP (DET N*): noun will be the head of this constituent ; S(... V\* ...) V will be the head of the constituent)

## Key observation
Dependency structures look like semantic structures
Constituency structures are still very important, especially for configurational languages such as English where word orders matters (**fixed structures**)

## Non-projective dependencies
_une fille est entrée qui portant un chapeau_
lot of crossing edges in constituency tree

## Control, raising and attribution
_Pierre veut dormir_
Non-tree case: _Pierre_ is sub of _veut_ which as _dormir_ as object. But _dormir_ has _Pierre_ as deep-subject.

## Overall objective
Find a formal device to build constituency tree and semantic like structure. We'll limit ourselves to projectivem tree-like tree

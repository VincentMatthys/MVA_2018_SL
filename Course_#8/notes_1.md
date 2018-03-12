# Conversational agents
_Benoit Sagot_
_12/03/2018 - 1h_

# Turing test
...
Cheat tricks exist

# Winograd schemas
Multiple choice questions.
Best $58~\%$ over 60 questions; humans $90~\% \to 100~\%$.

# Finite-state dialog architecture

## System initiative
System controls entirely the conversation.
Easy to build: signel automaton, with known words.
But limited tasks.

## Problems with System initiative
Requires joint initiative.

## System initiative + universals
Commands you can say anywhere (help, ...)
But not many initative

## Mixed initiative
Initiative based on frame.
System will try to fill the slots that are not yet filled by asking questions.

## Mixed Initiative: Frames
User can answer multiple questions at once.

## Condition-Action rules (SIRI)
Based on partial ontology

## Part of ontology for meeting booking task
This is how system try to build the architecture. Symbolic reasoning.

## Improvements to the rule-based approaches
Machine learning to map sentences into semantic representations that are used to fill the slots in the frames: mapping between the sentence and the universal language. (**Semantic parsing**, cf also FrameNet)

# Towards generic chatbots: symbolic approaches
Online booking system: very specific systems (limited).

## Eliza
System uses what users say to build answers.

## Domain: Rogerian psychology interview
Draw the patient out by reflecting patient's statements back at them.
It's to hard to simulate sensible human brains. Either you simulate someone who has psychological problems, or a psychologist (becase a psychologist doesn't have a human brain?!).

## ELIZA's transformation-based approach
Input pattern > output pattern:
**Each keywords is associated with a list of patterns**. The first pattern matched is fired. If no keyword applies, then either Eliza will do default behaviour, or grab an action (last-resort strategies).

## ELIZA: Last-resort strategies
Queue build when no keyword is matched.

## ELIZA: Outcomes
First chatbot that look like a human to an extreme.
Privacy implications pointed out.
Anthropomorphism and the Heider-Simmel Illusion (1944).

## PARRY (Colby 1971)
Attempt to simulate a person with paranoid schizophrenia
Same patter-response structure as Eliza.
Much richer:
+ control cstructure
+ language understanding capabilities
+ mental model: anger, fear, mistrust.
First sytem to pass the Turing test

## PARRY's architecture
`Input > NLU step > modify affect variabls > output stategy selector > output`
Real attempt to simulate psychological states of a human. (Modern chatbots do no do this nowadays)

**Affect variables**: if nothing happens, fear / anger drop ; otherwise, each user statement can change fear and anger. Insulsts increases Anger by some percenetage ...
**Flace concepts**: odered graph designed to lead interviewer to topic.
**Conceptualisation**: semantic parsing / normalisation: each sentence can be expresseed in different ways.

## PARRY: intent inference system
Benevole-detectin rules (somethingp positive)...

## A discussion with PARRY
Quite convincing especially at the time.

## Paranoid PARRY goes to the ROgerian psychiatrist ELIZA (Cerf 1973)
ELIZA: none strategy all the time.

# Information Retrieval (IR) approaches

## IR-based models
General idea: consider the task of finding the correct answer to a user;s turn as identical to finding the correct answert to a generic question in an IR / QA (question answering) system.

# Machine Translation based systems
Most recent systems

## MT-based models
General idea: view response generation as (statistical) machine translation of the input. In 2010, no neural nets, so system based on alignments: exploit high-frequency patterns and mapping.

## Basic seq2seq model

### Sample Results from Google's paper

## Multi-context response generation (2015)
LSTM to create an embedding to the context thing, and combine them to get the final context embedding. Use this context embedding as an additional information during the decoding step.
**Context consistency**

## Speaker consistency
Speaker consistency not correctly handled yet.
If the same system answer 8 and 18 about its age, it's not consistency. You want to build some sort of consistency.

## How to represent users: persona embeddings
Use embedding to represent people.

## Persona seq2seq models
Add at each step the embedding the perso to who you are talking to.

# Some of the remaining problems

## Long-term success of the intercation
+ Avoid infinite loops: `See you later > see you later`
+ Idea: use reinforcement learning. System accumulate rewards depending on how the conversation is successful + optimze the model so that it seeks the highest possible reward.
+ Reward can be computed using a classifier that tries to distinguish human and chatbot aswer.

## Interactive bots (mixed initiative)
The chatbot must be able to ask questions:
+ clarification questions (active or passive)
+ knowledge operations (look at the user reaction to see if your answer was good)
+ knowledge acquisition: store the answer to improve itself overtime
+ verification questions: validate what it is about to do.

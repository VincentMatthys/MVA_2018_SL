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

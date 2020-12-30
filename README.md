# Minesweeper

## An AI to play Minesweeper.

<img src="resources/minesweeper_output.png" width="600">

Minesweeper is a puzzle game that consists of a grid of cells, where some of the cells contain hidden “mines.” Clicking on a cell that contains a mine detonates the mine, and causes the user to lose the game. Clicking on a “safe” cell reveals a number that indicates how many neighboring cells contain a mine. The goal of the game is to flag each of the mines.

<img src="resources/puzzle.jpg" width="700">

## Propositional Logic

Knowledge-based agents make decisions by considering their knowledge base, and making inferences based on that knowledge. One way we could represent an AI’s knowledge about a Minesweeper game is by making each cell a propositional variable that is true if the cell contains a mine, and false otherwise.

The AI would know every time a safe cell is clicked on and would get to see the number of neighboring cells that are mines for that cell.

## Knowledge Representation

We’ll represent each sentence of our AI’s knowledge like the below.

`{A, B, C, D, E, F, G, H} = 1`

Every logical sentence in this representation has two parts: a set of cells on the board that are involved in the sentence, and a number count, representing the count of how many of those cells are mines. The above logical sentence says that out of cells A, B, C, D, E, F, G, and H, exactly 1 of them is a mine.

This is useful because it lends itself well to certain types of inference:

- Any time we have a sentence whose count is 0, we know that all of that sentence’s cells must be safe.
- Any time the number of cells is equal to the count, we know that all of that sentence’s cells must be mines.
- Any time we have two sentences set1 = count1 and set2 = count2 where set1 is a subset of set2, then we can construct the new sentence set2 - set1 = count2 - count1.

In general, we only want our sentences to be about cells that are not yet known to be either safe or mines. This means that, once we know whether a cell is a mine or not, we can update our sentences to simplify them and potentially draw new conclusions.

So using this method of representing knowledge, the AI agent can gather knowledge about the Minesweeper board, and select cells it knows to be safe.

---------------------------------------------------------------------------------------------------

**Propositional Logic**

Propositional logic is based on propositions, statements about the world that can be either true or false. It uses propositional symbols like letters (*P*, *Q*, *R*) to represent a proposition.

To connect propositional symbols in order to reason in a more complex way it uses other logical symbols known as logical connectives:

- **Not (¬)** inverses the truth value of the proposition.

- **And (∧)** is true only in the case that both arguments are true.

- **Or (∨)** is true as as long as either of its arguments is true.

- **Implication (→)** represents a structure of "if *P* then *Q*". *P* is called the 'antecedent' and *Q* is called the 'consequent'.

- **Biconditional (↔)** is an implication that goes both directions. Can be read as "if and only if". *P* ↔ *Q* is the same as *P* → *Q* and *Q* → *P* taken together.

**Model Checking**

A *model* is an assignment of a truth value to every proposition. Knowledge about the world is represented in the truth values of these propositions. The model is the truth-value assignment that provides information about the world.

There could be more than one possible models. In fact, the number of possible models is 2 to the power of the number of propositions. If we have 2 propositions, that will be 2² = 4 possible models.

The knowledge base (KB) is a set of sentences known by a knowledge-based agent. This is knowledge that the AI is provided about the world in the form of propositional logic sentences that can be used to make additional inferences about the world.

Entailment is represented by the symbol (⊨). If *α* ⊨ *β* (*α* entails *β*), then in any world where *α* is true, *β* is true, too. Entailment is different from implication. Implication is a logical connective between two propositions. Entailment, on the other hand, is a relation that means that if all the information in *α* is true, then all the information in *β* is true.

Inference is the process of deriving new sentences from old ones. There are multiple ways to infer new knowledge based on existing knowledge. One of these ways is the Model Checking algorithm.

To determine if KB ⊨ *α* (in other words, answering the question: “can we conclude that *α* is true based on our knowledge base”):
- Enumerate all possible models.
- If in every model where KB is true, *α* is true as well, then KB entails *α* (KB ⊨ *α*).

To run the Model Checking algorithm, the following information is needed:

- Knowledge Base, which will be used to draw inferences.
- A query, or the proposition that we are interested in whether it is entailed by the KB.
- Symbols, a list of all the symbols used.
- Model, an assignment of truth and false values to symbols.

We are interested only in the models where the KB is true. If the KB is false, then the conditions that we know to be true are not occurring in these models, making them irrelevant to our case.

## Implementation

At `logic.py`, we define several classes for different types of logical connectives. These classes can be composed within each other, so an expression like `And(Not(A), Or(B, C))` represents the logical sentence stating that symbol `A` is not true, and that symbol `B` or symbol `C` is true (where “or” here refers to inclusive, not exclusive, or).

`logic.py` also contains a function `model_check`. `model_check` takes a knowledge base and a query. The knowledge base is a single logical sentence: if multiple logical sentences are known, they can be joined together in an `And` expression. `model_check` recursively considers all possible models using the `check_all` function, and returns `True` if the knowledge base entails the query, and returns `False` otherwise.

As mentioned, the way the `check_all` function works is recursive. That is, it picks one symbol, creates two models, in one of which the symbol is true and in the other the symbol is false, and then calls itself again, now with two models that differ by the truth assignment of this symbol. The function will keep doing so until all symbols will have been assigned truth-values in the models, leaving the list `symbols` empty. Once it is empty (as identified by the line `if not symbols`), in each instance of the function (wherein each instance holds a different model), the function checks whether the KB is true given the model. If the KB is true in this model, the function checks whether the query is true, as described earlier.

<img src="resources/model_check.png" width="1000">

At `puzzle.py`, we define six propositional symbols. `AKnight`, for example, represents the sentence that “A is a knight”, while `AKnave` represents the sentence that “A is a knave.” We similarly define propositional symbols for characters B and C as well.

What follows are four different knowledge bases, `knowledge0`, `knowledge1`, `knowledge2`, and `knowledge3`, which contain the knowledge needed to deduce the solutions to the upcoming Puzzles 0, 1, 2, and 3, respectively.

The `main` function of this `puzzle.py` loops over all puzzles, and uses model checking to compute, given the knowledge for that puzzle, whether each character is a knight or a knave, printing out any conclusions that the model checking algorithm is able to make.

**Representing the puzzles using propositional logic**

Knowledge engineering is the process of figuring out how to represent propositions and logic in AI.

For each knowledge base, we want to encode two different types of information: (1) information about the structure of the problem itself (i.e., information given in the definition of a Knight and Knave puzzle), and (2) information about what the characters actually said.

Considering what it means if a sentence is spoken by a character. Under what conditions is that sentence true? Under what conditions is that sentence false? How can we express that as a logical sentence?

There are multiple possible knowledge bases for each puzzle that will compute the correct result. We attempt to choose a knowledge base that offers the most direct translation of the information in the puzzle, rather than performing logical reasoning on our own. We should also consider what the most concise representation of the information in the puzzle would be.

For instance, for Puzzle 0, setting `knowledge0 = AKnave` would result in correct output, since through our own reasoning we know 'A' must be a knave. But doing so would be against the spirit of this problem: the goal is to have our AI do the reasoning for us.

## Resources
* [Knowledge - Lecture 1 - CS50's Introduction to Artificial Intelligence with Python 2020][cs50 lecture]

## Usage

**To solve logic puzzles:** 

* Inside the `knights` directory: `python puzzle.py`

## Credits
[*Luis Sanchez*][linkedin] 2020.

Project from the course [CS50's Introduction to Artificial Intelligence with Python 2020][cs50 ai] from HarvardX.

[cs50 lecture]: https://www.youtube.com/watch?v=LucW-p6zC5c&feature=youtu.be
[linkedin]: https://www.linkedin.com/in/luis-sanchez-13bb3b189/
[cs50 ai]: https://cs50.harvard.edu/ai/2020/

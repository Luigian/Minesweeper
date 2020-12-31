# Minesweeper

## An AI to play Minesweeper.

<img src="resources/minesweeper_output.png" width="600">

Minesweeper is a puzzle game that consists of a grid of cells, where some of the cells contain hidden “mines.” Clicking on a cell that contains a mine detonates the mine, and causes the user to lose the game. Clicking on a “safe” cell reveals a number that indicates how many neighboring cells contain a mine. The goal of the game is to flag each of the mines.

**Propositional Logic**

Knowledge-based agents make decisions by considering their knowledge base, and making inferences based on that knowledge. One way we could represent an AI’s knowledge about a Minesweeper game is by making each cell a propositional variable that is true if the cell contains a mine, and false otherwise.

The AI would know every time a safe cell is clicked on and would get to see the number of neighboring cells that are mines for that cell.

**Knowledge Representation**

We’ll represent each sentence of our AI’s knowledge like the below.

`{A, B, C, D, E, F, G, H} = 1`

Every logical sentence in this representation has two parts: a set of cells on the board that are involved in the sentence, and a number count, representing the count of how many of those cells are mines. The above logical sentence says that out of cells A, B, C, D, E, F, G, and H, exactly 1 of them is a mine.

This is useful because it lends itself well to certain types of inference:

- Any time we have a sentence whose count is 0, we know that all of that sentence’s cells must be safe.
- Any time the number of cells is equal to the count, we know that all of that sentence’s cells must be mines.
- Any time we have two sentences set1 = count1 and set2 = count2 where set1 is a subset of set2, then we can construct the new sentence set2 - set1 = count2 - count1.

In general, we only want our sentences to be about cells that are not yet known to be either safe or mines. This means that, once we know whether a cell is a mine or not, we can update our sentences to simplify them and potentially draw new conclusions.

So using this method of representing knowledge, the AI agent can gather knowledge about the Minesweeper board, and select cells it knows to be safe.

## Implementation

There are two main files in this project,`runner.py`, which contains all of the code to run the graphical interface for the game; and `minesweeper.py`, which contains all of the logic the game itself and for the AI to play the game.

In `minesweeper.py` there are three classes defined, `Minesweeper`, which handles the gameplay; `Sentence`, which represents a logical sentence that contains both a set of cells and a count; and `MinesweeperAI`, which handles inferring which moves to make based on knowledge.

Each cell is a pair `(i, j)` where `i` is the row number (ranging from `0` to `height - 1`) and `j` is the column number (ranging from `0` to `width - 1`).

**The `Sentence` class**

This class is used to represent logical sentences of the form described before. Each sentence has a set of `cells` within it and a `count` of how many of those cells are mines. 

The class also contains functions `known_mines` and `known_safes` for determining if any of the cells in the sentence are known to be mines or known to be safe. 

It also contains functions `mark_mine` and `mark_safe` to update a sentence in response to new information about a cell.

**The `MinesweeperAI` class**

This class will implement an AI that can play Minesweeper. The AI class keeps track of a number of values. `self.moves_made` contains a set of all cells already clicked on, so the AI knows not to pick those again. `self.mines` contains a set of all cells known to be mines. `self.safes` contains a set of all cells known to be safe. And `self.knowledge` contains a list of all of the Sentences that the AI knows to be true.

The `mark_mine` function adds a cell to `self.mines`, so the AI knows that it is a mine. It also loops over all sentences in the AI’s knowledge and informs each sentence that the cell is a mine, so that the sentence can update itself accordingly if it contains information about that mine. The `mark_safe` function does the same thing, but for safe cells instead.

The `add_knowledge` takes as input a `cell` and its corresponding `count`, and updates `self.mines`, `self.safes`, `self.moves_made`, and `self.knowledge` with any new information that the AI can infer, given that `cell` is known to be a safe cell with `count` mines neighboring it. The function adds a new sentence to the AI’s knowledge base, based on the value of `cell` and `count`, including only cells whose state is still undetermined in the sentence. If, based on any of the sentences in `self.knowledge`, new cells can be marked as safe or as mines, or, new sentences can be inferred (using the subset method described before), then the function does so. 

Any time that we make any change to our AI’s knowledge, it may be possible to draw new inferences that weren’t possible before. That's why the function loops over and over until there's no change made, so we can be sure that those new inferences are added to the knowledge base.

The `make_safe_move` function returns a move `(i, j)` that is known to be safe, and not a move already made. If no safe move can be guaranteed, the function returns `None`. The function don't modify `self.moves_made`, `self.mines`, `self.safes`, or `self.knowledge`.

The `make_random_move` function is called if a safe move is not possible and returns a random move `(i, j)`. The move isn't a move that has already been made or a move that is known to be a mine. If no such moves are possible, the function returns `None`.

When we run our AI (as by clicking “AI Move”), note that it will not always win. There will be some cases where the AI must guess, because it lacks sufficient information to make a safe move. This is to be expected. `runner.py` will print whether the AI is making a move it believes to be safe or whether it is making a random move.

## Resources
* [Knowledge - Lecture 1 - CS50's Introduction to Artificial Intelligence with Python 2020][cs50 lecture]

## Usage

**To install pygame:**

* Inside the `minesweeper` directory: `pip3 install -r requirements.txt`

**To play Minesweeper (or let the AI play for you):** 

* Inside the `minesweeper` directory: `python runner.py`

## Credits
[*Luis Sanchez*][linkedin] 2020.

Project and images from the course [CS50's Introduction to Artificial Intelligence with Python 2020][cs50 ai] from HarvardX.

[cs50 lecture]: https://www.youtube.com/watch?v=LucW-p6zC5c&feature=youtu.be
[linkedin]: https://www.linkedin.com/in/luis-sanchez-13bb3b189/
[cs50 ai]: https://cs50.harvard.edu/ai/2020/

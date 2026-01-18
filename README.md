# A Bayesian Look-Ahead Heuristic for Blackjack

## Project Overview
This project dervies a computational decision-engine for a Blackjack game (1 player vs dealer). Unlike a standard strategy chart, this algorithm employs a one-step look-ahead policy to determine whether the player should hit or stand at any possible game state. The algorithm does not implement splitting capabilities. 

## Algorithm Specifics and One-Step Heuristic
The algorithm tracks the composition of the 52-card deck throughout each game iteration. Before each player decision, it analytically calculates the probability distribution of the next card drawn. This allows for strategies to be altered dynamically, based on which cards are already shown. 

To determine the value of a "stand" decision, the engine executes $10^3$ randomized trials of the dealer's hand from the current state, and returns a normalized win/loss/tie rate to represent "Stand EV."

The primary decision logic evaluates $EV_{Hit} = \sum_{i=1}^{10}P(C_i) \times EV_{\text{Stand}} (\text{Hand} + C_i)$. While full recusion (simulating every possible hit sequence until a player reaches 21 or busts) is optimal in theory, the computational trade-off is not worth it. The current algorithm only calculates relatve expected value: if $EV(\text{Stand} > EV(\text{Hit once and then stand})$, then it can be assumed that $EV(\text{Stand} > EV(\text{Hit multiple times})$. This algorithm captures mispredictions by breaking ties in favor of hitting, as it is true that absolute expected value of hitting multiple times will often exceed expected value of hitting once and then standing.

Although this engine does not guarantee globally optimal play, the near-negligible accuracy loss is more than justified given the reduced computational load. 

## Performance Validation
The engine includes an option to play the game manually and one to simulate the engine's performance. By automating the engine's recommendations, we can empirically validate the win-rate of the Bayesian policy against the dealer. In past simulations, the engine typically wins between 40-45% of games and loses 45-50% of games, which is consistent with high-performance engines that reach the mathematical limit of the game.

## Technical Implementation
The algorithm was coded in Python and utilizes the random library.
* [Link to code](blackjack-sim.py)
* [Back to Portfolio](https://brycepyburn.github.io/)

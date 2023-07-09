---
title: WordleBot - Using Spectral Clustering and Reinforcement Learning to Win at Wordle
description: <a href="https://towardsdatascience.com/wordlebot-386395472639" target="_">Published on Towards Data Science</a>.
category: Reinforcement Learning
#date: 2022-01-05 08:01:35 +0300
#role: Data Scientist
image: '/images/wordlebot/scrabble_tiles.jpg'
image_caption: ''
---

[Wordle](https://www.nytimes.com/games/wordle/index.html) is a simple word game developed by Josh Wardle. After reading about it in the New York Times, I gave it a try and was immediately hooked. My wife and I have developed a bit of a rivalry, comparing our guess count each day, and I thought it would be fun to build a bot to add to the competition. In this post, I’ll review the rules of Wordle and walk through my bot’s design and performance over 1,000 simulated games. The main ML/AI techniques used include Spectral Clustering and Q Learning.

**TLDR;**

* I used Machine Learning to build a bot to play Wordle on hard mode.
* Over 1,000 simulated games, the bot took an average of **3.767** guesses to discover the correct word.
* The bot lost 8 out of the 1,000 simulated games for an overall **win rate of 99.2%**.

# Wordle Primer / Rule Review

For the uninitiated, Wordle is a guessing game similar to Hangman. Each day, you have up to 6 attempts to guess a hidden word. For each letter of each guess, you receive feedback (green if the letter is in the hidden word and in the correct position, yellow if the letter is in the word but in the wrong position, and grey if the letter is not in the hidden word).

If you play the game on **hard mode**, you are **required** to use the feedback from each guess to inform your subsequent guesses (preventing you from submitting guesses purely to gain information and reduce the pool of possible words). My bot plays on hard mode.

# Related Work

A quick scan of the blogosphere revealed that I wasn’t the only one intrigued by using Data Science to tackle the Wordle challenge.

* Matt Rickard [described a greedy approach](https://matt-rickard.com/wordle-whats-the-best-starting-word/) to determine the “best” starting guess for Wordle based on eliminating as many answer choices as possible (he landed on "SOARE", which I didn’t even know was a word).
* Mark Scherschel II [proposed using pairwise distances to measure word centrality](https://medium.com/@schersch/an-informed-first-wordle-d2b3f001cd1a), choosing "TARES" as the best starting word choice due to its relative proximity to other words.

I wanted to incorporate and extend these ideas in my bot’s design to build an autonomous agent, focusing less on the best first word and more on a policy governing the best next guess to navigate from one state of play to the next. Ideally, this agent would learn from its experiences and improve its performance as it played more and more games.

# Source Data

While there are more than 12,000 5-letter words in the English language, many of them are archaic or esoteric. The creator of Wordle limited the answer bank to ~2,300 commonly used words in everyday language. I manually copied the 2,300-word bank from the Wordle source code to train my bot.

# Word Similarity Graph

The set of feasible words is organized in an undirected [graph data structure](https://en.wikipedia.org/wiki/Graph_(abstract_data_type)). Each “node” or “vertex” is a word, and each “edge” or “link” between words is weighted by a simple similarity score. The score is composed of 1 point for each common letter between two words and an additional point if the common letter is also in the same position in the word.

Pairwise similarity scores are computed for all words in the smaller answer bank and stored in a symmetric adjacency matrix.

# Spectral Clustering

Spectral Clustering is used to partition the graph into k “communities” (groups of similar nodes). I used a k value of 5. I’ll not delve too deeply into the weeds for this post, but if you haven’t come across this algorithm, you can think of it as K-Means for similarity graphs. For those interested in reading further, [check out this Wikipedia post](https://en.wikipedia.org/wiki/Spectral_clustering) (good jumping off point).

# Q Learning

Q Learning is a form of Reinforcement Learning that seeks to learn a policy π based on experiences (in the form of transitions between states, actions, and resulting rewards). One challenge in Q Learning is to define a discrete state space such that an agent can gain sufficient experience through experimentation to learn the “best” action in a given situation to maximize expected reward. Another challenge is to establish a reward system that effectively balances short-term and long-term rewards (e.g., it’s easier to train a bot to eat a candy bar than to save for college).

In this exercise, I define state by discretizing the distribution of nodes among the k communities. With k=5, this resulted in 126 possible states.

Actions were defined as **which community** the bot should choose its next word guess from.

Rewards were defined as the sum of the following 3 components: a shrinkage factor, a correctness factor, and a winning bonus.

The shrinkage factor is defined as the ratio of the number of feasible words before the guess to the number of feasible words after the guess. The more words eliminated by the guess, the larger the shrinkage factor.

The correctness factor is defined as 2 points for each green letter (correct letter in the correct position), 1 point for each yellow letter (correct letter, wrong position), and 0 points for each grey letter (letter not in the correct word).

The bot earns a big bonus of 1,000 points for each winning guess.

# Overall Strategy

For each game, a Grader class randomly selects a word from the bank of 2,315 common 5-letter words. The bot then uses an iterative approach to make guesses. In each iteration, the bot performs the following operations:

* partitions the graph using spectral clustering on the set of remaining feasible words
* discretizes the distribution of words among clusters
* uses the Q Learner to select a cluster from which to draw the next guess
* chooses the guess as the “centroid” of the chosen cluster (word having the highest total similarity score within the cluster)
* passes the guess to the Grader class which evaluates the guess and returns feedback in the form of a vector of scores for each letter (2 for green, 1 for yellow, 0 for grey)
* filters the graph to exclude infeasible words based on feedback from the grader
* calculates a reward to pass to the Q Learner in the next iteration

The bot terminates its iterations when it guesses the correct word.

With this strategy, I make the bot play 1,000 games and record the guess count for each.

# Performance

Over the 1,000 games, the bot achieves an average guess count of **3.767**. It “loses” 8 times (needing more than 6 guesses to identify the correct word) for an overall win rate of 99.2%. Here’s a chart showing the bot’s performance over its 1,000-game epoch:

![Games vs Guesses]({{site.baseurl}}/images/wordlebot/wordle_games_vs_guesses.png)
*WordleBot guess counts over 1,000 simulated games.*


# Conclusions

I was pleasantly surprised with my bot’s performance in general. My own Wordle average hovers between 3 and 4, so the bot can definitely hold its own against a human!

That said, there’s plenty of room for improvement. Calculating and storing similarity scores is by far the least computationally efficient part of my bot’s current design, as it requires 5,359,225 pairwise similarity scores to be computed for the 2,315 words. Since the graph is symmetric, technically the ~5 million similarity computations could be halved to ~2.5 million. Because the scores never change, they really only ever need to be computed once (while my current design recomputes scores unnecessarily out of expedience). The entire simulation takes about 45 minutes to run in a Google Colab environment, and I’m confident this runtime could be substantially decreased with some refactoring.

Additionally, there are many parameters which could be tuned. For example, I arbitrarily set the number of communities to 5 for this exercise and achieved results that were good enough for me, but there may be some other optimal number of communities.

Lastly, I suspect the Q Learning part of the bot may be overkill. I didn’t observe the reduction in the number of guesses per game I expected to see as the bot gained experience, suggesting there wasn’t much of a “convergence” happening. For a fun short project though, I’m really pleased with how it all turned out.

An extended version of this post including code can be found in a [Jupyter Notebook on my GitHub page](https://github.com/danschauder/wordlebot/blob/main/Wordle_Bot.ipynb).
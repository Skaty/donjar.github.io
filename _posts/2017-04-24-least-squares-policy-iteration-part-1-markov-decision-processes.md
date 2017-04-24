---
layout: post
mathjax: true
date: 2017-04-24
title: "Least Squares Policy Iteration pt. 1: Markov Decision Processes"
categories: computer-science algorithms artificial-intelligence
---

In NUS CS3243 (Introduction to Artificial Intelligence) we had a project, which is to build an agent that plays Tetris. It was an interesting project, even though I sort of freerided (sorry my teammates haha).

I was tasked to look at this interesting algorithm called LSPI (Least Squares Policy Iteration) though. Took me some time to grasp it, and I finally understood it. Sadly, when I implemented it, the policy resulted clears exactly 0 lines. :/ Perhaps, the features were bad, or the samples were bad, or I just misunderstood the algorithm. My seniors told me that they can clear like millions of lines with LSPI...

Anyways, all the reason for me to recap about it, isn't it? Maybe even juniors can refer to this in future iterations of the project! (It might be changed though, since I heard my batch is the last time Prof. Bryan's teaching CS3243)

I forsee that this can be long, hence I decided to break it into a few parts. This part talks about the underlying model, which is Markov Decision Process (MDP). Probably in the next part I will talk about approximation algorithms and LSPI itself, and the final part I will try to re-implement it with a simpler game.

## Basic Idea

The idea of a MDP is simple. We have a set of states $$S$$, as well as a set of actions $$A$$. An action brings you from a state to some other states with a given probablity. Doing an action can also give you a reward.

![Example of a Markov Decision Process]({{ site.url }}/images/blog/2017-04-24-markov-decision-process.png)

Shown above is an example of a Markov Decision Process. Here, the states are indicated by $$S1$$ until $$S4$$, and the actions are $$a1$$, $$a2$$, and $$a3$$. Listed in the brackets are the probability that doing an action will bring you to that state. For example, while action $$a1$$ will always bring you from $$S1$$ to $$S2$$, action $$a2$$ has 0.9 probability of bringing you back to $$S1$$, and 0.1 probability of bringing you to $$S4$$. As you can see, an action can bring you back to the same state.

Also listed after the letter R is the reward. For example, the reward of doing action $$a1$$ that brings you from $$S2$$ to $$S3$$ is 100, while the reward of doing action $$a3$$ that brings you from $$S4$$ to $$S4$$ is 1.

So, what can be modeled by this, you may ask? Many things! Consider, for example, Tetris; here, the states are the position of the board as well as the next piece, and the action is the act of placing a piece into the board. The rewards would be the number of points you get. Most likely, this would be the number of rows cleared; in certain games, however, you might get rewarded for things like back-to-back Tetris or T-spins. In this case, the probability of any action will always be 1.

We can also look at [Yahtzee](https://en.wikipedia.org/wiki/Yahtzee), for example. Here, the states are everything that describes the current position. This includes points filled in the scoresheet, current dice position, turn number, and so on. The actions would be the dice that you choose to roll, or filling in a scoresheet. The reward would be the number of points you get on filling in a score, and the probablity varies according to the action taken. If you're rolling all 5 dice, for example, there are $$\binom{10}{5} = 252$$ states that you can reach!

What is the main objective of this MDP, anyways? The idea is to find a policy $$\pi$$ that specifies what action should one take from a given state, so that we can maximize the total number of rewards over time. On the MDP above, for example, a policy would be to do action $$a1$$ from $$S1$$, action $$a1$$ from $$S2$$, action $$a3$$ from $$S3$$, and action $$a3$$ from $$S4$$.

## Formal Definition

Before we proceed, it's a good idea to formalize everything to remove any doubts.

A MDP consists of:
- $$S$$, a set of states.
- $$A$$, a set of actions.
- $$P: S \times A \times S \to [0, 1]$$, the probability function. This function takes in the starting state $$s$$, the action $$a$$ and the resulting state $$s'$$, and returns the probability that taking action $$a$$ from state $$s$$ brings you to $$s'$$. In the diagram above, $$P(S1, a1, S2) = 1$$ while $$P(S1, a3, S3) = 0$$.
- $$R: S \times A \times S \to \mathbb{R}$$, the reward function. In the diagram above, $$R(S2, a1, S3) = 100$$, while $$R(S1, a1, S2) = 0$$.
- $$\gamma \in [0, 1]$$, the discount factor, which is described below.

The objective is to find a function $$\pi$$ that maps from $$S$$ to $$A$$, so that the value

$$\sum_{t = 0}^\infty \gamma^t R(s_t, \pi(s_t), s_{t + 1})$$

is maximized. Here $$t$$ denotes the "time" and $$s_0, s_1, s_2, \dotsc$$ are states the agent is in at time $$0, 1, 2, \dotsc$$ respectively.

We can see that $$\gamma$$ is a value that diminishes the importance of future rewards. This $$\gamma$$ is typically close to 1, and is only there so that the infinite sum can converge. Indeed, if $$S$$ and $$A$$ are both finite, $$R$$ has a upper bound and the sum will definitely converge if $$\gamma < 1$$. There should be nothing wrong with setting $$\gamma = 1$$ though!

## Finding the Policy

So how do we actually find this optimal policy $$\pi$$? A simple idea might be to just take the action that maximizes the direct reward. For example, in the sample above, we can just take $$a1$$ from $$S2$$ since we can cash 100 instantly.

However, this might not be optimal! We can see that the optimal way is to actually take $$a2$$ from $$S2$$, then take $$a2$$ again from $$S1$$. This is so that we can eventually reach $$S4$$, where we can do $$a3$$ an infinite number of times. (Of course, this assumes that $$\gamma$$ is sufficiently big that it will be able to reach a total reward greater than 100 after a sufficient amount of time.)

Hence, we should define a value function $$V$$ that determines how rewarding a state is, according to a given policy. Before that, it is nice to define a helper function. Let's define an evaluation of how wise an action is, as follows: the wiseness of taking action $$a$$ that moves the agent from state $$s$$ to state $$s'$$ is

$$W(s, a, s') = P(s, a, s')\left[ R(s, a, s') + \gamma V(s') \right].$$

This wiseness is something I made up by the way :). This should make sense, though; if we want to consider how wise an action is, we should see the probability, the reward of taking the action, as well as how valuable the next state is.

Notice that, previously, $$V$$ is still not properly defined yet. We are ready to do so now. Here, $$V$$ maps from $$\Pi \times S$$ to $$\mathbb{R}$$, where $$\Pi$$ is the set of all policies possible, such that

$$V(\sigma, s) = \sum_{s' \in S} W(s, \sigma(s), s').$$

Simple, right? We just sum up all the "wiseness" of taking action $$\sigma(s)$$ from $$s$$ to everywhere. Also, if the context of a policy is clear, we can drop the $$\sigma$$ (the policy).

Now, to get the best policy, we just need to pick the action such that this sum of wiseness is maximized:

$$\pi(s) = \max_{a \in A} \left\{ \sum_{s' \in S} W(s, a, s') \right\}.$$

This might seem weird; we get the values from the policy, yet we get the policy from the values. It seems that there is a chicken-and-egg problem here!

To solve this, the idea is to start from a random policy $$\pi_0$$. Then, we use both the value equation and the policy equation above to improve the policy again and again, until it converges. It should converge; after all, there are only finitely many policies available, assuming finitely many states and actions.

Several variants of the algorithms exist. Notable algorithms include the "value iteration" and the "policy iteration".

### Value Iteration

In value iteration, we don't care about $$\pi$$ at all; we just aim at maximizing $$V$$. In other words, we merge the definition of $$\pi$$ into $$V$$, so that we get:

$$V_{i + 1}(s) = \max_{a \in A} \left\{ \sum_{s' \in S} W_i(s, a, s') \right\}.$$

Here $$i$$ is the iteration number, and we define $$W_i(s, a, s')$$ to be $$P(s, a, s') \left[ R(s, a, s') + \gamma V_i(s') \right]$$. So, we start at $$i = 0$$, getting $$V_0$$ as a value function from a random policy. Then, we iterate through the function above, stopping when we reach an $$i$$ such that $$V_{i + 1}(s) = V_i(s)$$.

If you don't want to merge the functions, this algorithm can also be viewed as running the policy and the value equations alternatingly.

### Policy Iteration

In policy iteration, we get a random policy at the start, then we calculate the _exact_ value of all states by repeatedly getting the value from the value equation until the value of each state converges. Then, from the values, we get a new policy from the policy equation, then we get the exact value again. We do this until the policy converges.

Basically:
- `old_policy` is initialized to a random policy, while `new_policy` is null
- While `old_policy != new_policy`:
  - `old_policy = new_policy` if not null
  - Get exact value of all states from the value equation until it converegs
  - Get `new_policy` from the policy equation and the values from the previous step

Notice that if we have a policy in hand, in the wiseness equation $$W(s, a, s') = P(s, a, s') \left[ R(s, a, s') + \gamma V(s') \right]$$, $$P(s, a, s')$$, $$R(s, a, s')$$, and $$\gamma$$ are actually already known! This means that we can just solve for the values by solving the system of $$n$$ _linear_ equations, where $$n$$ is the number of states. This equation is

$$V(s) = \sum_{s' \in S} W(s, \pi(s), s')$$

with $$V(s)$$ as the unknowns for all $$s \in S$$. This can be done with, for example, matrix inversion and multiplication. With Gauss-Jordan elimination this is $$O(n^3)$$; with Strassen algorithm this is around $$O(n^{2.8})$$.

Now, while these algorithms will definitely return the best policy that optimizes the MDP, we can see here the problem - these algorithms are _slow_. Imagine Tetris with 10 columns and 20 rows, for example; the number of possible states would be in an order of $$2^{10 \times 20} \approx 10^{60}$$. Even $$O(n)$$ is slow!

Enter LSPI, to be described in the next post (hopefully).

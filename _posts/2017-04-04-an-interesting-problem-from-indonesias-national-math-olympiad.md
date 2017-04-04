---
layout: post
mathjax: true
date: 2017-04-04
title: An Interesting Problem from Indonesia's National Math Olympiad
categories: math olympiad
---
Last year there was a very interesting problem from Indonesia's National Olympiad:

> Problem (Indonesia's National Math Olympiad 2016 no. 4). $$ABC$$ is a triangle such that the angles satisfy the equation
>
> $$\frac{\cos A}{20} + \frac{\cos B}{21} + \frac{\cos C}{29} = \frac{29}{420}.$$
>
> Prove that $$ABC$$ is a right-angled triangle.

My first reaction was "Huh? How the heck?"

An intriguing problem indeed. Firstly, the equations are not symmetrical, and secondly, how the heck does the numbers 20, 21, 29, and 420 appear?

Some people might argue that this problem is ugly. Indeed, putting these "magic numbers" doesn't look really nice; furthermore, trigonometric functions usually never appear in a problem explicitly. Nevertheless, whether it's pretty or ugly, I think we can agree that this problem is interesting and can throw some of the most experienced problem solvers even.

Note that the way to solve this is not by plugging one of $$A$$, $$B$$, or $$C$$ with 90 degrees. This attempt will only solve the converse ($$ABC$$ right-angled implies the equation).

One easy way would be to substitute $$C$$ with $$180^{\circ} - A - B$$ and use trigonometric identities to obtain $$\cos C = -\cos (A + B) = \sin A \sin B - \cos A \cos B$$. From there who knows if we can obtain something like $$\cos A = 0$$ (which means that $$A = 90^{\circ})$$.

Bashing (like this approach) is an OK solution, and if you're adept enough you might be able to solve this in around an hour (which is nice - the olympiad runs in 2 days, each day containing 4 problems in 4 hours, and usually problem 4 is the hardest of the day, which is indeed the case here).

My friend came with another approach that can involve even more bashing - move $$\frac{\cos C}{29}$$ to the right and square both sides! He got

$$\left( \frac{\sin A}{20} - \frac{\sin B}{21} \right)^2 = -\frac{\cos^2 C}{29^2}$$

LHS is nonnegative and RHS is nonpositive, hence both must be zero and $$\cos C = 0 \implies C = 90^{\circ}$$. Let's come up with a less bashing solution, though!

Now, from the numbers 20, 21, 29, and 420, we can notice two things:
- $$20 \times 21 = 420$$. More importantly,
- $$20^2 + 21^2 = 29^2$$, meaning that there is a right-angled triangle with sides 20, 21, and 29.

The last fact above might not be easy to catch. Intuitions like this separate the best problem solvers from others, and is the reason why practicing is important in olympiad mathematics. It sharpens your intuition.

Building from the two facts above, we can change the equation to:

$$\frac{21}{29} \cos A + \frac{20}{29} \cos B + \frac{20 \cdot 21}{29 \cdot 29} \cos C = 1$$

Now if we take $$\theta$$ an angle between 0 and $$90^{\circ}$$ such that $$\sin \theta = \frac{20}{29}$$, we have $$\cos \theta = \frac{21}{29}$$ and the equation can be simplified further into

$$\cos \theta \cos A + \sin \theta \cos B + \sin \theta \cos \theta \cos C = 1.$$

Aha! This looks much more managable isn't it? Even though we introduced a new variable, there are no more these so-called "magic numbers"!

We're still stuck here though, so no harm in plugging $$C = 180^{\circ} - A - B$$ and expanding $$\cos C$$ out, which we know equals $$\sin A \sin B - \cos A \cos B$$ (we discussed this). Hence the equation becomes

$$\cos \theta \cos A + \sin \theta \cos B + \sin \theta \cos \theta \sin A \sin B - \sin \theta \cos \theta \cos A \cos B = 1.$$

Looks messy? Not at all - this can be simplified to

$$(1 - \cos \theta \cos A)(1 - \sin \theta \cos B) = \sin \theta \cos \theta \sin A \sin B.$$

And from here we can finish this with a neat trick. Notice that $$1 \ge \cos (A - \theta) = \cos A \cos \theta + \sin A \sin \theta$$, meaning $$1 - \cos \theta \cos A \ge \sin \theta \sin A$$. Similarly, by using $$1 \ge \sin (B + \theta)$$ we have $$1 - \sin \theta \cos B \ge \cos \theta \sin B$$. Multiplying these inequalities, we have:

$$(1 - \cos \theta \cos A)(1 - \sin \theta \cos B) \ge \sin \theta \cos \theta \sin A \sin B.$$

However, we know that they are actually the same! Hence the two inequalities that we started off must be equalities after all, and hence, $$1 = \cos (A - \theta)$$ and $$1 = \sin (B + \theta)$$. Since we assumed $$0 < \theta < 90^{\circ}$$ and $$A$$ and $$B$$ are angles of a triangle, we have $$A - \theta = 0$$ and $$B + \theta = 90^{\circ}$$, making $$A + B = 90^{\circ}$$.

That means $$C = 90^{\circ}$$, and we are done!

To summarize, our solution has two main points:
1. Setting $$\sin \theta = \frac{20}{29}$$ to remove all the magic numbers
2. Removing $$C$$ from the equation and factoring it out nicely
3. Using inequalities and showing that the inequalities must be equalities after all

Hopefully these shows how cool math olympiad problems can be :) intuition is very important and can only be sharpened by solving more and more problems.

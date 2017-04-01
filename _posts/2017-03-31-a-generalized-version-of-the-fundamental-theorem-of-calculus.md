---
layout: post
date: 2017-03-31
title: A Generalized Version of the Fundamental Theorem of Calculus
categories: math real-analysis
---
Last semester (AY16/17 sem 1) I had a chance to take [MA3110S](https://nusmods.com/modules/MA3110S) under Prof. Chua Seng Kee. [SPM (Special Programme in Math)](http://ww1.math.nus.edu.sg/undergraduates.aspx?f=UP-SPM) modules are scary, but Prof. CSK has managed to up it into another level, giving things like generalized versions of theorems in the textbooks as well as his scary true/false questions! I really learned a lot under his guidance. (Your life in SPM won't be complete if you haven't taken any of his modules haha)

In this post, I will present one of the most memorable theorems from him. Before that, we would need to define some more definitions:

> _Definition._ The left-hand derivative $$f'_-(x)$$ is defined as $$\lim_{h \to 0^-} \frac{f(x + h) - f(x)}{h}$$. Similarly, the right-hand derivative $$f'_+(x)$$ is defined as $$\lim_{h \to 0^+} \frac{f(x + h) - f(x)}{h}$$.

For example, for the function $$f: \mathbb{R} \to \mathbb{R}$$ defined as $$f(x) = \vert x \vert$$, the value $$f'(0)$$ does not exist. However, $$f'_-(0) = -1$$ and $$f'_-(0) = 1$$.

It should be clear that if $$f'(c)$$ exists for some $$c \in \mathbb{R}$$, we have $$f'_-(c) = f'_+(c) = f'(c)$$. At the same time, if $$f'_-(c) \ne f'_+(c)$$, the derivative is not defined at $$c$$ (just like the case of $$\vert x \vert$$ in 0).

Now, we can proceed to our main result, which can be considered as the one-sided version of the Fundamental Theorem of Calculus:

> _Theorem (CSK's FTC)._ Let $$f$$ be a (Riemann) integrable function on $$[a, b]$$. Define $$F: [a, b] \to \mathbb{R}$$ such that $$F(x) = \int_a^x f(t) \, dt$$. Then, if $$\lim_{x \to c^-} f(x)$$ exists, we have $$F'_-(c) = \lim_{x \to c^-} f(x)$$.
>
> Similarly, if $$\lim_{x \to c^+} f(x)$$ exists, we have $$F'_+(c) = \lim_{x \to c^+} f(x)$$.

There are some interesting results that can be obtained. For example, it can be proven directly that $$f$$ is continuous at $$x_0$$ if and only if $$F$$ is differentiable at $$x_0$$. Hence, we know that if $$g: \mathbb{R}^+ \to \mathbb{R}$$ such that $$g(x) = \int_0^x \lfloor x \rfloor \, dx$$, $$g$$ is not differentiable at $$\mathbb{N}$$. In fact, $$g'_-(x) = \lceil x \rceil - 1$$ and $$g'_+(x) = \lfloor x \rfloor$$.

Also, note that it is required for $$\lim_{x \to c^-} f(x)$$ exists (similarly for the right-hand version). For example, one can take $$f: [0, 1] \to \mathbb{R}$$ such that $$f(x)$$ equals 1 if $$x$$ is in the form of $$\frac{1}{2^k}$$ for $$k \in \mathbb{N}$$, and 0 otherwise. By Riemann sums one can prove that $$\int_0^1 f(x)\, dx = 0$$, yet $$\lim_{x \to 0^+} f(x)$$ is undefined.

It is also interesting to note that this is also slightly different from the Fundamental Theorem of Calculus usually taught in high schools. Normally, the Fundamental Theorem of Calculus refers to this theorem:

$$\int_a^b f'(x) \, dx = f(b) - f(a).$$

By CSK's FTC proving this is obvious, since $$F(b) = \int_a^b f(t) \, dt$$ and $$F(a) = 0$$ in CSK's FTC notations.

Anyways, let's prove CSK's FTC! Here I will only prove the right-sided version, since the left-sided version is very similar. Let $$\lim_{x \to c^+} f(x) = L$$ exist. Notice that

$$\begin{align*}
\left\vert F'_+(c) - L \right\vert &= \lim_{h \to 0^+} \left\vert \frac{F(c + h) - F(c)}{h} - L \right\vert \\
&= \lim_{h \to 0^+} \left\vert \frac{\int_a^{c + h} f(t) \, dt - \int_a^{c} f(t) \, dt}{h} - L \right\vert \\
&= \lim_{h \to 0^+} \left\vert \frac{\int_c^{c + h} f(t) \, dt}{h} - L \right\vert \\
&= \lim_{h \to 0^+} \left\vert \frac{\int_c^{c + h} f(t) \, dt - hL}{h} \right\vert \\
&= \lim_{h \to 0^+} \left\vert \frac{\int_c^{c + h} \left( f(t) - L \right) \, dt}{h} \right\vert \\
&\le \lim_{h \to 0^+} \frac{\int_c^{c + h} \left\vert f(t) - L \right\vert \, dt}{h} \\

\end{align*}$$

Now if $$\lim_{x \to c^+} f(x) = L$$ exists, that means that for all $$\varepsilon > 0$$ there exists $$\delta > 0$$ such that $$c < x < c + \delta$$ implies $$\vert f(x) - L \vert < \varepsilon$$. This would mean that if $$0 < h < \delta$$, we have $$\int_c^{c + h} \left\vert f(t) - L \right\vert \, dt \le h\varepsilon$$. Taking $$h \to 0^+$$, we have that $$\left\vert F'_+(c) - L \right\vert$$ goes to 0 as well, and hence, $$F'_+(c) = L = \lim_{x \to c^+}$$. We are done.

---
title: Math question 1
date: 2025-08-13 10:43:29
mathjax: true
tags: math
---

A mathematical question:

There is

$abc=-1, a+b+c=4$

$AND$

$\frac{a}{a^{2}-3a-1}+\frac{b}{b^2-3b-1}+\frac{c}{c^2-3c-1}=\frac{4}{9}$

$What\ is\ a^2 + b^2 + c^2 ? $



Solution:

Take $\frac{a}{a^2-3a-1}$ as example.

We have
$$
\begin{aligned}
\frac{a}{a^2-3a-1}
&= \frac{a}{a^2-3a+abc}\\
&= \frac{1}{a-3+bc} \\
&= \frac{1}{a-(a+b+c-1)+bc}\\
&= \frac{1}{1-b-c+bc} \\
&= \frac{1}{(b-1)(c-1)} \\
\end{aligned}
$$
So
$$
\begin{aligned}
\frac{4}{9}&=\frac{a}{a^2-3a-1}+\frac{b}{b^2-3b-1}+\frac{c}{c^2-3c-1} \\
&=  \frac{1}{(b-1)(c-1)}+ \frac{1}{(a-1)(c-1)}+ \frac{1}{(a-1)(b-1)} \\
&=  \frac{a-1+b-1+c-1}{(a-1)(b-1)(c-1)} \\
&= \frac{a+b+c-3}{(a-1)(b-1)(c-1)} \\
&=  \frac{1}{(a-1)(b-1)(c-1)} \\
\end{aligned}
$$
Then
$$
4(a-1)(b-1)(c-1) = 9 \\
4[abc-ab-ac-bc+(a+b+c)-1] = 9 \\
4[-1-(ab+ac+bc)+4-1] = 9 \\
4[2-(ab+ac+bc)]=9 \\
2-(ab+ac+bc)=\frac{9}{4} \\
(ab+ac+bc)=2-\frac{9}{4}=-\frac{1}{4}
$$
From
$$
\begin{aligned}
16 &= (a+b+c)^2 \\
&= a^2+b^2+c^2+2ab+2ac+2bc \\
&= [a^2+b^2+c^2] + 2(ab+ac+bc) \\
&= [a^2+b^2+c^2] - 2*\frac{1}{4} \\
&= [a^2+b^2+c^2] - \frac{1}{2}
\end{aligned}
$$

$$
Thus, [a^2+b^2+c^2] = 16 + \frac{1}{2} = 16.5
$$


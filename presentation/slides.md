---
title: Modified Grover Search for Databases
subtitle: Quantum Computing Group Project
date: 2019-07-19
author:
 - J. Imbery
 - S. Santamaria
 - M. Signer
header-includes:
 - \usepackage{physics}
...

# Our $U_\omega: \ket{x} \ket{y} \to \ket{x} \ket{y \oplus f(x)}$

## For element search: $f(x) := x = k$

Let $X_{\bar k} = \bigotimes_{i=1}^{n} \begin{cases}
\mathbf{X} & \text{if $k$ has bit $i$ \emph{un}set}\\
\mathbf{I} & \text{otherwise}
\end{cases}: \ket{x} \to \ket{x \oplus \bar k}$,
and $U_\wedge: \ket{x}\ket{y} \to \ket{x}\ket{y \oplus \bigwedge_{i=1}^n x_i}$

Then $U_\omega = (X_{\bar k}) (U_\wedge) (X_{\bar k})$

![](u_omega_eq_5.pdf){height=4cm}

# Our $U_\omega: \ket{x} \ket{y} \to \ket{x} \ket{y \oplus f(x)}$

## For minimum search: $f(x) := x < k$

This is already implemented in Qiskit aqua with `qiskit.aqua.circuits.FixedValueComparator`


# Modified diffusion operator $U_s$

In the standard Grover search, we mirror around the (fixed) starting state $\ket{s}
= \frac{1}{\sqrt{N}} \sum_{i=0}^{2^n} \ket{i}$: $U_s = \mathbf{I} - 2\dyad{s}{s} = \mathbf{I} - \frac{2}{N} \mathbf{1}$

However, our starting state is given by $\ket{s} = A \ket{0}$.

We can use the distributive property:

$U_s = \mathbf{I} - 2 A \dyad{0} A^H = A \mathbf{I} A^H - 2 A \dyad{0} A^H = (A) (\mathbf{I}-2\dyad{0}) (A^H)$

So we can mirror about $\ket{s}$ by a isometric transform, mirror about $\ket{0}$, and then transform back!

# Iterative search for a single element

Consider the two cases: $k \in S, k \not \in S$:

In the first case, this will be a standard Grover search for one of $N$ elements. Then we will get $k$ as a measurement of $\ket{x}$.

In the second case, our Grover iteration will be an identity operation, so in the end we will get a random element from $S$ (which obviously won't be $k$).

So we can say $k \in S \Leftrightarrow \ket{x} \text{ measured as } k$.

# Iterative search for the minimum

- $work \gets \infty$
- repeat $K$ times:
  - Using Grover search, try to find $x \in S : x < work$
  - $work \gets \min(work, x)$

Unlike the single-element Grover search, we don't know $\dim \ket{good}$ (the
number of matching elements), so we don't know how many iterations to do.
Instead, try different numbers of iterations, up until the number we would need
for finding a single element: $1, 2, 4, \dots, \left[\frac{\pi}{4 \arcsin
\frac{1}{\sqrt N}}-\frac{1}{2}\right]$. The total time taken will still be $\mathcal{O}(N)$.

It can be proven that at least one of these iteration counts will have a good
boosting effect, so it is very likely that we will actually find a smaller
element. Because all smaller elements have the same probability of being
chosen, the expected number of remaining elements will be cut in half every
iteration. As such, we only need $\mathcal{O}(\log N)$ iterations to find the
correct minimum.

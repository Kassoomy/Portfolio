# Dice Roll a Reinforced Learning Example

We'll stablish a really simple game for the following example, the idea of the game is to thrown any number of dice $N$ with $X$ faces, with the objective to maximize the probability of getting a **target sum** defined by $S$, the idea of using this simple game is because we already know that the most probable sum of $N$ dices with $X$ faces can be found with the following equation: 

$$
\frac{N(X+1)}{2} = S
$$

if what we want to do is to find the $X$ number of dices to throw to maximize the probability of the **target sum** $S$, then we can just resolve the equation for $S$ in respect to $N$:

$$
\frac{2S}{(X+1)} = N
$$

So, for example in a game where you can throw a maximum of **100 6 sided dices** or in other words **100d6** and with an **target sum** $S=50$ we want to know how many dices to throw to maximizes the probability of getting **50** as a resulting sum, using the last equation we get: 

$$
\frac{2(50)}{(6+1)} \approx 14.29 
$$

$14.29d6$ would max our probabilities since we can't throw a fraction of a dice we either throw **14** or **15**, we can just round to the closest integer, in this case **14** and  that should give us a good approximation, but jut to confirm we can check with the fist equation which number of dices will give us the closest probable sum

|N|Equation|S|
|---|-------|------|
|14	|$\frac{14(6+1)}{2}$|49.00|
|15	|$\frac{15(6+1)}{2}$|52.50|

Now with this we know that there exists a possible optimal solution to the problem, which our model has to converge to when it's learning how to play the game.


## Coding
<sub> Using Python </sub>

First we import the libraries for the project
```python
import random
import matplotlib.pyplot as plt
import numpy as np
```

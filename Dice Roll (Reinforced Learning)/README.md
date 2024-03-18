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
Then we define the functions which will control the **Actions** of the **Agent**, the game rules and the learning of the **Agent**

```python
# Function for obtaining the next Action (dices to throw) based on the Politics
def getNextAction(state, Q, epsilon):
    if random.uniform(0, 1) < epsilon:
        return random.randint(1, maxDices)  # Random exploration
    else:
        return Q[state][1]  # Exploiting the current best action

# Function that will update the Q matrix after an Action and observation of the new game State along with the Reward
def updateQ(Q, state, action, reward, newState, alpha, gamma, max_Q2):
    if reward >= max_Q[state][2]:
        max_Q[state] = [state, action, reward]
        Q[state][1] = round(Q[state][1] + (alpha * (reward + (gamma * max_Q2[state][1]) - Q[state][1]))) # Bellman Equation

    return Q

# Function which simulates a game round with the current Action or number of dices thrown
def roundSim(Q, max_Q):

    pointSum = 0
    action = getNextAction(0, Q, epsilon)

    for dice in range(action):
        diceResult = random.randint(1, maxDiceFace)
        pointSum += diceResult

    if pointSum != targetSum:
        reward = -(abs(targetSum-pointSum))
    else:
        reward = pointSum


    newState = 0

    Q = updateQ(Q, 0, action, reward, newState, alpha, gamma, max_Q)

    return Q

```

Finally we can apply our functions in a recurrent game where we'll give the model or **Agent** a maximum number of rounds or **Epochs** to either get to a local optimal solution or give us the best one it manage to find in the last **Epoch** 

```python
# Define the game variables
targetSum = 50
maxDices = 100
maxDiceFace = 6

# We define and initial matrix Q (Politics)
Q = [[0, maxDices]] # matrix Q[State][Action]
print(Q)
max_Q = [[0, 0, -abs(targetSum - (maxDiceFace * maxDices))]] # We set the starting Action as the worst possible strategy for a clearer learning curve
print(max_Q)

# Define the learning parameters
alpha = 0.1  # rate of learning
gamma = 0.1  # discount factor
epsilon = 0.1  # porbability of exploring random strategies

# Train the model during various epochs until it converges into an optimal strategy

currentTrainignRound = 0
maxEpochs = 10
optimalQs = []
optimalRs = []
timePerEpoch = []

start_time = time.time()

# This will define our stop criteria, in this case we'll use either it exhausted the epochs to find the answer or it did find what we consider and optimal answer
while currentTrainignRound < maxEpochs and max_Q[0][2] != targetSum: 
    Q = roundSim(Q, max_Q)
    optimalQs.append(Q[0][1])
    optimalRs.append(max_Q[0][2])
    currentTrainignRound += 1
    timePerEpoch.append(time.time() - start_time)

fig,ax = plt.subplots()
ax.plot(optimalQs, color="blue", marker="o")
ax.set_xlabel("Epoch", fontsize = 14)
ax.set_ylabel("Dice Thrown", color="blue", fontsize=14)

ax2=ax.twinx()
ax2.plot(optimalRs, color="red",marker="o")
ax2.set_ylabel("Rewards",color="red",fontsize=14)
plt.show()


print(f'Optimal number of dice to throw {max_Q[0][1]} found in {currentTrainignRound} Epochs')
```

After we ran the above code we'll get a plot of which **Action** (dice thrown) our **Agent** took in each **Epoch** and the rewards it got, and how this influenced the **Agent** to make better decisions and learn to play our simple game

![](https://github.com/Kassoomy/Portfolio/blob/main/Dice%20Roll%20(Reinforced%20Learning)/Images/learning%20plot.png)
>Optimal number of dice to throw 14 found in 10 Epochs

as we can see our **Agent** managed to learn based on its own experiences, our theoretical optimal strategy for maximizing the probability of getting the **target sum** $S=50$ when throwing $N$ 6 sided dices.

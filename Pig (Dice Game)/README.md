# Pigs (Agalludo) Dice Game Probabilities
> [!NOTE]
> This is a recursive programing approach

## Generalities

First, we need the base of the game, which can be found in the [Wikipedia page about Pigs (Dice Game)](https://en.wikipedia.org/wiki/Pig_(dice_game)) in summary the game goes:

> *Each turn, a player repeatedly rolls a die until either a 1 is rolled or the player decides to "hold":*
> 
> *   *If the player rolls a 1, they score nothing, and it becomes the next player's turn.*
> *   *If the player rolls any other number, it is added to their turn total, and the player's turn continues.*
> *   *If a player chooses to "hold", their turn total is added to their score, and it becomes the next player's turn.*
> 
> *The first player to score 100 or more points wins.*
> 
> *For example, the first player, Donald, begins a turn with a roll of 5. Donald could hold and score 5 points but chooses to roll again. Donald rolls a 2 and could hold with a turn total of 7 points but chooses to roll again. Donald rolls a 1 and must end his turn without scoring. The next player, Alexis, rolls the sequence 4-5-3-5-6, after which she chooses to hold, and adds her turn total of 23 points to her score.*"

our goal this time is to find what is the probability of getting a partial sum in a single turn without exceeding the objective, in other words from the minimum sum (in the base case 2) to the objective or winning condition (in the base case 100).

We know that the probability of any face to appear in a 6-sided dice is $1/6$ or $\approx 16.67\%$ or in other words the probability of getting any number from an array from 1 to 6 with a uniform distribution.

We'll divide the results into Probability to Lose ***(PL)***, which in the base case will mean to get a 1 in any of the "steps" (*any dice thrown during a turn*) of the player's turn, and Probability to Win ***(PW)*** which will mean the probability to get to a specific sum in a single turn.

To ease our process, we'll only calculate the ***PL*** and found the ***PW*** as $PW = 1 - PL$.

Our first instinct would be to check for the probabilities of $N$ number of throws, finding those ***PL*** we can go step by step in probability tree diagram.

Starting with the first 3 throws we get the following probabilities trees:

If we do 1 throw, we'll have a $PL = 1/6 \approx 16.67 \%$ making the $PW = 5/6 \approx 83.33 \%$

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/1%20Dice%20Throw.png)

If we do 2 throws, we'll have a $PL = (1/6) + [(1/6) * (5/6)] \approx 30.56 \%$ making the $PW = (5/6) * (5/6) = 25/36 \approx 69.44 \%$

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/2%20Dice%20Throw.png)

If we do 3 throws, we'll have a $PL = (1/6) + [(1/6) * (5/6)] + [(1/6) * (5/6) * (5/6)]  \approx 42.13 \%$ making the $PW = (5/6) * (5/6) * (5/6) = 125/216 \approx 57.87 \%$

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/3%20Dice%20Throw.png)

If see a pattern emerging where the ***PW*** is equal to (5/6)$ to the power of the number of throws so for 3 throws we'll get $PW = (5/6)^3 \approx 57.87\%$ or ina more generilized form $PW = ((Faces - FacesToLose)/Faces)^N$ where $Faces$ is the array , $FacesToLose$ is the array of 'faces' in which the player will lose the amount summed in the current turn.

But this approach is only giving us the probability of losing and winning if we threw the dice consecutively, not the probability of getting to an objective sum in a single turn, for example if we want to get to a sum of 3 in a single turn we may have to throw the dice 2 times if the outcome of the first throw is a 2 but in the second throw any number besides the 1 will get us to the objective and we'll end the turn there.

So to get the true ***PL*** we'll have to go on a probability tree step by step, for this we'll define ***DN*** as the possible dice result of the $N_{th}$ throw that needed to be done to get to the objective sum, and ***RN*** as the result of the $N_{th}$ throw, but we'll only count the amount of paths that will end in a loss.

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Obj%203.png)

As an example we just look at the objective of 3, if we throw the dice once we have 6 possible outcomes (any of the possible faces) but since we only care about how many times we'll lose then we have 3 different possibilities, we get a **1** and we lose in the firs throw, we get a 2 and we have to do a second throw since this puts the turn sum less than the objective sum, and finally a **4/6** possibility of landing a **3** or more which will make the turn sum greater or equal to the objective sum which should end our turn since we either win and/or achieved the turn objective.

If we look at the probabilities of each of the scenarios of happening, we have the following:

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Obj%203%20(prob).png)

If we sum the probabilities of falling while trying to get a **3** as an objective sum, we have $PL = (1/6) + [(1/6) * (1/6)] = (1/6) + (1/6)^{2} ≈ 16.67 \% + 2.78 \% ≈ 19.44 \%$ which makes the $PW = 1 - PL ≈ 1 - 19.44 \% ≈ 80.56 \%$

we start to see a pattern, but to be sure let's look at the probability trees from objectives 4 through 7.

**Objetive Sum = 4**

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Obj%204.png)

$PL = (1/6) + [2 * (1/6)^{2}] ≈ 22.23 \%$

**Objetive Sum = 5**

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Obj%205.png)

$PL = (1/6) + [3 * (1/6)^{2}] + [1 * (1/6)^{3}] ≈ 25.46 \%$

**Objetive Sum = 6**

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Obj%206.png)

$PL = (1/6) + [4 * (1/6)^{2}] + [3 * (1/6)^{3}] ≈ 29.17 \%$

**Objetive Sum = 7**

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Obj%207.png)

$PL = (1/6) + [5 * (1/6)^{2}] + [6 * (1/6)^{3}] + [1 * (1/6)^{4}] ≈ 33.41 \%$

As we can see the ***PL*** could be generalized as the following equation

$$
PL =  \sum_{i=1}^{N} [(Faces - FacesToLose)^{i-1} * ( \frac{FacesToLose}{Faces})^{i}]
$$

But this will only get us to the first issue of using **N** as the number of consecutive throws and not give us the ***PL*** of the case when we reach the objective we finish the turn, for that we can use the generalized equation but changing the $(Faces - FacesToLose)^{i-1}$ part for an exact number of times a ***DN*** is reached.

For an example we can see a handmade table of the first **10** objective sums for the base case game:

![](https://github.com/Kassoomy/Portfolio/blob/main/Pig%20(Dice%20Game)/Images/Probabilities%20Table%201.png)

So, we can use the number of times an objective sum may get us to throw ***N*** number of dices, for example if we want to check the ***PL*** of objective sum **9** then we'll have:

$$
PL = [1*(1/6)^{1}] + [5*(1/6)^{2}] + [15*(1/6)^{3}] + [10*(1/6)^{4}] + [1*(1/6)^{5}] ≈ 38.28 \%
$$

For us to get these number of possible throws, we can choose many different tools and methods, but we'll tackle it this time with a **recursive function** that'll go through all the possible tree branches and sum their probabilities of getting a losing face to get the ***PL***

## Coding
<sub> Using Python </sub>

We start by importing the libraries we'll use for the current project:

```python
import pandas as pd   # We'll use Pandas to create our table of final results
```

For the base case, which is 1D6 and losing on the 1 face, we'll create an array of all possible faces and losing faces:

```python
faces = range(1, 7, 1)  # Array of numbers from 1 trough 6
facesLose = [1]         # Array with losing faces, in this case just the face with the number 1
```

We create a function which is going to call itself (Recurring Function) which will go through a possibility tree branch till the end of the branch, which means, until the leaf of the branch has only 2 possibilities 1 and 5/6.

The function will take the following parameters:
  1.   **faces:** array of numbers that could come in each 'roll' in the turn
  2.   **facesToLose:** array of numbers that would end the turn
  3.   **lastLevelFace:** the current **DN** result
  4.   **objective:** the objective sum of the turn/game
  5.   **lastLevel:** the last **DN** (i.e. D5 for the 5th thrown dice)
  6.   **currSum:** the sum of the current branch of DNs

with these inputs explained, we can proceed to the function

```python
def checkingProb(faces, facesToLose, lastLevelFace, objective, lastLevel, currSum):
  CurrloseProb = 0          # The Current dice throw (DN) cumulative PL
  currLevel = lastLevel + 1 # The current DN we are throwing

  for currFace in faces:    # We go trough each of the possible faces for the roll
    if currFace in facesToLose: # Check if the  current face is in the losing faces that ends the turn
      CurrloseProb += (len(facesToLose)/len(faces))**lastLevel # Since we eneded the turn we applied the partial part of the generilized formula to the current branch
      continue    # We move to the next face in the possible faces for the roll

    if (currSum + currFace) < objective:  # If we didn't manage to meet the objective, the we need to get to the Next DN and here's where the recursive function comes into play, letting us check the next branch for the current DN
      CurrloseProb += checkingProb(faces, facesToLose, currFace, objective, currLevel, currSum + currFace)

  return CurrloseProb   # We return the sum of all PLs to get the value of the general equation
```

For test, let's calculate the **PL** of objective sum = **9** for the base game of **1D6**

```python
print(f'{checkingProb(faces, facesLose, 0, 9, 1, 0):.4f}')   # We'll use up to 4 decimal points of precision
```
> 0.3828

As we saw we get the same result as the **PL** found by going through the probability tree by hand.

Next we can generate the same table of objectives form **2** to **10** with the following loop.

```python
df = pd.DataFrame(columns=['Objective', 'PL', 'PW'])

for currObjective in range(2, 11, 1):
  currLoseProb = checkingProb(faces, facesLose, 0, currObjective, 1, 0)
  df.loc[currObjective - 2] = [currObjective, str(round(currLoseProb*100,2)) + '%', str(round((1-currLoseProb)*100,2)) + '%']

df = df.set_index('Objective')
df
```

|Objective|PL|PW|
|---|-------|------|
|2	|16.67%	|83.33%|
|3	|19.44%	|80.56%
|4	|22.22%	|77.78%
|5	|25.46%	|74.54%
|6	|29.17%	|70.83%
|7	|33.41%	|66.59%
|8	|35.49%	|64.51%
|9	|38.28%	|61.72%
|10	|40.96%	|59.04%

So, we now have a functioning solution to get the accurate **PL** of the general solution, which means any array of 'faces' and 'losing faces' and for any objective sum.

The only set back of this solution is that we are checking for every single possibility in the possibility tree, which is too computational expensive for objective sums of 40+ in the base case, but for our approach using recursive programing is a good base solution to work on in the future.

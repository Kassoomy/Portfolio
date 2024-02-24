# SuperPermutation Approach
> [!NOTE]
> This project was built in conjunction with [Spjmu](https://github.com/spjmu)

For the background on the problem please refer to the wikipedia page for [Superpermutation](https://en.wikipedia.org/wiki/Superpermutation).

On our approach, first we create an array with all the permutations posible for $N$ elements, for example for 3 elements labled (1, 2, 3) we have:

* 123
* 132
* 213
* 231
* 312
* 321

To begin, we can select any of the **Possible Permutations**. For this illustration, let's commence with the permutation `123`. Using this as our starting point for generating the superpermutation result, our approach involves searching for the next permutation that begins with the last 2 digits of the current result, or more generally, the last $N-i$ digits, where $N$ represents the total number of elements and $i$ denotes the current iteration being checked. Initially, with $i = 1$, we examine the last 2 digits for our example, resulting in $3 - 1 = 2$ digits to consider. Subsequently, we explore the available permutations starting with `23`. In this case, we find `(231)`, as it is the sole option remaining. Utilizing `23` as the overlap to merge with the next permutation `231`, we simply append the last digit to the current result, yielding `1231`. This process can be generalized, indicating the inclusion of the last $i$ digits of the permutation to extend the sequence.

If we follow this same algorithm, we`ll end up with the following steps:

  1. 123 *(initial selection)*
  2. 1231 *(we use `23` to find `231`)*
  3. 12312 *(we use `31` to find `312`)*
  4. 1231213 *(since there's no permutation left that starts with `12` because `123` was already used, we move to the next $i=2$ so then we use the `2` to find `213` and take $i$ last digits and add `13` to the result)*
  5. 12312132 *(we use `13` to find `132`)*
  6. 123121321 *(we use `32` to find `321`)* 

This finishes our final result starting with `123` and getting to `123121321` an answer with length **9**, so with **3** elements it`ll take 9 *"steps"* to go through all possible permutations in a single pass; if we use the minimal possible steps formula, or the lower bound of the solution found by an anonymous poster in 4chan $n! + (n−1)! + (n−2)! + n − 3$ we get  $3! + (3−1)! + (3−2)! + 3 − 3 = 6 + 2 + 1 + 0 = 9$ this shows that our approach for $N=3$ worked since we manage to get an answer which is equal to the theoretical optimal solution. 

now that we have the basis of our approach, we can start programing our algorithm, first we import the needed libraries:

```Python
from itertools import permutations # We use the permutations function to get the possible permutations
import numpy as np                 # We use numpy library for array creation and manipulation
import math                        # We use math for the calculation of the theoretical lower bound
import pandas as pd                # We use pandas for tables of results
```
Define a function to help us remove items from an array, based in the item value and not the position, this is for getting rid of the last used permutation used, as to keep track of the available possible permutations

```python
def removeFromArray(baseArray, searchArray):
    for index in range(len(baseArray)):
        if np.array_equal(baseArray[index], searchArray):
            return np.delete(baseArray, index, 0)

    raise ValueError(f`{searchArray} not in base array`)
```
Now we write a function with our algorithm approach and ask it to print each step so we see the process

```python
def superPermutN (nElements, startingElement, initialPermutations, stepPrint, take):

  n = nElements
  ArrProb = initialPermutations
  ArrCheck = []

  suPP = startingElement
  ArrCheck.append(startingElement)
  ArrProb = removeFromArray(ArrProb, startingElement)
  steps = 1

  while len(ArrProb) != 0:
    steps += 1
    i = 1
    filter = ArrProb[np.sum(ArrProb[:,:(n-i)] == suPP[-(n-i):], axis=1) == (n - i)]

    while len(filter) == 0:
      i += 1
      filter = ArrProb[np.sum(ArrProb[:,:(n-i)] == suPP[-(n-i):], axis=1) == (n - i)]

    suPP = np.append(suPP,filter[0][-i:], axis=0)
    ArrCheck.append(filter[0])
    ArrProb = removeFromArray(ArrProb, filter[0])

    if stepPrint:
      print(f`Step {steps}:  {suPP}`)

  lbn = math.factorial(n) + math.factorial(n - 1) + math.factorial(n - 2) + (n - 3)

  return [ArrCheck[0], len(suPP), steps, suPP]
```
Next we'll use the function for the same scenario of our example with 3 elements

```python
n = 3   # Number of elements
posiblePs = np.array(list(permutations(range(1, n+1))))       # Create the array of posible permutations
tempPerm = superPermutN(n, posiblePs[0], posiblePs, True, 0)  # the result of the algorithm starting with the fist posible permutation `123`

lbn = math.factorial(n) + math.factorial(n - 1) + math.factorial(n - 2) + (n - 3) # The theoretical lower bound of the 

print(f`number of elements: {n}`)
print(f`Theoretical lower bound: {lbn}`)
print(`-`*80)
print(f`Lenght of Answer: {tempPerm[1]}`)
print(f`Number of steps: {tempPerm[2]}`)
print(f`Full Answer: {tempPerm[3]}`)
```
>Step 2:  [1 2 3 1]
>
>Step 3:  [1 2 3 1 2]
>
>Step 4:  [1 2 3 1 2 1 3]
>
>Step 5:  [1 2 3 1 2 1 3 2]
>
>Step 6:  [1 2 3 1 2 1 3 2 1]
>
>number of elements: 3
>
>Theoretical lower bound: 9
>
>---
>
>Lenght of Answer: 9
>
>Number of steps: 6
>
>Full Answer: [1 2 3 1 2 1 3 2 1]
>

As we swee we get the same steps and result as our example. 

Now in the following step we'll take a look at the results for 4 elements when we start with all of the posible permutations ordered by lenght of the answer result:

```python
n = 4
posiblePs = np.array(list(permutations(range(1, n+1))))

dfTemp = pd.DataFrame(columns=['Starting', 'Lenght', 'Steps', 'Answer'])
currRow = 0

lbn = math.factorial(n) + math.factorial(n - 1) + math.factorial(n - 2) + (n - 3)

print(f'Theoretical lower bound: {lbn} \n')

for currPermutation in posiblePs:
  tempPerm = superPermutN(n, currPermutation, posiblePs, False, 0)
  tempRow = []
  tempRow.append(tempPerm[0])
  tempRow.append(tempPerm[1])
  tempRow.append(tempPerm[2])
  tempRow.append(tempPerm[3])
  dfTemp.loc[currRow] = tempRow
  currRow += 1

dfTemp
```

>Theoretical lower bound: 33
>
|index|Starting|Lenght|Steps|Answer|
|---|---|---|---|---|
|0|\[1 2 3 4\]|33|24|\[1 2 3 4 1 2 3 1 4 2 3 1 2 4 3 1 2 1 3 4 2 1 3 2 4 1 3 2 1 4 3 2 1\]|
|2|\[1 3 2 4\]|33|24|\[1 3 2 4 1 3 2 1 4 3 2 1 3 4 2 1 3 1 2 4 3 1 2 3 4 1 2 3 1 4 2 3 1\]|
|4|\[1 4 2 3\]|33|24|\[1 4 2 3 1 4 2 1 3 4 2 1 4 3 2 1 4 1 2 3 4 1 2 4 3 1 2 4 1 3 2 4 1\]|
|6|\[2 1 3 4\]|33|24|\[2 1 3 4 2 1 3 2 4 1 3 2 1 4 3 2 1 2 3 4 1 2 3 1 4 2 3 1 2 4 3 1 2\]|
|8|\[2 3 1 4\]|33|24|\[2 3 1 4 2 3 1 2 4 3 1 2 3 4 1 2 3 2 1 4 3 2 1 3 4 2 1 3 2 4 1 3 2\]|
|10|\[2 4 1 3\]|33|24|\[2 4 1 3 2 4 1 2 3 4 1 2 4 3 1 2 4 2 1 3 4 2 1 4 3 2 1 4 2 3 1 4 2\]|
|12|\[3 1 2 4\]|33|24|\[3 1 2 4 3 1 2 3 4 1 2 3 1 4 2 3 1 3 2 4 1 3 2 1 4 3 2 1 3 4 2 1 3\]|
|14|\[3 2 1 4\]|33|24|\[3 2 1 4 3 2 1 3 4 2 1 3 2 4 1 3 2 3 1 4 2 3 1 2 4 3 1 2 3 4 1 2 3\]|
|1|\[1 2 4 3\]|34|24|\[1 2 4 3 1 2 4 1 3 2 4 1 2 3 4 1 2 1 3 4 2 1 3 1 4 2 3 1 4 3 2 1 4 3\]|
|3|\[1 3 4 2\]|34|24|\[1 3 4 2 1 3 4 1 2 3 4 1 3 2 4 1 3 1 2 4 3 1 2 1 4 3 2 1 4 2 3 1 4 2\]|
|5|\[1 4 3 2\]|34|24|\[1 4 3 2 1 4 3 1 2 4 3 1 4 2 3 1 4 1 2 3 4 1 2 1 3 4 2 1 3 2 4 1 3 2\]|
|7|\[2 1 4 3\]|35|24|\[2 1 4 3 2 1 4 2 3 1 4 2 1 3 4 2 1 2 3 4 1 2 3 1 2 4 3 1 2 4 1 3 2 4 1\]|
|9|\[2 3 4 1\]|35|24|\[2 3 4 1 2 3 4 2 1 3 4 2 3 1 4 2 3 1 2 4 3 1 2 1 4 3 2 1 4 1 3 2 4 1 3\]|
|11|\[2 4 3 1\]|35|24|\[2 4 3 1 2 4 3 2 1 4 3 2 4 1 3 2 4 1 2 3 4 1 2 1 3 4 2 1 3 1 4 2 3 1 4\]|
|13|\[3 1 4 2\]|35|24|\[3 1 4 2 3 1 4 3 2 1 4 3 1 2 4 3 1 2 3 4 1 2 3 2 4 1 3 2 4 2 1 3 4 2 1\]|
|15|\[3 2 4 1\]|35|24|\[3 2 4 1 3 2 4 3 1 2 4 3 2 1 4 3 2 1 3 4 2 1 3 1 4 2 3 1 4 1 2 3 4 1 2\]|
|16|\[3 4 1 2\]|35|24|\[3 4 1 2 3 4 1 3 2 4 1 3 4 2 1 3 4 2 3 1 4 2 3 1 2 4 3 1 2 1 4 3 2 1 4\]|
|17|\[3 4 2 1\]|35|24|\[3 4 2 1 3 4 2 3 1 4 2 3 4 1 2 3 4 1 3 2 4 1 3 1 2 4 3 1 2 1 4 3 2 1 4\]|
|18|\[4 1 2 3\]|35|24|\[4 1 2 3 4 1 2 4 3 1 2 4 1 3 2 4 1 3 4 2 1 3 4 2 3 1 4 2 3 2 1 4 3 2 1\]|
|19|\[4 1 3 2\]|35|24|\[4 1 3 2 4 1 3 4 2 1 3 4 1 2 3 4 1 2 4 3 1 2 4 2 3 1 4 2 3 2 1 4 3 2 1\]|
|20|\[4 2 1 3\]|35|24|\[4 2 1 3 4 2 1 4 3 2 1 4 2 3 1 4 2 3 4 1 2 3 4 1 3 2 4 1 3 1 2 4 3 1 2\]|
|21|\[4 2 3 1\]|35|24|\[4 2 3 1 4 2 3 4 1 2 3 4 2 1 3 4 2 1 4 3 2 1 4 1 3 2 4 1 3 1 2 4 3 1 2\]|
|22|\[4 3 1 2\]|35|24|\[4 3 1 2 4 3 1 4 2 3 1 4 3 2 1 4 3 2 4 1 3 2 4 1 2 3 4 1 2 1 3 4 2 1 3\]|
|23|\[4 3 2 1\]|35|24|\[4 3 2 1 4 3 2 4 1 3 2 4 3 1 2 4 3 1 4 2 3 1 4 1 2 3 4 1 2 1 3 4 2 1 3\]|

We have 8 starting permutations that give us the optimal answer for $N=4$ but gf course we'll need a way to make sure our solution is one of the valid solutions to the problem, for this we create a function that'll check if each of the posible permutations was used, we go even one step further toget the number of times each permutation is used, since an optimal solution should use each of the posible permutations only once.

```python
def megaArrayComparison(baseArray, comparisonArray, numberOfElements):
  countOfInstances = 0

  for index in range(len(baseArray)-numberOfElements+1):
    if np.array_equal(baseArray[index:numberOfElements+index], comparisonArray):
      countOfInstances += 1

  return countOfInstances
```

For even more verification, we can check the reverse which will be the permutations of  N  elements that can be found in the final result but are not part of the possible permutations, in this case we check that all of the resulting *"Fake"* permutations are combinations of repeating elements and not a permutation without repeating elements.

```python
def reversedComparison(posibleArray, comparisonArray, numberOfElements):
  falsePerms = []

  for index in range(len(comparisonArray)-numberOfElements+1):
    currentPerm = comparisonArray[index:numberOfElements+index]
    found = False

    for perm in posibleArray:
      if np.array_equal(currentPerm, perm):
        found = True
        break

    if found == False:
      falsePerms.append(currentPerm)

  return falsePerms
```

Now we can do a loop trough all possible permutations and go through the answer $N$ digits at a time to check if we have every possible permutation accounted in our answer  

```python
for perm in posiblePs:
  print(f'permutation {perm} was found {megaArrayComparison(tempPerm[3], perm, n)} times')
```
>permutation [1 2 3 4] was found 1 times
>
>permutation [1 2 4 3] was found 1 times
>
>permutation [1 3 2 4] was found 1 times
>
>permutation [1 3 4 2] was found 1 times
>
>permutation [1 4 2 3] was found 1 times
>
>permutation [1 4 3 2] was found 1 times
>
>permutation [2 1 3 4] was found 1 times
>
>permutation [2 1 4 3] was found 1 times
>
>permutation [2 3 1 4] was found 1 times
>
>permutation [2 3 4 1] was found 1 times
>
>permutation [2 4 1 3] was found 1 times
>
>permutation [2 4 3 1] was found 1 times
>
>permutation [3 1 2 4] was found 1 times
>
>permutation [3 1 4 2] was found 1 times
>
>permutation [3 2 1 4] was found 1 times
>
>permutation [3 2 4 1] was found 1 times
>
>permutation [3 4 1 2] was found 1 times
>
>permutation [3 4 2 1] was found 1 times
>
>permutation [4 1 2 3] was found 1 times
>
>permutation [4 1 3 2] was found 1 times
>
>permutation [4 2 1 3] was found 1 times
>
>permutation [4 2 3 1] was found 1 times
>
>permutation [4 3 1 2] was found 1 times
>
>permutation [4 3 2 1] was found 1 times

So when we start with `1234` and $N=4$ we have an optimal solution with every permutation possible being used once. 

Now we can further our check with the reverse search and find the *"Fake"* permutations

```python
reversedComparison(posiblePs, tempPerm[3], n)
```
>[array([1, 2, 3, 1]),
 array([2, 3, 1, 2]),
 array([3, 1, 2, 1]),
 array([1, 2, 1, 3]),
 array([2, 1, 3, 2]),
 array([1, 3, 2, 1])]

All the *"Fake"* permutations contain duplicated elements and there aren't a lot of them, the less we have of these permutations the more optimal or close to optimal our solution is.

To finish we can tackle the current issue with this approach, besides it being brute force since you need to go through every starting premutation to know which one is better also there are points that we call *bifurcations* where the algorithm gets to a step where multiple permutations are possible and then it'll need an strategy on which of those to choose; on our initial approach we always select the first possible permutation

```python
suPP = np.append(suPP,filter[0][-i:], axis=0)
```

If we change the `filter[0]` for `filter[-1]` to get the last possible permutation, it'll give us different results, so the strategy to get the optimal decisions in each of this scenarios will be the key to find optimal solutions for $N=5$ an onwards, for now the best solution our base algorithm gives for $N=5$ has length `153` while the theoretical lower bound is `152`

|index|Starting|Lenght|Steps|Answer|
|---|---|---|---|---|
|0|\[1 2 3 4 5\]|153|120|\[1 2 3 4 5 1 2 3 4 1 5 2 3 4 1 2 5 3 4 1 2 3 5 4 1 2 3 1 4 5 2 3 1 4 2 5 3 1 4 2 3 5 1 4 2 3 1 5 4 2 3 1 2 4 5 3 1 2 4 3 5 1 2 4 3 1 5 2 4 3 1 2 5 4 3 1 2 1 3 4 5 2 1 3 4 2 5 1 3 4 2 1 5 3 4 2 1 3 5 4 2 1 3 2 4 5 1 3 2 4 1 5 3 2 4 1 3 5 2 4 1 3 2 5 4 1 3 2 1 4 5 3 2 1 4 3 5 2 1 4 3 2 5 1 4 3 2 1 5 4 3 2 1\]|

# Feature Engineering
> [!NOTE]
> This is an Evolutionary algorithm aplication, more specifically an aplicaction of a Genetic Algorithm for feature selection in a Decision Tree model

For the base of this excercise, we'll use the following [Kaggle Job Data](https://www.kaggle.com/datasets/murilozangari/jobs-and-salaries-in-data-field-2024?resource=download) from 2024 [Jobs and Salaries in Data Science](https://www.kaggle.com/datasets/hummaamqaasim/jobs-in-data/data) 

we'll use the following variables as the base for the decision tree as to have an easier to plot result:

- work_year
  
- experience_level (One Hot encoded)
  -  Entry-level
  -  Executive
  -  Senior
  -  Mid-level
    
- job_category (One Hot encoded)
  - Job-Data-Science-and-Research
  - Job-BI-and-Visualization
  - Job-Data-Architecture-and-Modeling
  - Job-Data-Analysis
  - Job-Data-Engineering
  - Job-Leadership-and-Management
  - Job-Data-Quality-and-Operations
  - Job-Machine-Learning-and-AI
  - Job-Cloud-and-Database
  - Job-Data-Management-and-Strategy

- salary_currency (Changed for either USD [1] or not USD [0])
  
- employment_type  (One Hot encoded)
  - Freelance
  - Full-time
  - Contract
  - Part-time

- company_size  (One Hot encoded)
  - Size-S
  - Size-M
  - Size-L

- company_location (One Hot encoded)
  - Company-Locate-USA
  - Company-Locate-UK
  - Company-Locate-Canada
  - Company-Locate-Other


And use the  `work_setting` as the independent variable, with just **2** values ***Remote (1)*** and ***In-person (0)***

## Code
<sub> Using Python </sub>

First we import the libraries for the project
```python
import pandas as pd
import numpy as np
import random
import matplotlib.pyplot as plt

from sklearn.tree import DecisionTreeClassifier # Import Decision Tree Classifier
from sklearn.model_selection import train_test_split # Import train_test_split function
from sklearn import metrics #Import scikit-learn metrics module for accuracy calculation

# Libraries for displaying and saving the detailed Decision Tree
from sklearn.tree import export_graphviz
from six import StringIO
from IPython.display import Image, display
import pydotplus
```

We set up our `SkLearn` decision tree model for quick set up 
```python
 # Create Decision Tree classifer object
model = DecisionTreeClassifier()
```

first we define the initial population of columns or features to use, each genome is an array of ***True*** and ***False** eith the lenght of all posible features and if it's a  ***True***  the feature will be included in the dercision tree

```python
def boolArrGen(sizeR):
    initial_arr = [True, False]
    arr = [False]

    while sum(arr) <= 0:
        arr = np.random.choice(initial_arr, size=sizeR)

    bool_arr = list(map(bool, arr))

    return bool_arr
```

Next we define a curated application of the decision tree model, where we'll change the columns or features to be used based on the **population** fo **genomes** that we have at the moment, and test each of the **individuals** or set of features

```python
def decisionTreeGenerator(criteria, plot, dfx, dfy):

    genome2Use = [bool(item) for item in criteria]
    col2Use = dfx[dfx.columns[genome2Use]].columns
    X = dfx[col2Use]
    y = dfy

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=1)

    # Train Decision Tree Classifer
    model.fit(X_train,y_train)

    #Predict the response for test dataset
    y_pred = model.predict(X_test)

    if plot:
        dot_data = StringIO()
        export_graphviz(model, out_file=dot_data,
                        filled=True, rounded=True,
                        special_characters=True,feature_names = col2Use,class_names=['Remote','In-Person'])
        graph = pydotplus.graph_from_dot_data(dot_data.getvalue())
        # graph.write_png('DecisionTreeAgent.png')
        img = Image(graph.create_png())
        display(img)

    return metrics.accuracy_score(y_test, y_pred)
```

The we define our fitsness score, which will order thhe population from the best result to the worst, so the 2 first genomes of the population are the 2 best individuals, that we can use in the future to create *"offspring"* to progress the accuracy

```python
def fitness_score(iterations, dfx, dfy) :
    global populations, best
    fit_value = []
    fit_score=[]

    for i in range(iterations):
        chromosome_value = boolArrGen(lenGenome)
        fit_value.append(decisionTreeGenerator(chromosome_value, False, dfx, dfy))

    fit_value, populations = zip(*sorted(zip(fit_value, populations) , reverse = True))
    best= fit_value[0]
```

A function for selecting the parents, in this case 2 for the *"offspring"* 

```python
def selectparent():
    global parents
    parents=populations[0:2]
```

Finaly with the population order by fitness and the parent selected we can do the cross over of parents

```python
def crossover() :
    global parents

    while sum(parents[0]) <= 0 and sum(parents[1]) <= 0:
        cross_point = random.randint(0,lenGenome)
        parents = parents + tuple([(parents[0][0:cross_point +1] + parents[1][cross_point+1:lenGenome])])
        parents = parents + tuple([(parents[1][0:cross_point +1] + parents[0][cross_point+1:lenGenome])])
```

Also we want there to be some random **mutation** so we are not stuck in a local optimal solution and not get close to the global optimal solution

```python
def mutation() :
    global populations, parents
    mute = random.randint(0,49)
    if mute == 20 :
        x = random.randint(0, 1)
        y = random.randint(0,lenGenome-1)
        parents[x][y] = 1-parents[x][y]

        while parents[x][y] <= 0:
            x = random.randint(0,1)
            y = random.randint(0,lenGenome-1)
            parents[x][y] = 1-parents[x][y]

    populations = parents
```



>Incumbent solution found after 1001 generations: Accuracy 0.6796949475691134 with Genome
>[False, True, True, True, False, False, False, True, False, >False, True, False, True, False, True, True, False, False, True, True, False, False, True, True, False, True, False]
>Refering to the vairables ['Entry-level' 'Executive' 'Senior' 'Contract' 'Company-Locate-USA'
> 'Company-Locate-Canada' 'Size-M' 'Size-L' 'Job-BI-and-Visualization'
> 'Job-Data-Architecture-and-Modeling' 'Job-Leadership-and-Management'
> 'Job-Data-Quality-and-Operations' 'Job-Cloud-and-Database']


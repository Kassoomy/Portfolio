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





# Helpdesk-Ticket-Auto-Categorization

Full notebook available [here](https://github.com/dpowell022/Helpdesk-Ticket-Auto-Categorization/blob/main/Helpdesk%20Ticket%20Classifier.ipynb)

Detailed project report available [here](https://github.com/dpowell022/Helpdesk-Ticket-Auto-Categorization/blob/main/Capstone%203%20-%20Project%20Report.docx)

High-level presentation [here](https://github.com/dpowell022/Helpdesk-Ticket-Auto-Categorization/blob/main/Capstone%203%20-%20Presentation.pptx)
***
### Business problem: 
***
I've worked in technical support for 10 years and in that world, identifying patterns and trends reported by your users is critical for understanding solutioning gaps and making a plan towards addressing them. While some patterns and trends are easy to see, especially at a micro level, it can become problematic to perform quality identification at a macro level. 

For this reason, most ticketing (or CRM) software used by support desks includes drop-down selection fields for agents to categorize the nature of the issue within the ticket. However, this presents a set of key challenges: 
 - As the complexity of the solution grows, there arises more and more categories of interest, and there comes more and more hierarchical selections, and the complexity of simply categorizing the issue grows. 
 - As the complexity of categorizing the issue grows, so too does the cost of training help desk agents as well as likelihood of incorrect selections.
For large companies with complex solutions, categorizing every ticket accurately presents a very high training cost and even still then a high probability of miscategorization. 

This project sought to create a machine learning model which can evaluate features within a help desk ticket, including user commentary, and automatically (and accurately) classify the issue. Such a data product would greatly improve the value of this data in the hands of Product, Development, and Support organizations and would greatly reduce training costs. 

***
### Acquiring and cleaning the data
***
 - Data taken from real tickets logged by customers
 - Dropped features from the dataset deemed irrelevant to machine learning
 - Used domain knowledge to impute values or drop rows where missing values existed
 - Curbed outliers, where appropriate for the business problem
 - Clean text fields of special characters, stop words, and move to lowercase
 - Convert target variables into a single categorical feature with over 70 classes

***
### Feature engineering and Pre-processing
***
 - Added feature to define whether a ticket was resolved by 'Tier1' or 'Tier2'
 - Vectorized freeform text into a word corpus, applying 1-3 word n-grams for contextual value add
 - Split data into 75/25 parts for training and testing
 
***
### Modeling
***
 - Focused on 4 models: Random Forest, Gradient Boosting, Logistic Regression, LinearSVC
 - Trained each model both the vocabulary and categorical variables, only the vocabulary, and only the categorical variables
 - LinearSVC with only the vocabulary as training data performed the best
 - Performed HalvingGridSearch hyper param tuning on LinearSVC, resulted in out-of-the-box params as best-performing

***
### Conclusions and future scope
***
In the goal of taking helpdesk ticket data and classifying it into the available labels for categorization, this project was not as successful as I had hoped. Success was limited by the lack of text data cleanliness; additionally, the huge number of target prediction classes meant that even this large ~10K-row dataset was not able to allow for sufficient model training on some unrepresented classes. 

The freeform text fields (Problem Notes, Action Notes) appear to hold the most value in pattern identification, and if further pre-processing was done to clean up the content of them, their value could be recognized even further. Even still, the scores were not too bad for about half of the classes, and it is likely that performance can be improved even further via implementation of the steps listed in Future Scope: 

1. Larger dataset to give more rows for each class in the target variable
2. Try more n-grams, such as 3 or even 4
3. Reduce target classes to fewer selections to improve scores for remaining classes
4. Neural net model
5. Better text pre-processing of Action Notes, which are cluttered with irrelevant words, e.g. email headers, agent IDs, date/times, etc
6. Build a set of sequential classifiers, one for Prod_cat1, then, for each available Prod_cat1 selection, build another to predict that class' Prod_cat2, then repeat for Prod_cat3. This would require well over 100 classifier models, which could be trained in an iterative loop

# Helpdesk-Ticket-Auto-Categorization

### Business problem: 
I've worked in technical support for 10 years and in that world, identifying patterns and trends reported by your users is critical for understanding solutioning gaps and making a plan towards addressing them. While some patterns and trends are easy to see, especially at a micro level, it can become problematic to perform quality identification at a macro level. 

For this reason, most ticketing (or CRM) software used by support desks includes drop-down selection fields for agents to categorize the nature of the issue within the ticket. However, this presents a set of key challenges: 
 - As the complexity of the solution grows, there arises more and more categories of interest, and there comes more and more hierarchical selections, and the complexity of simply categorizing the issue grows. 
 - As the complexity of categorizing the issue grows, so too does the cost of training help desk agents as well as likelihood of incorrect selections.
For large companies with complex solutions, categorizing every ticket accurately presents a very high training cost and even still then a high probability of miscategorization. 

This project sought to create a machine learning model which can evaluate features within a help desk ticket, including user commentary, and automatically (and accurately) classify the issue. Such a data product would greatly improve the value of this data in the hands of Product, Development, and Support organizations and would greatly reduce training costs. 

### Acquiring and cleaning the data
Source data was taken from real tickets logged by helpdesk agents and reported by customers. A data extract was pulled from an internal server and cleaning began. The dataset I worked with was laden with features, representing each field found within a ticket, including discrete variables, continuous variables, and date/time objects. I had to drop unnecessary features, impute or drop missing values where applicable, and curb outliers where appropriate to preserve model training integrity. I also cleaned the text fields to lowercase all words and drop special characters.

My target variable was the multi-class categorical variable for 'product category'. I merged this multi-tiered categorical variable into a single multi-class variable, giving me over 100 target classes. I reduced this a bit by dropping any classes which had less than 25 instances in the dataset, but the task would still involve predicting correctly from over 70 classes.

### Feature engineering and Pre-processing
I added several new features based on information from existing features, such as breaking out the 'Team group' (or responsible party of helpdesk agents who fixed the issue) into a Tier1 and Tier2 category, as well as running the word corpus through a TF-IDF vectorizer, also applying 2 and 3 n-grams to generate better contextual value, and dropping n-grams that appeared less than 10 times. I finished pre-processing with a 75/25 split of training/test data.

### Modeling
I ran my training data through 4 models out of the box to benchmark initial scores: random forest, gradient boosting, logistic regression, and SVM linear SVC. My model choices were partially dictated by multi-class support and partially by my limited computational resources. Of those 4 models, grad boost failed to complete training after 2 hours and I abandoned it, and logistic regression AND SVM failed to converge. 

I then tried training the models again, once using ONLY the vocabulary features, and once using ONLY the NON-vocabulary features. In all cases, the models trained with only the vocabulary features outperformed all others. SVM Linear SVC performed the best. 

Taking, then, the best-performing model, I performed hyperparameter tuning using HalvingGridSearch (limited computational cost) and found the out-of-the-box params performed best.

### Conclusions and future scope
In the goal of taking helpdesk ticket data and classifying it into the available labels for categorization, this project was not as successful as I had hoped. Success was limited by the lack of text data cleanliness; additionally, the huge number of target prediction classes meant that even this large ~10K-row dataset was not able to allow for sufficient model training on some unrepresented classes. 

The freeform text fields (Problem Notes, Action Notes) appear to hold the most value in pattern identification, and if further pre-processing was done to clean up the content of them, their value could be recognized even further. Even still, the scores were not too bad for about half of the classes, and it is likely that performance can be improved even further via implementation of the steps listed in Future Scope: 

1. Larger dataset to give more rows for each class in the target variable
2. Try more n-grams, such as 3 or even 4
3. Reduce target classes to fewer selections to improve scores for remaining classes
4. Neural net model
5. Better text pre-processing of Action Notes, which are cluttered with irrelevant words, e.g. email headers, agent IDs, date/times, etc
6. Build a set of sequential classifiers, one for Prod_cat1, then, for each available Prod_cat1 selection, build another to predict that class' Prod_cat2, then repeat for Prod_cat3. This would require well over 100 classifier models, which could be trained in an iterative loop

# CreditMateHiring
Hiring Challenge for CreditMate

### The Problem

The dataset was fairly trivial in size of only ~14k points in training and ~5k points in test set having two columns. One is the the free flowing text named `comments` and the other is class label named `EVENT_TYPE`. We need to featurize the text data and adopt a multiclass classification setting to predict the class labels. Few things that were not mentioned are the following
	
- Latency Requirement
- Interpretibility Requirement

### The Business

The busimess requirement is simple. There are I guess multiple collection agent who intends to collect the money from different lenders and based on their expreience with those client, they need to write some comment in a text section and based on those comments, there needs to be certain action taken. Now those collecting agents are non techical people and that's why they were given a free flowing text field to write down their response which can be further used for some other business purposes. Hence there comes a requirement to convert the free flowing text into appropriate event types automaticlly so that some other systems (May be rating a lender into defaulter/star) can be automated.

### Techical Insights

These are the following technical insights that I have got from the EDA.

- There are 7 class labels and hence multicalss classification problem
- Some class labels are very less in number and hence it is problem with with imbalanced dataset.
- In this scenario, where multiclass classifcation with imbalanced dataset, I always use [**multicass-logloss**](http://wiki.fast.ai/index.php/Log_Loss)
- Distribution of words came out to be interesting and very defining for the class labels and hence simple bag of words would be good.
- This is a very high dimensional dataset and hence simpler linear models would be better.

### Approach

- First cleaned the dataset to remove punctuations and made all the letter small to improve the readability.
- Didn't remove any stopwords based on my hunch.
- Didn't perform any stemming or lemmatization also based on hunch.
- Used upto 3 gram count vectorizer to fit the data with a [`Logistic Regression`](https://en.wikipedia.org/wiki/Logistic_regression) and got 89.51% validation accuracy.
- To Improve the performace of class label 3, I used some hand crafted feature engineering but that worked only to improve the accuracy to 89.58% but not the log-loss.

### Businedss Insights

- There seems to be some confusion labeling `RINGING` and `NOT_CONTACTABLE`. It has to be more robust.
- `HARDIK KADAKIA` seems to be more associated with defautler than usual customers.
- There seems to be some confusion again to put someone in `RINGING` and `FOLLOW_UP`.
- Some comments are exactly same as event type.
- Events that are marked `PAID` seems more uniformly commented than other events which seems logical.

### What Worked

- `CountVectorizer` with upto 3 grams worked well.
- Decision of using multiclass logloss worked well.
- Simpler Linear Model worked well.
- Handcrafted features to improve the performace on class label 3 worked slightly well.

### What Didn't
- Feature Engineering didn't have huge effect.
- Scaling the data incresed the logloss
- Stacking with 1gram TFIDF didn't work well
- Fitting a Tree based model with model based feature selection didn't work well as expected.

### Results

| Algorithm           | Model                                                           | Dimension | Train Accuracy | Test Accuracy |
|---------------------|-----------------------------------------------------------------|-----------|----------------|---------------|
| Logistic Regression | Upto 3grams BOW                                                 | 10339     | 0.924274       | 0.895182      |
| Logistic Regression | Upto 3grams BOW + Engineered Features                           | 10342     | 0.923858       | 0.895895      |
| Logistic Regression | Upto 3grams BOW + Engineered Features +1gram TFIDF + Centering  | 11511     | 0.925164       | 0.891086      |
| LightGBM            | Upto 3grams BOW + Model based feature selection                 | 572       | 0.892332       | 0.875679      |

### Reflection

- With mode data, we can model the sequence information using a simple LSTM and softmax layer at the output.
- We can also use Association Rules to increse the performace with existing models; a gentle approch towards stacking a Linear and Bayesian Model.
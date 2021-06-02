# IntentClassification
A Machine Learning model for text based multi-label Intent Classification of code review questions 

### Problem description
Code review is a systematic examination of a source code produced by a person or a group of people, to find bugs, give suggestions, make clarifications, and hence evaluate the quality of the code. In this process, there will be one or more presenters, who will be facing questions or suggestions from a panel. The dataset used here, emerged from a case study aimed at investigating the communicative intention of developers’ questions during the code review process. The challenge is to extract the necessary information from the bunch of questions so as to come up with a proper conclusion about the intention of the questions and hence create a model which predicts the same with a plausible accuracy.

### About the dataset
The [dataset](https://github.com/naveena-as/IntentClassification/raw/main/dataset.xlsx) is obtained from an exploratory case study done as part of a research paper : [Communicative Intention in Code Review Questions](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiLmZeW1_jwAhVl9XMBHS9FAHMQFjAAegQIAxAD&url=https%3A%2F%2Fwww.win.tue.nl%2F~aserebre%2FICSME2018NIER.pdf&usg=AOvVaw3wCsZN4EKvrFzuFFXUkQG6), involving 399 Android code reviews, from which 499 questions have been extracted and fed into an Excel sheet. It has the following 4 fields:
- `inline-comment-id`
- `#Comment`
- `Question` : question/statement raised by the reviewer
- `Final Label` : intention of the question/statement (Target variable)

The intentions are broadly classified into 5, with further subdivisions summing up to 9 and 2 additional subdivisions, making it a total of 14. We have considered only the broad classifications and hence converted the attribute `Final Label` to contain only the intial level of classification with 5 subcategories, namely Suggestions, Requests, Attitudes and emotions, Hypothetical Scenario, Rhetorical Question. This converted category is fed as a new attribute `New Label`. See the figure for a better understanding:

![QuestionTree](https://user-images.githubusercontent.com/67685791/120459925-44c29880-c3b6-11eb-8d00-223e193c6e9a.png)

### Feature extraction
The attribute `Questions` is taken as the input variable and `New Label` as the output variable. Distribution of the output variable (number of samples per label) can be seen here:

![class_distribution](https://user-images.githubusercontent.com/67685791/120470411-1c8c6700-c3c1-11eb-8231-995bb12baf94.png)

Since the input variable is in textual form and not numerical, certain preprocessing steps are required to be done before training the model with it. The output variable can be fed to the model either directly, or after encoding it into numerical values using encoders such as the [Label Encoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.LabelEncoder.html). Here, we have fed them directly.

### Text preprocessing
The preprocessing of the text has been done using two approaches:
 - Approach 1 :
      - Expanding the contractions (e.g: haven’t --> have not)
      - Conversion of text to word sequence (includes conversion to lower case)
      - Removal of numbers and punctuations
 
 - Approach 2 :
      - Conversion to lower case
      - Removal of numbers and punctuations
      - [Tokenisation](https://www.tutorialspoint.com/python_data_science/python_word_tokenization.htm) : converts the sentence into a list of words
      - [Lemmatisation](https://www.geeksforgeeks.org/python-lemmatization-with-nltk/) : converts each word to its root form

These approaches are done on the input variable and the results are stored in a new attribute `Question Words`.

We are required to add new features to train and test the model so as to enhance its accuracy. For this purpose, we have introduced new columns related to the attribute “Questions”:
- `excl_marks`  : count of exclamation marks
- `qstn_marks`  : count of question marks
- `puncts`      : count of punctuations like .,:;
- `symbols`     : count of other symbols like *&$%=/
- `word_count`  : number of words
Out of these, `word_count` and `excl_marks` have been included as features, in addition to the preprocessed text column `Question Words`. These have been chosen because they contribute the most to the emotion in a statement/question, compared to other symbols. You can experiment training the model with other features as well. But here, the vector for training consists of only three attributes – `Question Words`, `excl_marks` and `word_count`.

### Packages used
To convert `Question Words` to numerical vectors, the [TF-IDF vectoriser](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html) from sklearn is used, which assigns a particular numerical value to each word, signifying its importance.
For text preprocessing, the package [nltk](https://www.nltk.org/), [keras](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/text/text_to_word_sequence), [contractions](https://www.geeksforgeeks.org/nlp-expand-contractions-in-text-processing/), [string](https://docs.python.org/3/library/string.html) (for removing punctuations), [re](https://docs.python.org/3/library/re.html) (Regular Expressions) have been used. For training, testing and accuracy analysis of the model, [sklearn](https://scikit-learn.org/stable/model_selection.html#model-selection) has been used.

### Model fitting and goodness
For each approach, the model is fit to three different classifiers - [Linear SVM](https://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html), [Random Forest Classifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html), [Logistic Regression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html) - among which Linear SVM performed the best in both the approaches.

_NOTE : If any errors are raised while importing the modules from nltk, it might be because explicit download of the modules are required. In such cases, try the following code in your Jupyter notebook:_
```bash
import nltk
nltk.download('stopwords')
nltk.download('wordnet')
 ```

---
interact_link: content/appendix/pipeline.ipynb
title: 'Appendix'
prev_page:
  url: /analysis/topicmodels
  title: 'Topic Models'
next_page:
  url: /placeholder
  title: 'Pipelines'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---



{:.input_area}
```python
%matplotlib inline

import pandas as pd

pd.set_option('display.max_colwidth', 120)
```




{:.input_area}
```python
wine_df_full = pd.read_csv('data/wine_reviews.csv')

# let us reduce down our dataset so that it more manageable. 
wine_df = wine_df_full.sample(n = 10000)
```




{:.input_area}
```python
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.neighbors import KNeighborsClassifier

from sklearn.pipeline import Pipeline
from sklearn.model_selection import GridSearchCV

from sklearn.metrics import classification_report, confusion_matrix, accuracy_score
```




{:.input_area}
```python
pipeline = Pipeline([
                     ('vectorizer' , CountVectorizer()),
                     ('classifier' , KNeighborsClassifier())
                    ])

parameters = {'vectorizer__max_features' : [300, 500, 700],
              'classifier__n_neighbors' : [2,3, 5] }


```




{:.input_area}
```python
grid        = GridSearchCV(pipeline,
                           parameters,
                           return_train_score = True,
                           cv = 3,
                           n_jobs = -1, 
                           verbose = 2)
```




{:.input_area}
```python
grid.fit(wine_df['description'], wine_df['rating'])
```


{:.output .output_stream}
```
Fitting 3 folds for each of 9 candidates, totalling 27 fits
[CV] classifier__n_neighbors=2, vectorizer__max_features=300 .........
[CV] classifier__n_neighbors=2, vectorizer__max_features=300 .........
[CV] classifier__n_neighbors=2, vectorizer__max_features=300 .........
[CV] classifier__n_neighbors=2, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=300, total=   4.3s
[CV] classifier__n_neighbors=2, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=300, total=   4.8s
[CV]  classifier__n_neighbors=2, vectorizer__max_features=300, total=   4.6s
[CV] classifier__n_neighbors=2, vectorizer__max_features=500 .........
[CV] classifier__n_neighbors=2, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=500, total=   4.7s
[CV] classifier__n_neighbors=2, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=500, total=   3.2s
[CV] classifier__n_neighbors=2, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=700, total=   3.2s
[CV] classifier__n_neighbors=3, vectorizer__max_features=300 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=700, total=   3.2s
[CV] classifier__n_neighbors=3, vectorizer__max_features=300 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=500, total=   3.1s
[CV] classifier__n_neighbors=3, vectorizer__max_features=300 .........
[CV]  classifier__n_neighbors=2, vectorizer__max_features=700, total=   3.0s
[CV] classifier__n_neighbors=3, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=300, total=   3.2s
[CV] classifier__n_neighbors=3, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=300, total=   3.2s
[CV] classifier__n_neighbors=3, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=300, total=   3.0s
[CV] classifier__n_neighbors=3, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=500, total=   3.3s
[CV] classifier__n_neighbors=3, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=500, total=   3.3s
[CV] classifier__n_neighbors=3, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=500, total=   3.3s
[CV] classifier__n_neighbors=5, vectorizer__max_features=300 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=700, total=   3.3s
[CV] classifier__n_neighbors=5, vectorizer__max_features=300 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=700, total=   3.4s
[CV] classifier__n_neighbors=5, vectorizer__max_features=300 .........
[CV]  classifier__n_neighbors=3, vectorizer__max_features=700, total=   3.5s
[CV] classifier__n_neighbors=5, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=5, vectorizer__max_features=300, total=   3.6s
[CV] classifier__n_neighbors=5, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=5, vectorizer__max_features=300, total=   3.7s
[CV] classifier__n_neighbors=5, vectorizer__max_features=500 .........
[CV]  classifier__n_neighbors=5, vectorizer__max_features=300, total=   3.7s
[CV] classifier__n_neighbors=5, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=5, vectorizer__max_features=500, total=   3.9s
[CV] classifier__n_neighbors=5, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=5, vectorizer__max_features=500, total=   3.6s
[CV]  classifier__n_neighbors=5, vectorizer__max_features=500, total=   3.5s
[CV] classifier__n_neighbors=5, vectorizer__max_features=700 .........
[CV]  classifier__n_neighbors=5, vectorizer__max_features=700, total=   3.4s
[CV]  classifier__n_neighbors=5, vectorizer__max_features=700, total=   3.2s
[CV]  classifier__n_neighbors=5, vectorizer__max_features=700, total=   2.8s

```




{:.output .output_data_text}
```
GridSearchCV(cv=3, error_score='raise',
       estimator=Pipeline(memory=None,
     steps=[('vectorizer', CountVectorizer(analyzer='word', binary=False, decode_error='strict',
        dtype=<class 'numpy.int64'>, encoding='utf-8', input='content',
        lowercase=True, max_df=1.0, max_features=None, min_df=1,
        ngram_range=(1, 1), preprocessor=None, stop_words=None,
       ...owski',
           metric_params=None, n_jobs=1, n_neighbors=5, p=2,
           weights='uniform'))]),
       fit_params=None, iid=True, n_jobs=-1,
       param_grid={'vectorizer__max_features': [300, 500, 700], 'classifier__n_neighbors': [2, 3, 5]},
       pre_dispatch='2*n_jobs', refit=True, return_train_score=True,
       scoring=None, verbose=2)
```





{:.input_area}
```python
pd.DataFrame(grid.cv_results_).sort_values(by = 'rank_test_score')
```





<div markdown="0" class="output output_html">
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mean_fit_time</th>
      <th>mean_score_time</th>
      <th>mean_test_score</th>
      <th>mean_train_score</th>
      <th>param_classifier__n_neighbors</th>
      <th>param_vectorizer__max_features</th>
      <th>params</th>
      <th>rank_test_score</th>
      <th>split0_test_score</th>
      <th>split0_train_score</th>
      <th>split1_test_score</th>
      <th>split1_train_score</th>
      <th>split2_test_score</th>
      <th>split2_train_score</th>
      <th>std_fit_time</th>
      <th>std_score_time</th>
      <th>std_test_score</th>
      <th>std_train_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.650011</td>
      <td>3.911422</td>
      <td>0.7290</td>
      <td>0.95490</td>
      <td>2</td>
      <td>300</td>
      <td>{'classifier__n_neighbors': 2, 'vectorizer__max_features': 300}</td>
      <td>1</td>
      <td>0.740552</td>
      <td>0.950945</td>
      <td>0.727273</td>
      <td>0.958452</td>
      <td>0.719172</td>
      <td>0.955302</td>
      <td>0.005341</td>
      <td>0.207406</td>
      <td>0.008814</td>
      <td>0.003078</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.663731</td>
      <td>3.014868</td>
      <td>0.7212</td>
      <td>0.96905</td>
      <td>2</td>
      <td>500</td>
      <td>{'classifier__n_neighbors': 2, 'vectorizer__max_features': 500}</td>
      <td>2</td>
      <td>0.724655</td>
      <td>0.967897</td>
      <td>0.726373</td>
      <td>0.971651</td>
      <td>0.712571</td>
      <td>0.967602</td>
      <td>0.024517</td>
      <td>0.748231</td>
      <td>0.006141</td>
      <td>0.001843</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.631577</td>
      <td>2.498195</td>
      <td>0.7166</td>
      <td>0.97260</td>
      <td>2</td>
      <td>700</td>
      <td>{'classifier__n_neighbors': 2, 'vectorizer__max_features': 700}</td>
      <td>3</td>
      <td>0.726155</td>
      <td>0.971497</td>
      <td>0.714671</td>
      <td>0.972551</td>
      <td>0.708971</td>
      <td>0.973751</td>
      <td>0.011422</td>
      <td>0.080301</td>
      <td>0.007147</td>
      <td>0.000921</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.685521</td>
      <td>2.450972</td>
      <td>0.7035</td>
      <td>0.80460</td>
      <td>3</td>
      <td>300</td>
      <td>{'classifier__n_neighbors': 3, 'vectorizer__max_features': 300}</td>
      <td>4</td>
      <td>0.711758</td>
      <td>0.804530</td>
      <td>0.705071</td>
      <td>0.808910</td>
      <td>0.693669</td>
      <td>0.800360</td>
      <td>0.045753</td>
      <td>0.052470</td>
      <td>0.007468</td>
      <td>0.003491</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0.684377</td>
      <td>2.984071</td>
      <td>0.6984</td>
      <td>0.75990</td>
      <td>5</td>
      <td>300</td>
      <td>{'classifier__n_neighbors': 5, 'vectorizer__max_features': 300}</td>
      <td>5</td>
      <td>0.707558</td>
      <td>0.761026</td>
      <td>0.697570</td>
      <td>0.764212</td>
      <td>0.690069</td>
      <td>0.754462</td>
      <td>0.029646</td>
      <td>0.070882</td>
      <td>0.007164</td>
      <td>0.004059</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.676721</td>
      <td>2.596920</td>
      <td>0.6824</td>
      <td>0.77825</td>
      <td>3</td>
      <td>500</td>
      <td>{'classifier__n_neighbors': 3, 'vectorizer__max_features': 500}</td>
      <td>6</td>
      <td>0.688062</td>
      <td>0.780978</td>
      <td>0.686169</td>
      <td>0.776361</td>
      <td>0.672967</td>
      <td>0.777411</td>
      <td>0.068777</td>
      <td>0.043217</td>
      <td>0.006714</td>
      <td>0.001976</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.697600</td>
      <td>2.933428</td>
      <td>0.6741</td>
      <td>0.72870</td>
      <td>5</td>
      <td>500</td>
      <td>{'classifier__n_neighbors': 5, 'vectorizer__max_features': 500}</td>
      <td>7</td>
      <td>0.679964</td>
      <td>0.724572</td>
      <td>0.678368</td>
      <td>0.732263</td>
      <td>0.663966</td>
      <td>0.729264</td>
      <td>0.013292</td>
      <td>0.168813</td>
      <td>0.007195</td>
      <td>0.003165</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.721103</td>
      <td>2.678074</td>
      <td>0.6727</td>
      <td>0.76480</td>
      <td>3</td>
      <td>700</td>
      <td>{'classifier__n_neighbors': 3, 'vectorizer__max_features': 700}</td>
      <td>8</td>
      <td>0.675165</td>
      <td>0.764326</td>
      <td>0.675968</td>
      <td>0.769612</td>
      <td>0.666967</td>
      <td>0.760462</td>
      <td>0.071410</td>
      <td>0.134303</td>
      <td>0.004067</td>
      <td>0.003750</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.589188</td>
      <td>2.520334</td>
      <td>0.6653</td>
      <td>0.71335</td>
      <td>5</td>
      <td>700</td>
      <td>{'classifier__n_neighbors': 5, 'vectorizer__max_features': 700}</td>
      <td>9</td>
      <td>0.666167</td>
      <td>0.711671</td>
      <td>0.672967</td>
      <td>0.716514</td>
      <td>0.656766</td>
      <td>0.711864</td>
      <td>0.104428</td>
      <td>0.161456</td>
      <td>0.006642</td>
      <td>0.002239</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
best_pipeline = grid.best_estimator_

```




{:.input_area}
```python
best_pipeline.get_params()
```





{:.output .output_data_text}
```
{'classifier': KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
            metric_params=None, n_jobs=1, n_neighbors=2, p=2,
            weights='uniform'),
 'classifier__algorithm': 'auto',
 'classifier__leaf_size': 30,
 'classifier__metric': 'minkowski',
 'classifier__metric_params': None,
 'classifier__n_jobs': 1,
 'classifier__n_neighbors': 2,
 'classifier__p': 2,
 'classifier__weights': 'uniform',
 'memory': None,
 'steps': [('vectorizer',
   CountVectorizer(analyzer='word', binary=False, decode_error='strict',
           dtype=<class 'numpy.int64'>, encoding='utf-8', input='content',
           lowercase=True, max_df=1.0, max_features=300, min_df=1,
           ngram_range=(1, 1), preprocessor=None, stop_words=None,
           strip_accents=None, token_pattern='(?u)\\b\\w\\w+\\b',
           tokenizer=None, vocabulary=None)),
  ('classifier',
   KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
              metric_params=None, n_jobs=1, n_neighbors=2, p=2,
              weights='uniform'))],
 'vectorizer': CountVectorizer(analyzer='word', binary=False, decode_error='strict',
         dtype=<class 'numpy.int64'>, encoding='utf-8', input='content',
         lowercase=True, max_df=1.0, max_features=300, min_df=1,
         ngram_range=(1, 1), preprocessor=None, stop_words=None,
         strip_accents=None, token_pattern='(?u)\\b\\w\\w+\\b',
         tokenizer=None, vocabulary=None),
 'vectorizer__analyzer': 'word',
 'vectorizer__binary': False,
 'vectorizer__decode_error': 'strict',
 'vectorizer__dtype': numpy.int64,
 'vectorizer__encoding': 'utf-8',
 'vectorizer__input': 'content',
 'vectorizer__lowercase': True,
 'vectorizer__max_df': 1.0,
 'vectorizer__max_features': 300,
 'vectorizer__min_df': 1,
 'vectorizer__ngram_range': (1, 1),
 'vectorizer__preprocessor': None,
 'vectorizer__stop_words': None,
 'vectorizer__strip_accents': None,
 'vectorizer__token_pattern': '(?u)\\b\\w\\w+\\b',
 'vectorizer__tokenizer': None,
 'vectorizer__vocabulary': None}
```





{:.input_area}
```python
train_prediction = best_pipeline.predict(wine_df['description'])

print(classification_report(train_prediction, wine_df['rating']))
```


{:.output .output_stream}
```
             precision    recall  f1-score   support

       High       1.00      0.90      0.95      4390
        Low       0.93      1.00      0.96      5610

avg / total       0.96      0.96      0.96     10000


```



{:.input_area}
```python
test_sample = wine_df_full.sample(n = 10000, replace=False)

test_prediction = best_pipeline.predict(test_sample['description'])

print(classification_report(test_prediction, test_sample['rating']))
```


{:.output .output_stream}
```
             precision    recall  f1-score   support

       High       0.60      0.77      0.68      3092
        Low       0.88      0.77      0.82      6908

avg / total       0.80      0.77      0.78     10000


```



{:.input_area}
```python
from sklearn.feature_extraction.text import TfidfTransformer
```




{:.input_area}
```python
pipeline = Pipeline([
                     ('vectorizer' , CountVectorizer()),
                     ('tfidf'      , TfidfTransformer()),
                     ('classifier' , KNeighborsClassifier())
                    ])

parameters = {'vectorizer__max_features' : [250, 300, 350],
              'vectorizer__stop_words'   : ['english', None],
              'tfidf__use_idf'           : [True, False],
              'classifier__n_neighbors'  : [2, 3] }



```




{:.input_area}
```python
grid        = GridSearchCV(pipeline,
                           parameters,
                           return_train_score = True,
                           cv = 3,
                           n_jobs = -1, 
                           verbose = 2)
```




{:.input_area}
```python
grid.fit(wine_df['description'], wine_df['rating'])
```


{:.output .output_stream}
```
Fitting 3 folds for each of 24 candidates, totalling 72 fits
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english, total=   2.3s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english, total=   2.3s
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english, total=   2.3s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None, total=   2.8s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english, total=   2.6s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english, total=   2.3s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None, total=   3.2s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None, total=   3.1s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english, total=   2.4s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.7s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english, total=   2.4s
[CV] classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english, total=   2.4s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.1s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english, total=   2.4s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english, total=   2.4s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english, total=   2.5s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None, total=   2.8s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None, total=   2.7s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None, total=   2.8s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english, total=   2.5s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english, total=   2.5s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english, total=   2.5s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.3s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.7s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.2s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.0s
[CV] classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.1s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None, total=   4.1s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.9s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=2, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None, total=   4.1s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english, total=   3.8s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english, total=   3.5s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=english, total=   3.3s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None, total=   4.5s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None, total=   4.6s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english, total=   4.7s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=250, vectorizer__stop_words=None, total=   6.5s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english, total=   3.6s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=english, total=   3.6s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None, total=   5.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None, total=   5.4s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english, total=   4.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=300, vectorizer__stop_words=None, total=   5.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english, total=   4.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=english, total=   4.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None, total=   5.2s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None, total=   5.2s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=True, vectorizer__max_features=350, vectorizer__stop_words=None, total=   5.6s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english, total=   4.6s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english, total=   3.8s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=english, total=   3.8s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None, total=   4.4s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None, total=   4.1s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=250, vectorizer__stop_words=None, total=   4.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english, total=   4.3s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english, total=   3.9s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=english, total=   4.1s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.6s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.5s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.2s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=300, vectorizer__stop_words=None, total=   3.9s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.0s
[CV] classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None 
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=english, total=   3.0s
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.5s
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.5s
[CV]  classifier__n_neighbors=3, tfidf__use_idf=False, vectorizer__max_features=350, vectorizer__stop_words=None, total=   3.7s

```




{:.output .output_data_text}
```
GridSearchCV(cv=3, error_score='raise',
       estimator=Pipeline(memory=None,
     steps=[('vectorizer', CountVectorizer(analyzer='word', binary=False, decode_error='strict',
        dtype=<class 'numpy.int64'>, encoding='utf-8', input='content',
        lowercase=True, max_df=1.0, max_features=None, min_df=1,
        ngram_range=(1, 1), preprocessor=None, stop_words=None,
       ...owski',
           metric_params=None, n_jobs=1, n_neighbors=5, p=2,
           weights='uniform'))]),
       fit_params=None, iid=True, n_jobs=-1,
       param_grid={'vectorizer__max_features': [250, 300, 350], 'vectorizer__stop_words': ['english', None], 'tfidf__use_idf': [True, False], 'classifier__n_neighbors': [2, 3]},
       pre_dispatch='2*n_jobs', refit=True, return_train_score=True,
       scoring=None, verbose=2)
```





{:.input_area}
```python
pd.DataFrame(grid.cv_results_).sort_values(by = 'rank_test_score')

```





<div markdown="0" class="output output_html">
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mean_fit_time</th>
      <th>mean_score_time</th>
      <th>mean_test_score</th>
      <th>mean_train_score</th>
      <th>param_classifier__n_neighbors</th>
      <th>param_tfidf__use_idf</th>
      <th>param_vectorizer__max_features</th>
      <th>param_vectorizer__stop_words</th>
      <th>params</th>
      <th>rank_test_score</th>
      <th>split0_test_score</th>
      <th>split0_train_score</th>
      <th>split1_test_score</th>
      <th>split1_train_score</th>
      <th>split2_test_score</th>
      <th>split2_train_score</th>
      <th>std_fit_time</th>
      <th>std_score_time</th>
      <th>std_test_score</th>
      <th>std_train_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17</th>
      <td>1.170683</td>
      <td>4.161584</td>
      <td>0.7867</td>
      <td>0.89755</td>
      <td>3</td>
      <td>True</td>
      <td>350</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': True, 'vectorizer__max_features': 350, 'vectorizer__stop_words': None}</td>
      <td>1</td>
      <td>0.788542</td>
      <td>0.899190</td>
      <td>0.787579</td>
      <td>0.895155</td>
      <td>0.783978</td>
      <td>0.898305</td>
      <td>0.100817</td>
      <td>0.107977</td>
      <td>0.001964</td>
      <td>0.001732</td>
    </tr>
    <tr>
      <th>15</th>
      <td>0.965019</td>
      <td>4.175408</td>
      <td>0.7812</td>
      <td>0.89220</td>
      <td>3</td>
      <td>True</td>
      <td>300</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': True, 'vectorizer__max_features': 300, 'vectorizer__stop_words': None}</td>
      <td>2</td>
      <td>0.781944</td>
      <td>0.887339</td>
      <td>0.783378</td>
      <td>0.892305</td>
      <td>0.778278</td>
      <td>0.896955</td>
      <td>0.119842</td>
      <td>0.236494</td>
      <td>0.002148</td>
      <td>0.003927</td>
    </tr>
    <tr>
      <th>16</th>
      <td>0.928742</td>
      <td>3.072961</td>
      <td>0.7773</td>
      <td>0.88515</td>
      <td>3</td>
      <td>True</td>
      <td>350</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': True, 'vectorizer__max_features': 350, 'vectorizer__stop_words': 'e...</td>
      <td>3</td>
      <td>0.766647</td>
      <td>0.890489</td>
      <td>0.782778</td>
      <td>0.883156</td>
      <td>0.782478</td>
      <td>0.881806</td>
      <td>0.073366</td>
      <td>0.057893</td>
      <td>0.007535</td>
      <td>0.003815</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0.944672</td>
      <td>3.026088</td>
      <td>0.7748</td>
      <td>0.88720</td>
      <td>3</td>
      <td>True</td>
      <td>300</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': True, 'vectorizer__max_features': 300, 'vectorizer__stop_words': 'e...</td>
      <td>4</td>
      <td>0.763047</td>
      <td>0.892439</td>
      <td>0.777678</td>
      <td>0.885256</td>
      <td>0.783678</td>
      <td>0.883906</td>
      <td>0.016476</td>
      <td>0.507439</td>
      <td>0.008665</td>
      <td>0.003745</td>
    </tr>
    <tr>
      <th>12</th>
      <td>0.990014</td>
      <td>2.508165</td>
      <td>0.7735</td>
      <td>0.88150</td>
      <td>3</td>
      <td>True</td>
      <td>250</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': True, 'vectorizer__max_features': 250, 'vectorizer__stop_words': 'e...</td>
      <td>5</td>
      <td>0.762747</td>
      <td>0.883438</td>
      <td>0.775578</td>
      <td>0.879556</td>
      <td>0.782178</td>
      <td>0.881506</td>
      <td>0.109654</td>
      <td>0.137139</td>
      <td>0.008068</td>
      <td>0.001585</td>
    </tr>
    <tr>
      <th>13</th>
      <td>0.830728</td>
      <td>4.362559</td>
      <td>0.7721</td>
      <td>0.88935</td>
      <td>3</td>
      <td>True</td>
      <td>250</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': True, 'vectorizer__max_features': 250, 'vectorizer__stop_words': None}</td>
      <td>6</td>
      <td>0.775345</td>
      <td>0.888539</td>
      <td>0.764776</td>
      <td>0.887356</td>
      <td>0.776178</td>
      <td>0.892155</td>
      <td>0.091845</td>
      <td>0.802333</td>
      <td>0.005189</td>
      <td>0.002042</td>
    </tr>
    <tr>
      <th>22</th>
      <td>0.727000</td>
      <td>2.340584</td>
      <td>0.7619</td>
      <td>0.87875</td>
      <td>3</td>
      <td>False</td>
      <td>350</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': False, 'vectorizer__max_features': 350, 'vectorizer__stop_words': '...</td>
      <td>7</td>
      <td>0.754649</td>
      <td>0.883738</td>
      <td>0.762076</td>
      <td>0.878656</td>
      <td>0.768977</td>
      <td>0.873856</td>
      <td>0.043127</td>
      <td>0.072686</td>
      <td>0.005851</td>
      <td>0.004035</td>
    </tr>
    <tr>
      <th>20</th>
      <td>0.843213</td>
      <td>3.285683</td>
      <td>0.7576</td>
      <td>0.87725</td>
      <td>3</td>
      <td>False</td>
      <td>300</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': False, 'vectorizer__max_features': 300, 'vectorizer__stop_words': '...</td>
      <td>8</td>
      <td>0.745651</td>
      <td>0.882238</td>
      <td>0.755476</td>
      <td>0.877756</td>
      <td>0.771677</td>
      <td>0.871756</td>
      <td>0.089618</td>
      <td>0.071229</td>
      <td>0.010731</td>
      <td>0.004294</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.578924</td>
      <td>2.237310</td>
      <td>0.7527</td>
      <td>0.90920</td>
      <td>2</td>
      <td>True</td>
      <td>350</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': True, 'vectorizer__max_features': 350, 'vectorizer__stop_words': 'e...</td>
      <td>9</td>
      <td>0.749550</td>
      <td>0.911491</td>
      <td>0.762376</td>
      <td>0.909255</td>
      <td>0.746175</td>
      <td>0.906855</td>
      <td>0.018350</td>
      <td>0.628051</td>
      <td>0.006979</td>
      <td>0.001893</td>
    </tr>
    <tr>
      <th>18</th>
      <td>0.987835</td>
      <td>3.083754</td>
      <td>0.7498</td>
      <td>0.87475</td>
      <td>3</td>
      <td>False</td>
      <td>250</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': False, 'vectorizer__max_features': 250, 'vectorizer__stop_words': '...</td>
      <td>10</td>
      <td>0.750150</td>
      <td>0.879238</td>
      <td>0.743174</td>
      <td>0.871156</td>
      <td>0.756076</td>
      <td>0.873856</td>
      <td>0.182437</td>
      <td>0.243110</td>
      <td>0.005272</td>
      <td>0.003359</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.580391</td>
      <td>1.821766</td>
      <td>0.7479</td>
      <td>0.90760</td>
      <td>2</td>
      <td>True</td>
      <td>300</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': True, 'vectorizer__max_features': 300, 'vectorizer__stop_words': 'e...</td>
      <td>11</td>
      <td>0.734553</td>
      <td>0.908491</td>
      <td>0.756376</td>
      <td>0.908355</td>
      <td>0.752775</td>
      <td>0.905955</td>
      <td>0.021492</td>
      <td>0.085388</td>
      <td>0.009553</td>
      <td>0.001165</td>
    </tr>
    <tr>
      <th>10</th>
      <td>0.797745</td>
      <td>2.314199</td>
      <td>0.7430</td>
      <td>0.91235</td>
      <td>2</td>
      <td>False</td>
      <td>350</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': False, 'vectorizer__max_features': 350, 'vectorizer__stop_words': '...</td>
      <td>12</td>
      <td>0.745951</td>
      <td>0.916442</td>
      <td>0.739574</td>
      <td>0.915404</td>
      <td>0.743474</td>
      <td>0.905205</td>
      <td>0.078513</td>
      <td>0.027637</td>
      <td>0.002625</td>
      <td>0.005070</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.582446</td>
      <td>1.912485</td>
      <td>0.7391</td>
      <td>0.90970</td>
      <td>2</td>
      <td>False</td>
      <td>300</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': False, 'vectorizer__max_features': 300, 'vectorizer__stop_words': '...</td>
      <td>13</td>
      <td>0.737552</td>
      <td>0.909691</td>
      <td>0.738674</td>
      <td>0.914954</td>
      <td>0.741074</td>
      <td>0.904455</td>
      <td>0.025495</td>
      <td>0.020054</td>
      <td>0.001469</td>
      <td>0.004286</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0.540779</td>
      <td>1.746032</td>
      <td>0.7366</td>
      <td>0.90365</td>
      <td>2</td>
      <td>True</td>
      <td>250</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': True, 'vectorizer__max_features': 250, 'vectorizer__stop_words': 'e...</td>
      <td>14</td>
      <td>0.729454</td>
      <td>0.906691</td>
      <td>0.738374</td>
      <td>0.900405</td>
      <td>0.741974</td>
      <td>0.903855</td>
      <td>0.001660</td>
      <td>0.020781</td>
      <td>0.005263</td>
      <td>0.002570</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.599577</td>
      <td>2.415360</td>
      <td>0.7362</td>
      <td>0.88985</td>
      <td>2</td>
      <td>True</td>
      <td>350</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': True, 'vectorizer__max_features': 350, 'vectorizer__stop_words': None}</td>
      <td>15</td>
      <td>0.739352</td>
      <td>0.888239</td>
      <td>0.734473</td>
      <td>0.889756</td>
      <td>0.734773</td>
      <td>0.891555</td>
      <td>0.015238</td>
      <td>0.050224</td>
      <td>0.002233</td>
      <td>0.001356</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0.547162</td>
      <td>1.846357</td>
      <td>0.7324</td>
      <td>0.90300</td>
      <td>2</td>
      <td>False</td>
      <td>250</td>
      <td>english</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': False, 'vectorizer__max_features': 250, 'vectorizer__stop_words': '...</td>
      <td>16</td>
      <td>0.732454</td>
      <td>0.902490</td>
      <td>0.731773</td>
      <td>0.904455</td>
      <td>0.732973</td>
      <td>0.902055</td>
      <td>0.006475</td>
      <td>0.046370</td>
      <td>0.000491</td>
      <td>0.001044</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.613891</td>
      <td>2.380259</td>
      <td>0.7270</td>
      <td>0.88310</td>
      <td>2</td>
      <td>True</td>
      <td>300</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': True, 'vectorizer__max_features': 300, 'vectorizer__stop_words': None}</td>
      <td>17</td>
      <td>0.732454</td>
      <td>0.880288</td>
      <td>0.725773</td>
      <td>0.880456</td>
      <td>0.722772</td>
      <td>0.888556</td>
      <td>0.026856</td>
      <td>0.002337</td>
      <td>0.004047</td>
      <td>0.003858</td>
    </tr>
    <tr>
      <th>23</th>
      <td>0.752127</td>
      <td>2.854057</td>
      <td>0.7186</td>
      <td>0.86370</td>
      <td>3</td>
      <td>False</td>
      <td>350</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': False, 'vectorizer__max_features': 350, 'vectorizer__stop_words': N...</td>
      <td>18</td>
      <td>0.723155</td>
      <td>0.860186</td>
      <td>0.714071</td>
      <td>0.863507</td>
      <td>0.718572</td>
      <td>0.867407</td>
      <td>0.028106</td>
      <td>0.121911</td>
      <td>0.003709</td>
      <td>0.002951</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.617535</td>
      <td>2.401295</td>
      <td>0.7165</td>
      <td>0.87620</td>
      <td>2</td>
      <td>True</td>
      <td>250</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': True, 'vectorizer__max_features': 250, 'vectorizer__stop_words': None}</td>
      <td>19</td>
      <td>0.721356</td>
      <td>0.879088</td>
      <td>0.710771</td>
      <td>0.875056</td>
      <td>0.717372</td>
      <td>0.874456</td>
      <td>0.045237</td>
      <td>0.139353</td>
      <td>0.004365</td>
      <td>0.002057</td>
    </tr>
    <tr>
      <th>21</th>
      <td>0.813277</td>
      <td>2.858795</td>
      <td>0.7136</td>
      <td>0.86235</td>
      <td>3</td>
      <td>False</td>
      <td>300</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': False, 'vectorizer__max_features': 300, 'vectorizer__stop_words': N...</td>
      <td>20</td>
      <td>0.717157</td>
      <td>0.862886</td>
      <td>0.711671</td>
      <td>0.862157</td>
      <td>0.711971</td>
      <td>0.862007</td>
      <td>0.093577</td>
      <td>0.103089</td>
      <td>0.002518</td>
      <td>0.000384</td>
    </tr>
    <tr>
      <th>19</th>
      <td>0.916645</td>
      <td>3.257190</td>
      <td>0.7118</td>
      <td>0.85780</td>
      <td>3</td>
      <td>False</td>
      <td>250</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 3, 'tfidf__use_idf': False, 'vectorizer__max_features': 250, 'vectorizer__stop_words': N...</td>
      <td>21</td>
      <td>0.712058</td>
      <td>0.860036</td>
      <td>0.712271</td>
      <td>0.857207</td>
      <td>0.711071</td>
      <td>0.856157</td>
      <td>0.111296</td>
      <td>0.131830</td>
      <td>0.000523</td>
      <td>0.001638</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0.863508</td>
      <td>3.169554</td>
      <td>0.6517</td>
      <td>0.82095</td>
      <td>2</td>
      <td>False</td>
      <td>350</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': False, 'vectorizer__max_features': 350, 'vectorizer__stop_words': N...</td>
      <td>22</td>
      <td>0.651170</td>
      <td>0.817582</td>
      <td>0.650165</td>
      <td>0.821209</td>
      <td>0.653765</td>
      <td>0.824059</td>
      <td>0.063974</td>
      <td>0.086127</td>
      <td>0.001517</td>
      <td>0.002651</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0.706671</td>
      <td>2.628934</td>
      <td>0.6447</td>
      <td>0.81660</td>
      <td>2</td>
      <td>False</td>
      <td>300</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': False, 'vectorizer__max_features': 300, 'vectorizer__stop_words': N...</td>
      <td>23</td>
      <td>0.639772</td>
      <td>0.812031</td>
      <td>0.641764</td>
      <td>0.817609</td>
      <td>0.652565</td>
      <td>0.820159</td>
      <td>0.100523</td>
      <td>0.220924</td>
      <td>0.005620</td>
      <td>0.003394</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.590949</td>
      <td>2.152607</td>
      <td>0.6382</td>
      <td>0.80815</td>
      <td>2</td>
      <td>False</td>
      <td>250</td>
      <td>None</td>
      <td>{'classifier__n_neighbors': 2, 'tfidf__use_idf': False, 'vectorizer__max_features': 250, 'vectorizer__stop_words': N...</td>
      <td>24</td>
      <td>0.633473</td>
      <td>0.807831</td>
      <td>0.632763</td>
      <td>0.807410</td>
      <td>0.648365</td>
      <td>0.809210</td>
      <td>0.007630</td>
      <td>0.036215</td>
      <td>0.007193</td>
      <td>0.000769</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
best_pipeline = grid.best_estimator_

print('Train fit')
train_prediction = best_pipeline.predict(wine_df['description'])

print(classification_report(train_prediction, wine_df['rating']))

print('Test fit')
test_sample = wine_df_full.sample(n = 10000, replace=False)

test_prediction = best_pipeline.predict(test_sample['description'])

print(classification_report(test_prediction, test_sample['rating']))
```


{:.output .output_stream}
```
Train fit
             precision    recall  f1-score   support

       High       0.84      0.90      0.87      3716
        Low       0.94      0.90      0.92      6284

avg / total       0.90      0.90      0.90     10000

Test fit
             precision    recall  f1-score   support

       High       0.70      0.77      0.74      3602
        Low       0.86      0.82      0.84      6398

avg / total       0.81      0.80      0.80     10000


```



{:.input_area}
```python
print(accuracy_score(test_prediction, test_sample['rating']))
```


{:.output .output_stream}
```
0.8014

```

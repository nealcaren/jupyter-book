---
interact_link: content/analysis/classify.ipynb
title: 'Classification'
prev_page:
  url: /placeholder
  title: 'Analysis'
next_page:
  url: /analysis/topicmodels
  title: 'Topic Models'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---

<div class="alert alert-info">
<h3> Groups All Day</h3>
<p> Group numbers are in <code>data/groups.json</code>. Find your group. Move tables and chairs so that folks are not in row and no one has to turn around to see the board.

Start a new notebook where you will do your work for today. Make the first cell a markdown cell and put a title or notes in there. Second cell can include your <code>import</code> statements.
</div>


### Text Classification



{:.input_area}
```python
%matplotlib inline

import pandas as pd
```




{:.input_area}
```python
#https://www.kaggle.com/zynicide/wine-reviews/data

wine_df = pd.read_csv('data/wine_reviews.csv')

```


<div class="alert alert-info">
<h3> Your turn</h3>
<p> What's in the dataset?
</div>




{:.input_area}
```python
wine_df['points'].value_counts()
```





{:.output .output_data_text}
```
87     16933
86     12600
91     11359
92      9613
85      9530
93      6489
84      6480
94      3758
83      3025
82      1836
95      1535
81       692
96       523
80       397
97       229
98        77
99        33
100       19
Name: points, dtype: int64
```





{:.input_area}
```python
wine_df['description'][:5]
```





{:.output .output_data_text}
```
0    Aromas include tropical fruit, broom, brimston...
1    This is ripe and fruity, a wine that is smooth...
2    Tart and snappy, the flavors of lime flesh and...
3    Pineapple rind, lemon pith and orange blossom ...
4    Much like the regular bottling from 2012, this...
Name: description, dtype: object
```



![google_search.png](images/google_search.png)



{:.input_area}
```python
pd.set_option('display.max_colwidth', 120)


```




{:.input_area}
```python
wine_df['description'][:5]
```





{:.output .output_data_text}
```
0    Aromas include tropical fruit, broom, brimstone and dried herb. The palate isn't overly expressive, offering unripen...
1    This is ripe and fruity, a wine that is smooth while still structured. Firm tannins are filled out with juicy red be...
2    Tart and snappy, the flavors of lime flesh and rind dominate. Some green pineapple pokes through, with crisp acidity...
3    Pineapple rind, lemon pith and orange blossom start off the aromas. The palate is a bit more opulent, with notes of ...
4    Much like the regular bottling from 2012, this comes across as rather rough and tannic, with rustic, earthy, herbal ...
Name: description, dtype: object
```





{:.input_area}
```python
wine_df.head()
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
      <th>country</th>
      <th>description</th>
      <th>designation</th>
      <th>points</th>
      <th>price</th>
      <th>province</th>
      <th>region_1</th>
      <th>region_2</th>
      <th>taster_name</th>
      <th>taster_twitter_handle</th>
      <th>title</th>
      <th>variety</th>
      <th>winery</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Italy</td>
      <td>Aromas include tropical fruit, broom, brimstone and dried herb. The palate isn't overly expressive, offering unripen...</td>
      <td>Vulkà Bianco</td>
      <td>87</td>
      <td>NaN</td>
      <td>Sicily &amp; Sardinia</td>
      <td>Etna</td>
      <td>NaN</td>
      <td>Kerin O’Keefe</td>
      <td>@kerinokeefe</td>
      <td>Nicosia 2013 Vulkà Bianco  (Etna)</td>
      <td>White Blend</td>
      <td>Nicosia</td>
      <td>Low</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Portugal</td>
      <td>This is ripe and fruity, a wine that is smooth while still structured. Firm tannins are filled out with juicy red be...</td>
      <td>Avidagos</td>
      <td>87</td>
      <td>15.0</td>
      <td>Douro</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Roger Voss</td>
      <td>@vossroger</td>
      <td>Quinta dos Avidagos 2011 Avidagos Red (Douro)</td>
      <td>Portuguese Red</td>
      <td>Quinta dos Avidagos</td>
      <td>Low</td>
    </tr>
    <tr>
      <th>2</th>
      <td>US</td>
      <td>Tart and snappy, the flavors of lime flesh and rind dominate. Some green pineapple pokes through, with crisp acidity...</td>
      <td>NaN</td>
      <td>87</td>
      <td>14.0</td>
      <td>Oregon</td>
      <td>Willamette Valley</td>
      <td>Willamette Valley</td>
      <td>Paul Gregutt</td>
      <td>@paulgwine</td>
      <td>Rainstorm 2013 Pinot Gris (Willamette Valley)</td>
      <td>Pinot Gris</td>
      <td>Rainstorm</td>
      <td>Low</td>
    </tr>
    <tr>
      <th>3</th>
      <td>US</td>
      <td>Pineapple rind, lemon pith and orange blossom start off the aromas. The palate is a bit more opulent, with notes of ...</td>
      <td>Reserve Late Harvest</td>
      <td>87</td>
      <td>13.0</td>
      <td>Michigan</td>
      <td>Lake Michigan Shore</td>
      <td>NaN</td>
      <td>Alexander Peartree</td>
      <td>NaN</td>
      <td>St. Julian 2013 Reserve Late Harvest Riesling (Lake Michigan Shore)</td>
      <td>Riesling</td>
      <td>St. Julian</td>
      <td>Low</td>
    </tr>
    <tr>
      <th>4</th>
      <td>US</td>
      <td>Much like the regular bottling from 2012, this comes across as rather rough and tannic, with rustic, earthy, herbal ...</td>
      <td>Vintner's Reserve Wild Child Block</td>
      <td>87</td>
      <td>65.0</td>
      <td>Oregon</td>
      <td>Willamette Valley</td>
      <td>Willamette Valley</td>
      <td>Paul Gregutt</td>
      <td>@paulgwine</td>
      <td>Sweet Cheeks 2012 Vintner's Reserve Wild Child Block Pinot Noir (Willamette Valley)</td>
      <td>Pinot Noir</td>
      <td>Sweet Cheeks</td>
      <td>Low</td>
    </tr>
  </tbody>
</table>
</div>
</div>



### Turning words in to features



{:.input_area}
```python
from sklearn.feature_extraction.text import CountVectorizer

```




{:.input_area}
```python
vectorizer = CountVectorizer(lowercase   = True,
                             ngram_range = (1,1),
                             stop_words  = 'english',
                             max_df      = .60,
                             min_df      = .01,
                             max_features = None)
```




{:.input_area}
```python
vectorizer.fit(wine_df['description'])
```





{:.output .output_data_text}
```
CountVectorizer(analyzer='word', binary=False, decode_error='strict',
        dtype=<class 'numpy.int64'>, encoding='utf-8', input='content',
        lowercase=True, max_df=0.6, max_features=None, min_df=0.01,
        ngram_range=(1, 1), preprocessor=None, stop_words='english',
        strip_accents=None, token_pattern='(?u)\\b\\w\\w+\\b',
        tokenizer=None, vocabulary=None)
```





{:.input_area}
```python
len(vectorizer.get_feature_names())
```





{:.output .output_data_text}
```
400
```





{:.input_area}
```python
review_word_counts = vectorizer.transform(wine_df['description'])
```




{:.input_area}
```python
from sklearn.naive_bayes import MultinomialNB
```




{:.input_area}
```python
nb_classifier = MultinomialNB()

```




{:.input_area}
```python
nb_classifier.fit(review_word_counts, wine_df['rating'])
```





{:.output .output_data_text}
```
MultinomialNB(alpha=1.0, class_prior=None, fit_prior=True)
```





{:.input_area}
```python
nb_classifier.coef_[0]
```





{:.output .output_data_text}
```
array([ -7.33467081,  -7.24590289,  -7.19142933,  -7.20616256,
        -7.38885524,  -8.18761044,  -8.89070795, -11.09798286,
        -7.20061213,  -7.24397425,  -6.69237287,  -4.00593826,
        -6.70465667,  -7.24397425,  -7.25950854,  -6.263145  ,
        -6.67477066,  -6.92639671,  -6.56148092,  -6.14471991,
        -6.39337878,  -6.09342661,  -7.16437628,  -4.84345499,
        -6.96645929,  -6.27185332,  -7.04067957,  -3.78998822,
        -6.93484747,  -6.76128647,  -5.86105745,  -7.28728811,
        -6.39585097,  -7.30147274,  -6.26459113,  -5.88949074,
        -6.73083916,  -7.12085331,  -8.99606846,  -9.17839002,
        -6.77928336,  -4.62605818,  -6.80255446,  -7.28127003,
        -6.45534174,  -5.38365017,  -5.85864374,  -4.66095875,
        -6.82131674,  -5.29641393,  -6.28284647,  -5.05147071,
        -7.33256333,  -7.57162234,  -6.77807344,  -5.25233262,
        -6.4614902 ,  -7.22300113,  -7.28127003,  -6.99910314,
        -6.93202261,  -6.6704181 ,  -5.11679824,  -6.92639671,
        -6.60120809,  -7.02817941,  -6.90970648,  -5.45898136,
        -6.25953879,  -6.94337025,  -6.60019543,  -6.97231583,
        -6.67368074,  -6.92639671,  -8.24885406,  -5.35585418,
        -5.83765061,  -6.17138815,  -4.27578547,  -7.14499242,
        -5.76966548,  -6.77084449,  -6.78779405,  -4.92438617,
        -6.89600763,  -7.57429971,  -5.50665672,  -6.73894574,
        -6.66392457,  -7.36248009,  -6.42855828,  -6.28505968,
        -6.03022282,  -6.31276643,  -6.62064605,  -7.24783526,
        -6.96063686,  -6.76605406,  -7.01583357,  -7.06133161,
        -6.71029049,  -6.71937114,  -5.85479388,  -4.80154344,
        -7.04698874,  -5.67423501,  -6.93909978,  -6.87981295,
        -5.63530735,  -7.44143879,  -6.60934649,  -6.80007922,
        -6.5595354 ,  -6.69793772,  -7.28728811,  -7.23057692,
        -7.82255487,  -6.81376954,  -6.74594694,  -5.74741811,
        -4.30921589,  -6.14087621,  -7.07425586,  -4.5923347 ,
        -6.65319485,  -6.59012486,  -5.8944758 ,  -5.40823784,
        -6.27696842,  -7.73382675,  -6.93061316,  -6.48106378,
        -7.04698874,  -6.99158996,  -7.37558071,  -7.10061653,
        -7.26343012,  -5.86882072,  -5.67865535,  -6.2459526 ,
        -3.93059431,  -6.04579856,  -7.30761401,  -6.00367509,
        -5.63878427,  -6.97820686,  -3.2928417 ,  -6.26459113,
        -6.47747633,  -6.96938327,  -8.22287857,  -6.49372218,
        -6.8520884 ,  -7.59598078,  -6.53363467,  -7.02507863,
        -6.81251719,  -7.17510984,  -4.62241008,  -6.51023631,
        -3.71517988,  -6.61755166,  -5.08005344,  -4.95653214,
        -7.05015831,  -6.33580892,  -7.05015831,  -7.01583357,
        -5.10606387,  -6.59514747,  -6.14664731,  -6.52327188,
        -6.65747293,  -4.95888231,  -7.0066732 ,  -6.93626289,
        -7.71513462,  -6.31885937,  -6.23114795,  -5.97077739,
        -5.341953  ,  -6.24808555,  -6.13069801,  -5.71641117,
        -6.0574946 ,  -6.2283527 ,  -6.82384519,  -8.7337042 ,
        -7.71205295,  -6.85469596,  -7.23821054,  -6.52514805,
        -6.20695044,  -6.99910314,  -5.54467098,  -5.76131929,
        -7.86825702,  -7.3516925 ,  -5.98599507,  -7.40910341,
        -6.09770404,  -6.30144128,  -5.36670314,  -7.73382675,
        -6.37867263,  -4.70226606,  -6.0983166 ,  -5.25602072,
        -5.78612127,  -7.55834193,  -5.70146693,  -6.07052025,
        -6.74010921,  -7.00212429,  -8.26476952,  -6.94765903,
        -7.48706495,  -6.41165171,  -6.702412  ,  -6.84948762,
        -6.85339133,  -6.96792021,  -5.44485592,  -5.80559211,
        -5.85095879,  -7.36465164,  -6.61344066,  -6.17535117,
        -7.07425586,  -6.85339133,  -6.92639671,  -6.90695165,
        -7.09396094,  -5.56891459,  -5.8830471 ,  -6.99458846,
        -7.02817941,  -6.61652233,  -6.28653788,  -7.16082388,
        -6.39998491,  -4.75027528,  -5.67183212,  -4.66518893,
        -4.6986319 ,  -6.2459526 ,  -6.85469596,  -5.05297755,
        -7.28327204,  -7.25365487,  -7.0036383 ,  -5.93631864,
        -8.6675644 ,  -5.76526407,  -6.78657379,  -7.55570688,
        -6.25953879,  -3.97101903,  -5.3208936 ,  -5.51825303,
        -7.03597368,  -5.50665672,  -6.63626323,  -6.70690639,
        -7.36465164,  -7.05015831,  -6.01432243,  -6.79884388,
        -5.62380415,  -6.54794106,  -6.37059504,  -4.88046989,
        -7.05972776,  -7.64080621,  -7.79878465,  -8.18761044,
        -7.66987803,  -7.75287494,  -6.19203491,  -6.60019543,
        -7.53229879,  -7.87547727,  -7.83988632,  -6.87847515,
        -7.48706495,  -7.86825702,  -6.84689358,  -6.3196236 ,
        -6.97673084,  -7.26934146,  -5.31303942,  -6.78779405,
        -5.67543862,  -4.40533532,  -6.20695044,  -5.30082044,
        -6.4676767 ,  -6.84689358,  -6.93768033,  -4.53233384,
        -7.40005357,  -6.50194516,  -7.27927203,  -6.48827752,
        -6.19608077,  -6.6682489 ,  -6.61755166,  -7.35815108,
        -6.83146911,  -5.62724124,  -6.37220535,  -6.47301005,
        -7.69375976,  -7.36900895,  -6.39255607,  -6.55468808,
        -6.56538337,  -5.28439522,  -6.81502345,  -5.348011  ,
        -7.64367565,  -6.95629218,  -5.53940133,  -6.67586177,
        -6.82511181,  -6.22974935,  -5.86009127,  -4.62115037,
        -6.34989366,  -6.71595622,  -6.83530298,  -6.27185332,
        -4.84170949,  -6.91246891,  -5.67183212,  -6.32115382,
        -6.53933269,  -6.05573145,  -6.54794106,  -5.97239682,
        -6.8430151 ,  -5.68755508,  -6.96354384,  -6.63941615,
        -7.56098394,  -4.6830347 ,  -6.39915675,  -6.01883966,
        -6.88249391,  -6.31428619,  -5.67183212,  -7.0051546 ,
        -4.37644764,  -5.58382124,  -6.64046933,  -5.76000781,
        -7.25755351,  -5.23641994,  -6.92080229,  -6.11437624,
        -8.05779882,  -6.68243353,  -5.98818805,  -6.60628682,
        -6.76844641,  -6.37301147,  -6.80255446,  -6.51023631,
        -5.30496925,  -7.25171123,  -6.0307954 ,  -7.30556273,
        -5.15566081,  -7.01583357,  -7.00819411,  -8.2973811 ,
        -7.43209292,  -7.15024177,  -7.79209566,  -6.78292193,
        -7.62376034,  -6.55759365,  -7.55570688,  -6.50194516,
        -6.60934649,  -6.37705188,  -7.27727801,  -6.94765903,
        -5.01659451,  -6.52420953,  -3.2604285 ,  -7.35384073,
        -6.92499517,  -5.85383373,  -7.07263117,  -6.63836407,
        -6.35147095,  -6.36098728,  -6.58214078,  -6.49645566])
```





{:.input_area}
```python
coeficients = pd.Series(nb_classifier.coef_[0], 
                        index = vectorizer.get_feature_names())
```





{:.output .output_data_text}
```
array(['High', 'Low'], dtype='<U4')
```





{:.input_area}
```python
coeficients.sort_values()[:20]
```





{:.output .output_data_text}
```
2022          -11.097983
beautifully    -9.178390
beautiful      -8.996068
2020           -8.890708
impressive     -8.733704
opulent        -8.667564
velvety        -8.297381
lovely         -8.264770
cellar         -8.248854
focused        -8.222879
potential      -8.187610
2019           -8.187610
tightly        -8.057799
producer       -7.875477
layered        -7.868257
purple         -7.868257
provide        -7.839886
develop        -7.822555
polished       -7.798785
vines          -7.792096
dtype: float64
```





{:.input_area}
```python
coeficients.sort_values(ascending=False)[:20]
```





{:.output .output_data_text}
```
wine      -3.260428
flavors   -3.292842
fruit     -3.715180
aromas    -3.789988
finish    -3.930594
palate    -3.971019
acidity   -4.005938
cherry    -4.275785
drink     -4.309216
tannins   -4.376448
red       -4.405335
ripe      -4.532334
dry       -4.592335
soft      -4.621150
fresh     -4.622410
berry     -4.626058
black     -4.660959
notes     -4.665189
sweet     -4.683035
oak       -4.698632
dtype: float64
```



<div class="alert alert-info">
<h3> Your turn</h3>
<p> New groups! Load up the group spreadsheet and find your group. Working in your group, use the "ge_speeches.json" file to determine what are the most distinguishing words used by Hillary Clinton and Donald Trump during the 2016 election. Do this in a new notebook!

</div>




{:.input_area}
```python
nb_classifier.predict(review_word_counts)
```





{:.output .output_data_text}
```
array(['Low', 'High', 'Low', ..., 'Low', 'High', 'High'], dtype='<U4')
```





{:.input_area}
```python
wine_df['prediction']  = nb_classifier.predict(review_word_counts)
```




{:.input_area}
```python
pd.crosstab(wine_df['rating'], wine_df['prediction'])
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
      <th>prediction</th>
      <th>High</th>
      <th>Low</th>
    </tr>
    <tr>
      <th>rating</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>High</th>
      <td>26996</td>
      <td>6639</td>
    </tr>
    <tr>
      <th>Low</th>
      <td>7884</td>
      <td>43609</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
nb_classifier.predict_proba(review_word_counts)
```





{:.output .output_data_text}
```
array([[0.05837039, 0.94162961],
       [0.8189896 , 0.1810104 ],
       [0.0073304 , 0.9926696 ],
       ...,
       [0.31987064, 0.68012936],
       [0.92064944, 0.07935056],
       [0.65113375, 0.34886625]])
```





{:.input_area}
```python
predict_df = pd.DataFrame(nb_classifier.predict_proba(review_word_counts), 
                          columns=nb_classifier.classes_)
```




{:.input_area}
```python
predict_df.head()
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
      <th>High</th>
      <th>Low</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.058370</td>
      <td>0.941630</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.818990</td>
      <td>0.181010</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.007330</td>
      <td>0.992670</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.009965</td>
      <td>0.990035</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.017779</td>
      <td>0.982221</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
wine_df_prediction = pd.concat([wine_df, predict_df], axis = 1)
```




{:.input_area}
```python
wine_df_prediction.sort_values('High', ascending=False)[['description','points']].head(15)
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
      <th>description</th>
      <th>points</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>79481</th>
      <td>From a beautifully exposed southwest facing vineyard with views of the Pyrenees, this is a serious and impressive wi...</td>
      <td>96</td>
    </tr>
    <tr>
      <th>67748</th>
      <td>Dark, rich mountain blueberry and blackberry form the core of this classically delicious Napa Valley wine from an es...</td>
      <td>93</td>
    </tr>
    <tr>
      <th>5129</th>
      <td>A blend of 28% Cabernet Franc, 23% Cabernet Sauvignon, 21% Malbec, 18% Petit Verdot and 10% Merlot, this is a big, b...</td>
      <td>94</td>
    </tr>
    <tr>
      <th>25463</th>
      <td>A blend of 28% Cabernet Franc, 23% Cabernet Sauvignon, 21% Malbec, 18% Petit Verdot and 10% Merlot, this is a big, b...</td>
      <td>94</td>
    </tr>
    <tr>
      <th>64570</th>
      <td>A superb wine from a great year, this is powerful and structured, with great acidity and solid, pronounced fruits. L...</td>
      <td>96</td>
    </tr>
    <tr>
      <th>49526</th>
      <td>This Ferreirinha Douro Superior wine is made in exceptional years. The 2007 is the 16th vintage since 1960 (the prev...</td>
      <td>97</td>
    </tr>
    <tr>
      <th>58945</th>
      <td>A blend of 57% Cabernet Sauvignon, 14% Merlot, 13% Malbec, 11% Cabernet Franc and 5% Petit Verdot, this stunning, we...</td>
      <td>93</td>
    </tr>
    <tr>
      <th>3958</th>
      <td>This is an enormous Cabernet, as packed with intensity and power as anything in Napa Valley. The vineyard is Von Str...</td>
      <td>95</td>
    </tr>
    <tr>
      <th>5115</th>
      <td>A proprietary blend of 57% Merlot, 35% Cabernet Sauvignon and 8% Petit Verdot, all homegrown, this is dense and powe...</td>
      <td>94</td>
    </tr>
    <tr>
      <th>36344</th>
      <td>Mature dark-skinned berry, leather, underbrush and dark spice are some of the aromas that emerge on this fantastic r...</td>
      <td>97</td>
    </tr>
    <tr>
      <th>68457</th>
      <td>After a great inaugural 2010 vintage, this is another impressive Cabernet from this winery. The vineyard is in the P...</td>
      <td>95</td>
    </tr>
    <tr>
      <th>41876</th>
      <td>This is a bold and powerful yet layered and nuanced wine, with hints of green peppercorn, tobacco leaf, cigar box an...</td>
      <td>93</td>
    </tr>
    <tr>
      <th>73422</th>
      <td>A beautifully dense, ripe wine, its intense acidity balanced by an opulent structure and gorgeous fruits. The textur...</td>
      <td>97</td>
    </tr>
    <tr>
      <th>77340</th>
      <td>From one of the top estates in Cahors, this complex, dense wine is both structured and packed with great fruit. At t...</td>
      <td>94</td>
    </tr>
    <tr>
      <th>70514</th>
      <td>This comes from 30-year-old vines in the Quinta da Manoella, the family vineyard of Jorge Borges, one of the winemak...</td>
      <td>91</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
wine_df_prediction.sort_values('Low', ascending=False)[['description','points']].head(15)
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
      <th>description</th>
      <th>points</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>26592</th>
      <td>Lemon citrus, toast, white flowers—the lead on this wine is feminine and light and, as the name suggests, feels like...</td>
      <td>84</td>
    </tr>
    <tr>
      <th>39450</th>
      <td>Reasonably accurate on the nose for Leyda Sauvignon Blanc, but also a little pickled smelling. Feels chunky and a li...</td>
      <td>85</td>
    </tr>
    <tr>
      <th>63660</th>
      <td>Apple and mineral aromas are basic but clean, and the palate is fresh and lithe, with little to no extra weight. Fla...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>16200</th>
      <td>Lively aromas of grapefruit, white flowers and mineral lead into a light, fruity but rather simple palate that offer...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>30861</th>
      <td>Simple but solid apple and nectarine aromas are straight forward. This feels round and easy, without much acid-based...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>21333</th>
      <td>Simple but solid apple and nectarine aromas are straight forward. This feels round and easy, without much acid-based...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>52219</th>
      <td>Fruity on the nose, with a friendly mix of pineapple, apple, melon and powdered sugar aromas. Feels smooth and round...</td>
      <td>87</td>
    </tr>
    <tr>
      <th>43618</th>
      <td>A medium-bodied Bordeaux blend with sweet aromas of cherry pie and a hint of fresh sage and tarragon. Simple and str...</td>
      <td>85</td>
    </tr>
    <tr>
      <th>66311</th>
      <td>Dry, mild, dusty berry aromas are simple but correct for the variety. This feels scattered across the palate, with s...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>58458</th>
      <td>Slightly stalky, roasted aromas of earthy plum and berry lean in the direction of compost. This feels light and some...</td>
      <td>87</td>
    </tr>
    <tr>
      <th>13944</th>
      <td>This straightforward Verdicchio opens with subdued aromas of stone fruit and citrus. The palate is a bit lean but of...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>44783</th>
      <td>Simple green-apple aromas are innocuous. This is fresh, easy and light on the palate. Flavors of apple and sweet gre...</td>
      <td>87</td>
    </tr>
    <tr>
      <th>65070</th>
      <td>Citrus and peach aromas are basic but clean. The palate starts out fresh and easy before losing some steam, but alon...</td>
      <td>85</td>
    </tr>
    <tr>
      <th>84252</th>
      <td>This easy-drinking white marries citrus and green pear with just a hint of herbal spice. Simple and straightforward,...</td>
      <td>87</td>
    </tr>
    <tr>
      <th>54423</th>
      <td>Stylistically, this is a racy, no-oak wine that smells of pineapple and tastes of citrus and passion fruit. The mout...</td>
      <td>83</td>
    </tr>
  </tbody>
</table>
</div>
</div>



<div class="alert alert-info">
<h3> Your turn</h3>
<p> In your groups, how well do your models fit? What is the most Trumpish Trump speech? What is the least?

</div>



### What about overfitting?



{:.input_area}
```python
from sklearn.model_selection import train_test_split

train, test = train_test_split(wine_df, test_size=0.2)
```




{:.input_area}
```python
len(train)
```





{:.output .output_data_text}
```
68102
```





{:.input_area}
```python
len(test)
```





{:.output .output_data_text}
```
17026
```





{:.input_area}
```python
vectorizer = CountVectorizer(lowercase   = True,
                             ngram_range = (3,3),
                             max_df      = 1.00,
                             min_df      = .05,
                             max_features = None)

vectorizer.fit(train['description'])

vectorizer.get_feature_names()
```





{:.output .output_data_text}
```
['on the finish', 'on the nose', 'on the palate']
```





{:.input_area}
```python
X_train = vectorizer.transform(train['description'])
```




{:.input_area}
```python
nb_classifier.fit(X_train, train['rating'])
```





{:.output .output_data_text}
```
MultinomialNB(alpha=1.0, class_prior=None, fit_prior=True)
```





{:.input_area}
```python
from sklearn.metrics import classification_report, confusion_matrix, accuracy_score


```




{:.input_area}
```python
print(accuracy_score(train['rating'],
                     nb_classifier.predict(X_train)))


```


{:.output .output_stream}
```
0.8385216293207248

```



{:.input_area}
```python
print(confusion_matrix(train['rating'], 
                      nb_classifier.predict(X_train)))

```


{:.output .output_stream}
```
[[21780  4975]
 [ 6022 35325]]

```



{:.input_area}
```python
print(classification_report(train['rating'],
                       nb_classifier.predict(X_train)))


```


{:.output .output_stream}
```
             precision    recall  f1-score   support

       High       0.78      0.81      0.80     26755
        Low       0.88      0.85      0.87     41347

avg / total       0.84      0.84      0.84     68102


```

Precision: % of selected items that are correct 

Recall: % of correct items that are selected




{:.input_area}
```python
test_wf         = vectorizer.transform(test['description'])
test_prediction = nb_classifier.predict(test_wf)
```




{:.input_area}
```python
print(accuracy_score(test['rating'], test_prediction))
```


{:.output .output_stream}
```
0.8360742393985668

```



{:.input_area}
```python
print(classification_report(test['rating'], test_prediction))

```


{:.output .output_stream}
```
             precision    recall  f1-score   support

       High       0.79      0.81      0.80      6880
        Low       0.87      0.85      0.86     10146

avg / total       0.84      0.84      0.84     17026


```



{:.input_area}
```python
vectorizer = CountVectorizer(lowercase=True,
                             ngram_range = (1,1),
                             stop_words = 'english',
                             max_df = .60,
                             min_df = 5,
                             max_features = None)
```




{:.input_area}
```python
vectorizer.fit(train['description'])
X_train = vectorizer.transform(train['description'])
nb_classifier.fit(X_train, train['rating'])

```





{:.output .output_data_text}
```
MultinomialNB(alpha=1.0, class_prior=None, fit_prior=True)
```





{:.input_area}
```python
print(accuracy_score(train['rating'],
                     nb_classifier.predict(X_train)))


```


{:.output .output_stream}
```
0.8925435376347244

```



{:.input_area}
```python

print(accuracy_score(test['rating'],
                     nb_classifier.predict(vectorizer.transform(test['description']))))

```


{:.output .output_stream}
```
0.8863502877951368

```

<div class="alert alert-info">
<h3> Your turn</h3>
<p> What happens to your model if you change some of the parameters for your vectorizer? Be sure to spit the data between train and test!

</div>






{:.input_area}
```python
from sklearn.linear_model import LogisticRegression
```




{:.input_area}
```python
ln_classifier = LogisticRegression()
```




{:.input_area}
```python
vectorizer = CountVectorizer(lowercase=True,
                             ngram_range = (1,2),
                             stop_words = 'english',
                             max_df = .80,
                             min_df = .005,
                             max_features = None)

vectorizer.fit(train['description'])

print(len(vectorizer.get_feature_names()))
ln_classifier.fit(vectorizer.transform(train['description']), train['rating'])
```


{:.output .output_stream}
```
913

```




{:.output .output_data_text}
```
LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
```





{:.input_area}
```python
print(accuracy_score(train['rating'],
                     ln_classifier.predict(vectorizer.transform(train['description']))))




```


{:.output .output_stream}
```
0.9051863381398491

```



{:.input_area}
```python
print(accuracy_score(test['rating'],
                     ln_classifier.predict(vectorizer.transform(test['description']))))



```


{:.output .output_stream}
```
0.8981557617761071

```

### What about a different model?

<div class="alert alert-info">
<h3> Your turn</h3>
What is the out sample accuracy of a logistic regression model on your data?
<p><code> from sklearn.linear_model import LogisticRegression

</div>





![](images/knn1.png)



{:.input_area}
```python
from sklearn.neighbors import KNeighborsClassifier

knn_classifier = KNeighborsClassifier(n_neighbors = 3)
```




{:.input_area}
```python
from sklearn.feature_extraction.text import TfidfVectorizer
```




{:.input_area}
```python
tf_vector  = TfidfVectorizer(lowercase  =  True,
                             ngram_range = (1,1),
                             stop_words  = 'english',
                             max_df      = .60,
                             min_df      = .05,
                             max_features = None)
```




{:.input_area}
```python
asdfadsf
```




{:.input_area}
```python
tf_vector.fit(wine_df['description'])
```




{:.input_area}
```python
len(tf_vector.get_feature_names())
```




{:.input_area}
```python
review_tf = tf_vector.transform(wine_df['description'])
```




{:.input_area}
```python
wine_df['rating']
```




{:.input_area}
```python
knn_classifier.fit(review_tf, wine_df['rating'])
```




{:.input_area}
```python
knn_prediction = knn_classifier.predict(review_tf)
```




{:.input_area}
```python

print(accuracy_score(test['rating'], knn_prediction))


```




{:.input_area}
```python
print(classification_report(test['rating'], knn_prediction))
```


<div class="alert alert-info">
<h3> Your turn</h3>
<p> What does a k-nearest neigbhor for your speech dataset look like? How does the accuracy compare?
</div>





![](images/knn2.png)



{:.input_area}
```python
knn_classifier = KNeighborsClassifier(n_neighbors = 15)
```


### But what's the best fitting model?



{:.input_area}
```python
from sklearn.model_selection import GridSearchCV
```




{:.input_area}
```python
parameters = {'n_neighbors' : (2, 3, 4)}
              
```




{:.input_area}
```python
grid = GridSearchCV(KNeighborsClassifier(), parameters, cv=5)
```




{:.input_area}
```python
grid.fit(review_word_counts,
         wine_df['rating'])
```




{:.input_area}
```python
from sklearn.pipeline import Pipeline
from sklearn.model_selection import GridSearchCV
```




{:.input_area}
```python
pipeline = Pipeline([
                     ('vectorizer' , CountVectorizer()),
                     ('classifier' , MultinomialNB())
                    ])
```




{:.input_area}
```python
parameters = {'vectorizer__max_df' : (.2, .4),
              'vectorizer__min_df' : (100, 150)
             }
              
```




{:.input_area}
```python
grid_search = GridSearchCV(pipeline,
                           parameters,
                           n_jobs = -1,
                           cv = 3,
                           verbose = 1)
```




{:.input_area}
```python
grid_search.fit(wine_df['description'],
                wine_df['rating'])
```




{:.input_area}
```python
grid_search.best_score_
```




{:.input_area}
```python
grid_search.best_estimator_
```




{:.input_area}
```python
parameters = {'vectorizer__max_df'      : [.1, .15, .2, .25],
              'vectorizer__min_df'      : [25, 50, 100],
              'vectorizer__stop_words'  : [None, 'english'],
              'vectorizer__ngram_range' : [(1,1), (1,2)]
             }
              
```




{:.input_area}
```python
grid_search.best_score_
```




{:.input_area}
```python
grid_search = GridSearchCV(pipeline,
                           parameters,
                           n_jobs = -1,
                           cv = 5,
                           verbose = 1)
```




{:.input_area}
```python
grid_search.fit(wine_df_extremes['description'],
                wine_df_extremes['rating'])
```




{:.input_area}
```python
grid_search.best_score_
```




{:.input_area}
```python
df
```



{:.output .output_traceback_line}
```
---------------------------------------------------------------------------
```

{:.output .output_traceback_line}
```
NameError                                 Traceback (most recent call last)
```

{:.output .output_traceback_line}
```
<ipython-input-102-00cf07b74dcd> in <module>()
----> 1 df

```

{:.output .output_traceback_line}
```
NameError: name 'df' is not defined
```




{:.input_area}
```python
grid_search.best_estimator_.get_params
```


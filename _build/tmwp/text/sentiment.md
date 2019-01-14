---
interact_link: content/tmwp/text/sentiment.ipynb
title: 'pandas Basics'
prev_page:
  url: /tmwp/text/counting
  title: 'Python Basics'
next_page:
  url: 
  title: ''
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---

### Sentiment Analysis




A traditional method of analyzing texts is to compute the proportion of the words have positive connotations, negative connotations or are neutral. This method is commonly referred to as sentiment analysis. The typical approach to sentiment analysis is to see how many words in a text are also in a predefined list of words associated with a sentiment. So "I am having a bad day." might score a "1" on a negative sentiment scale for the presence of "bad" or a .17 because one of six of the words is negative. Some sentiment systems rank words on a scale, so that "terrific" might be a 5 while "fine" scores a 1. 

Some systems go beyond positive and negative. The proprietary LIWC program, for example, extends this to measure dozens of other word attributes, such as "tone", "analytic thinking", and "clout". More generally, these methods can be used whenever you have a list of words, and you want to count their occurrences in a set of texts.

This lesson introduces two different dictionaries that are available in Python, AFINN, and Vader. It concludes by showing how to analyze a text corpus for occurrences on  an arbitrary word list.

This lesson assumes your computer has an Anaconda Python 3.7 distribution installed.

#### AFINN

[AFINN](http://www2.imm.dtu.dk/pubdb/views/publication_details.php?id=6010) is an English word listed developed by Finn Årup Nielsen. Words scores range from minus five (negative) to plus five (positive). The English language dictionary consists of 2,477 coded words.

If this is your first time running this notebook, you may need to install it:

```
!pip install afinn
```



{:.input_area}
```python
from afinn import Afinn
```


After importing `Afinn`, you need to set the language, English (`en`), Danish (`da`), or emoticon (`emoticons`). 



{:.input_area}
```python
afinn = Afinn(language='en')
```


The `score` method returns the sum of word valence scores for a text string.



{:.input_area}
```python
afinn.score('Bad day.')
```





{:.output .output_data_text}
```
-3.0
```





{:.input_area}
```python
afinn.score('Good day.')
```





{:.output .output_data_text}
```
3.0
```





{:.input_area}
```python
afinn.score('Horrible, bad day.')
```





{:.output .output_data_text}
```
-6.0
```



Before using a sentiment dictionary, it is useful to see whether it is has any face validity.  To do that, we can look at a sample of the words from the list.

After importing the pandas library, the cell below will load word list as a pandas dataframe from the tab-delimited version on Afinn's GitHub page and display a sample of 20 words.



{:.input_area}
```python
import pandas as pd

afinn_wl_url = ('https://raw.githubusercontent.com'
                '/fnielsen/afinn/master/afinn/data/AFINN-111.txt')

afinn_wl_df = pd.read_csv(afinn_wl_url, 
                          header=None, 
                          sep='\t', 
                          names=['term', 'value'])

afinn_wl_df.sample(20)
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
      <th>term</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1700</th>
      <td>persecutes</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>1143</th>
      <td>hardier</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1219</th>
      <td>humiliated</td>
      <td>-3</td>
    </tr>
    <tr>
      <th>427</th>
      <td>collide</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>768</th>
      <td>doubtful</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>716</th>
      <td>disorganized</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>1380</th>
      <td>jocular</td>
      <td>2</td>
    </tr>
    <tr>
      <th>998</th>
      <td>flops</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>494</th>
      <td>controversially</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>1002</th>
      <td>fond</td>
      <td>2</td>
    </tr>
    <tr>
      <th>174</th>
      <td>arrogant</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>662</th>
      <td>dilemma</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>1504</th>
      <td>meaningful</td>
      <td>2</td>
    </tr>
    <tr>
      <th>35</th>
      <td>accusation</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>1610</th>
      <td>nosey</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>2122</th>
      <td>stimulating</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1833</th>
      <td>ratified</td>
      <td>2</td>
    </tr>
    <tr>
      <th>809</th>
      <td>eerie</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>375</th>
      <td>charm</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1534</th>
      <td>misinterpreted</td>
      <td>-2</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
%matplotlib inline

afinn_wl_df['value'].hist()
```





{:.output .output_data_text}
```
<matplotlib.axes._subplots.AxesSubplot at 0x1107b94a8>
```




{:.output .output_png}
![png](../../images/tmwp/text/sentiment_13_1.png)



We can use Afinn to analyze a larger text database.  Aashita Kesarwani put together a [corpus](https://www.kaggle.com/aashita/nyt-comments/home) of comments made to New York Times articles. I sampled 10,000 of these from April, 2017.  

Before opening this as a dataframe, I import pandas and enable graphs to be displayed in the Jupyter notebook.



{:.input_area}
```python
nyt_df = pd.read_json('data/nyt_201704_comments.json')
```




{:.input_area}
```python
nyt_df.info()
```


{:.output .output_stream}
```
<class 'pandas.core.frame.DataFrame'>
Int64Index: 10000 entries, 0 to 9999
Data columns (total 12 columns):
articleID           10000 non-null object
commentBody         10000 non-null object
commentID           10000 non-null int64
commentType         10000 non-null object
createDate          10000 non-null int64
editorsSelection    10000 non-null bool
recommendations     10000 non-null int64
replyCount          10000 non-null int64
sectionName         10000 non-null object
userDisplayName     9997 non-null object
userID              10000 non-null int64
userLocation        9999 non-null object
dtypes: bool(1), int64(5), object(6)
memory usage: 947.3+ KB

```



{:.input_area}
```python
nyt_df.head()
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
      <th>articleID</th>
      <th>commentBody</th>
      <th>commentID</th>
      <th>commentType</th>
      <th>createDate</th>
      <th>editorsSelection</th>
      <th>recommendations</th>
      <th>replyCount</th>
      <th>sectionName</th>
      <th>userDisplayName</th>
      <th>userID</th>
      <th>userLocation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>58ef8bfc7c459f24986da097</td>
      <td>Tragedies abound in war, precision munitions n...</td>
      <td>22148699</td>
      <td>comment</td>
      <td>1492123756</td>
      <td>False</td>
      <td>1</td>
      <td>0</td>
      <td>Middle East</td>
      <td>Bill Owens</td>
      <td>66371869</td>
      <td>Essex</td>
    </tr>
    <tr>
      <th>1</th>
      <td>58e5a1507c459f24986d8a56</td>
      <td>"...but then again, please get off my lawn" ma...</td>
      <td>22053980</td>
      <td>comment</td>
      <td>1491481436</td>
      <td>False</td>
      <td>6</td>
      <td>0</td>
      <td>Unknown</td>
      <td>Mike P</td>
      <td>56758055</td>
      <td>Long Island</td>
    </tr>
    <tr>
      <th>2</th>
      <td>58ff102d7c459f24986dbe81</td>
      <td>Just another flim-flam plan to shuffle mor...</td>
      <td>22263548</td>
      <td>comment</td>
      <td>1493128804</td>
      <td>False</td>
      <td>13</td>
      <td>1</td>
      <td>Politics</td>
      <td>giniajim</td>
      <td>1651431</td>
      <td>VA</td>
    </tr>
    <tr>
      <th>3</th>
      <td>58ec83fb7c459f24986d98cd</td>
      <td>What do you mean, nice try?  Moynihan Station ...</td>
      <td>22113999</td>
      <td>userReply</td>
      <td>1491924651</td>
      <td>False</td>
      <td>1</td>
      <td>0</td>
      <td>Unknown</td>
      <td>Guy Walker</td>
      <td>55823171</td>
      <td>New York City</td>
    </tr>
    <tr>
      <th>4</th>
      <td>58fcbc357c459f24986db9d0</td>
      <td>Where I live, in a city where cabs are plentif...</td>
      <td>22247141</td>
      <td>comment</td>
      <td>1492971817</td>
      <td>True</td>
      <td>124</td>
      <td>6</td>
      <td>Unknown</td>
      <td>plphillips</td>
      <td>18764882</td>
      <td>Washington DC</td>
    </tr>
  </tbody>
</table>
</div>
</div>



To estimate the Afinn sentiment score for all of the responses in the dataframe, we can `apply` the scorer to create a new column. This takes a couple of seconds.



{:.input_area}
```python
nyt_df['afinn_score'] = nyt_df['commentBody'].apply(afinn.score)
```


`describe` gives a sense of the distribution. 



{:.input_area}
```python
nyt_df['afinn_score'].describe()
```





{:.output .output_data_text}
```
count    10000.000000
mean        -0.283000
std          7.166188
min       -130.000000
25%         -3.000000
50%          0.000000
75%          3.000000
max         42.000000
Name: afinn_score, dtype: float64
```



It is also useful to sort by `afinn_score` to get a sense of what the extreme scoring comments look like. In this case, I subset the dataframe to display just the two relevant columns. 



{:.input_area}
```python
columns_to_display = ['commentBody', 'afinn_score']

nyt_df.sort_values(by='afinn_score')[columns_to_display].head(10)
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
      <th>commentBody</th>
      <th>afinn_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9348</th>
      <td>Well Bill, nobody will be able to say that you...</td>
      <td>-130.0</td>
    </tr>
    <tr>
      <th>5893</th>
      <td>"Don’t Weaken Title IX Campus Sex Assault Poli...</td>
      <td>-62.0</td>
    </tr>
    <tr>
      <th>1510</th>
      <td>Would you describe (former prime minister of I...</td>
      <td>-54.0</td>
    </tr>
    <tr>
      <th>3378</th>
      <td>"I disapprove of what you say, but I will defe...</td>
      <td>-54.0</td>
    </tr>
    <tr>
      <th>3956</th>
      <td>The ultimate weakness of violence is that it i...</td>
      <td>-52.0</td>
    </tr>
    <tr>
      <th>9353</th>
      <td>The “Dirty Muslim”\n\nShe is called a “Dirty M...</td>
      <td>-46.0</td>
    </tr>
    <tr>
      <th>7788</th>
      <td>Democracy and western civilization are doing j...</td>
      <td>-43.0</td>
    </tr>
    <tr>
      <th>4446</th>
      <td>Immigrants\n\nImmigration purge\nEverybody is...</td>
      <td>-42.0</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Factual error: There has been no "rapid fallof...</td>
      <td>-42.0</td>
    </tr>
    <tr>
      <th>7571</th>
      <td>This is all fine and dandy, except for the fac...</td>
      <td>-39.0</td>
    </tr>
  </tbody>
</table>
</div>
</div>



By default, the sort is ascending, mean the lowest scoring, or most negative comments, are displayed by `head`. The comments with highest score are shown with `tail`. 



{:.input_area}
```python
nyt_df.sort_values(by='afinn_score')[columns_to_display].tail(10)
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
      <th>commentBody</th>
      <th>afinn_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4949</th>
      <td>Aside from the question of whether positive th...</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>3617</th>
      <td>I found myself immensely enjoying this when I ...</td>
      <td>33.0</td>
    </tr>
    <tr>
      <th>7912</th>
      <td>How is prepping for the SATs gaming the system...</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>6085</th>
      <td>"'You Create That Chemistry': How Actors Fall ...</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>3971</th>
      <td>I'd like to see the discussion moved up a leve...</td>
      <td>37.0</td>
    </tr>
    <tr>
      <th>2523</th>
      <td>His lawyers are grasping at straws.... In the ...</td>
      <td>38.0</td>
    </tr>
    <tr>
      <th>3486</th>
      <td>I applaud the spirit of this column and agree ...</td>
      <td>38.0</td>
    </tr>
    <tr>
      <th>8495</th>
      <td>My goodness... This hit home for me in so many...</td>
      <td>39.0</td>
    </tr>
    <tr>
      <th>9717</th>
      <td>When I look at the American actresses of Clair...</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>9205</th>
      <td>"Driven | 2017 Porsche 911 Turbo S"\n\n  Since...</td>
      <td>42.0</td>
    </tr>
  </tbody>
</table>
</div>
</div>



One of the drawbacks to using the raw Afinn score is the that longer texts may yield higher values simply because they contain more words. To adjust for that, we can divide the score by the number of words in the text. 

The most straightforward way to count words in a Python string is to use the `split` method, which spilts a string based on white spaces, and then count the length of the resulting list.



{:.input_area}
```python
def word_count(text_string):
    '''Calculate the number of words in a string'''
    return len(text_string.split())
```




{:.input_area}
```python
word_count('This sentence has seven words in it.')
```





{:.output .output_data_text}
```
7
```



You can employ that function on our dataframe to create a new column, `word_count` using `appply` to the text column, `commentBody`.



{:.input_area}
```python
nyt_df['word_count'] = nyt_df['commentBody'].apply(word_count)
```




{:.input_area}
```python
nyt_df['word_count'].describe()
```





{:.output .output_data_text}
```
count    10000.000000
mean        73.459700
std         63.508284
min          2.000000
25%         26.000000
50%         53.000000
75%        100.000000
max        296.000000
Name: word_count, dtype: float64
```



We can divide the original score by the word count to produce `afinn_adjusted`. This isn't exactly a percentage variable, since word scores in Afinn can range from -5 to 5, but it is a useful adjustment to control for variable comment length. To make it clearer that this isn't a percent score, and to make the results more readable, the adjustment is multiplied by 100.



{:.input_area}
```python
nyt_df['afinn_adjusted'] = nyt_df['afinn_score'] / nyt_df['word_count'] * 100
```




{:.input_area}
```python
nyt_df['afinn_adjusted'].describe()
```





{:.output .output_data_text}
```
count    10000.000000
mean         0.216934
std         14.222974
min       -100.000000
25%         -6.000000
50%          0.000000
75%          5.882353
max        266.666667
Name: afinn_adjusted, dtype: float64
```



You can use `groupby` to see how the sentiment score varies by key characteristics, such as whether or not a New York Times editor highlighted the comment.



{:.input_area}
```python
nyt_df.groupby('editorsSelection')['afinn_adjusted'].describe()
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
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>editorsSelection</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>False</th>
      <td>9783.0</td>
      <td>0.245986</td>
      <td>14.302260</td>
      <td>-100.000000</td>
      <td>-6.000000</td>
      <td>0.0</td>
      <td>5.882353</td>
      <td>266.666667</td>
    </tr>
    <tr>
      <th>True</th>
      <td>217.0</td>
      <td>-1.092828</td>
      <td>9.952139</td>
      <td>-61.904762</td>
      <td>-5.504587</td>
      <td>0.0</td>
      <td>3.875969</td>
      <td>60.000000</td>
    </tr>
  </tbody>
</table>
</div>
</div>



The above syntax maybe a little complex to decipher.
* `nyt_df` is the dataframe we want to use;
* `.groupby('editorsSelection')` creates a pandas `groupby` object split by the values of `editorsSelection`;
* `['afinn_adjusted']` is the specific column we want to focus on;
* `.describe()` produces descriptive statistics for each of the groups.

Overall, the findings suggest that editors select comments with more of a negative tone. 

Pandas can also be used to create the absolute value of a variable using the `abs` method. This is useful for exploring to what extent, in this case, editors embrace or avoid comments that are extreme, either positive or negative.



{:.input_area}
```python
nyt_df['afinn_adjusted_abs'] = nyt_df['afinn_adjusted'].abs()
```




{:.input_area}
```python
nyt_df.groupby('editorsSelection')['afinn_adjusted_abs'].describe()
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
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>editorsSelection</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>False</th>
      <td>9783.0</td>
      <td>8.930247</td>
      <td>11.173974</td>
      <td>0.0</td>
      <td>2.301499</td>
      <td>5.960265</td>
      <td>11.721444</td>
      <td>266.666667</td>
    </tr>
    <tr>
      <th>True</th>
      <td>217.0</td>
      <td>6.399846</td>
      <td>7.687472</td>
      <td>0.0</td>
      <td>1.612903</td>
      <td>4.562738</td>
      <td>8.724832</td>
      <td>61.904762</td>
    </tr>
  </tbody>
</table>
</div>
</div>



Here, there seems to be some evidence that editors are avoiding comments with extreme sentiment, as values are lower across the board for the editor's selections. 

#### Vader

A second method for sentiment analysis is [vader](https://github.com/cjhutto/vaderSentiment)(Valence Aware Dictionary and sEntiment Reasoner). According to the authors, it is, "a lexicon and rule-based sentiment analysis tool that is specifically attuned to sentiments expressed in social media." 

As with Afinn, Vader measures both the strength and direction of sentiment. Based on the work of 10 coders, the Vader dictionary includes approximately 7,500 words, emoticons, emojis, acronyms, and commonly used slang. 

Unlike Afinn, Vader scores an entire text, not just words. This allows for the algorithm to adjust for negations, such as "not", booster words, such as "remarkably". It also scores words written in all caps as more intense. Vader returns the proportion of a text that is negative, positive, and neutral, along with a combined score. 

There is a version included with nltk (`from nltk.sentiment.vader import SentimentIntensityAnalyzer`) but a more recent version can be separately installed:


```
!pip install vaderSentiment
```




{:.input_area}
```python
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
```


Vader requires that you set up an analyzer.



{:.input_area}
```python
analyzer = SentimentIntensityAnalyzer()
```


The `polarity_scores` method returns a dictionary with four items. 
* `pos`, `neu`, and `neg`  are the proportions of text that fall in each category.
* `compound` is the normalized, weighted composite score. 



{:.input_area}
```python
analyzer.polarity_scores('Horrible bad day.')
```





{:.output .output_data_text}
```
{'compound': -0.7906, 'neg': 0.875, 'neu': 0.125, 'pos': 0.0}
```



One useful feature of Vader is that it is able to look at works in context and score appropriately. 



{:.input_area}
```python
analyzer.polarity_scores("At least it isn't a horrible book.")
```





{:.output .output_data_text}
```
{'compound': 0.431, 'neg': 0.0, 'neu': 0.637, 'pos': 0.363}
```



It also scores contemporary lingo and emojis.



{:.input_area}
```python
analyzer.polarity_scores('Today SUX!')
```





{:.output .output_data_text}
```
{'compound': -0.5461, 'neg': 0.779, 'neu': 0.221, 'pos': 0.0}
```





{:.input_area}
```python
analyzer.polarity_scores('💋')
```





{:.output .output_data_text}
```
{'compound': 0.4215, 'neg': 0.0, 'neu': 0.263, 'pos': 0.737}
```



Since Vader returns a dictionary (unlike Afinn which returns a single value), it is slightly more complicated to use it on an entire pandas dataframe.

First, apply the analyzer on the text column. 



{:.input_area}
```python
sentiment = nyt_df['commentBody'].apply(analyzer.polarity_scores)
```


Our new object `sentiment` is a series, where each item is a dictionary. This series can be unpacked into a dataframe.



{:.input_area}
```python
sentiment_df = pd.DataFrame(sentiment.tolist())

sentiment_df.head()
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
      <th>compound</th>
      <th>neg</th>
      <th>neu</th>
      <th>pos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.7783</td>
      <td>0.576</td>
      <td>0.424</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.3182</td>
      <td>0.000</td>
      <td>0.850</td>
      <td>0.150</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.5499</td>
      <td>0.069</td>
      <td>0.876</td>
      <td>0.055</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.9107</td>
      <td>0.033</td>
      <td>0.836</td>
      <td>0.131</td>
    </tr>
  </tbody>
</table>
</div>
</div>



The new sentiment dataframe can be merged with the original dataframe.



{:.input_area}
```python
nyt_df_sentiment = pd.concat([nyt_df,sentiment_df], axis = 1)
```




{:.input_area}
```python
nyt_df_sentiment.head()
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
      <th>articleID</th>
      <th>commentBody</th>
      <th>commentID</th>
      <th>commentType</th>
      <th>createDate</th>
      <th>editorsSelection</th>
      <th>recommendations</th>
      <th>replyCount</th>
      <th>sectionName</th>
      <th>userDisplayName</th>
      <th>userID</th>
      <th>userLocation</th>
      <th>afinn_score</th>
      <th>word_count</th>
      <th>afinn_adjusted</th>
      <th>afinn_adjusted_abs</th>
      <th>compound</th>
      <th>neg</th>
      <th>neu</th>
      <th>pos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>58ef8bfc7c459f24986da097</td>
      <td>Tragedies abound in war, precision munitions n...</td>
      <td>22148699</td>
      <td>comment</td>
      <td>1492123756</td>
      <td>False</td>
      <td>1</td>
      <td>0</td>
      <td>Middle East</td>
      <td>Bill Owens</td>
      <td>66371869</td>
      <td>Essex</td>
      <td>-4.0</td>
      <td>7</td>
      <td>-57.142857</td>
      <td>57.142857</td>
      <td>-0.7783</td>
      <td>0.576</td>
      <td>0.424</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>58e5a1507c459f24986d8a56</td>
      <td>"...but then again, please get off my lawn" ma...</td>
      <td>22053980</td>
      <td>comment</td>
      <td>1491481436</td>
      <td>False</td>
      <td>6</td>
      <td>0</td>
      <td>Unknown</td>
      <td>Mike P</td>
      <td>56758055</td>
      <td>Long Island</td>
      <td>1.0</td>
      <td>14</td>
      <td>7.142857</td>
      <td>7.142857</td>
      <td>0.3182</td>
      <td>0.000</td>
      <td>0.850</td>
      <td>0.150</td>
    </tr>
    <tr>
      <th>2</th>
      <td>58ff102d7c459f24986dbe81</td>
      <td>Just another flim-flam plan to shuffle mor...</td>
      <td>22263548</td>
      <td>comment</td>
      <td>1493128804</td>
      <td>False</td>
      <td>13</td>
      <td>1</td>
      <td>Politics</td>
      <td>giniajim</td>
      <td>1651431</td>
      <td>VA</td>
      <td>0.0</td>
      <td>12</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>58ec83fb7c459f24986d98cd</td>
      <td>What do you mean, nice try?  Moynihan Station ...</td>
      <td>22113999</td>
      <td>userReply</td>
      <td>1491924651</td>
      <td>False</td>
      <td>1</td>
      <td>0</td>
      <td>Unknown</td>
      <td>Guy Walker</td>
      <td>55823171</td>
      <td>New York City</td>
      <td>4.0</td>
      <td>104</td>
      <td>3.846154</td>
      <td>3.846154</td>
      <td>-0.5499</td>
      <td>0.069</td>
      <td>0.876</td>
      <td>0.055</td>
    </tr>
    <tr>
      <th>4</th>
      <td>58fcbc357c459f24986db9d0</td>
      <td>Where I live, in a city where cabs are plentif...</td>
      <td>22247141</td>
      <td>comment</td>
      <td>1492971817</td>
      <td>True</td>
      <td>124</td>
      <td>6</td>
      <td>Unknown</td>
      <td>plphillips</td>
      <td>18764882</td>
      <td>Washington DC</td>
      <td>8.0</td>
      <td>120</td>
      <td>6.666667</td>
      <td>6.666667</td>
      <td>0.9107</td>
      <td>0.033</td>
      <td>0.836</td>
      <td>0.131</td>
    </tr>
  </tbody>
</table>
</div>
</div>



If you intend to do this more than once, it can be useful to wrap the entire process into a single function that takes a dataframe and returns the datframe with the polarity columns appended. To clarify where the sentiment information comes from, the prefix `vader_` is added to each of the polarity scores.



{:.input_area}
```python
def vaderize(df, textfield):
    '''Compute the Vader polarity scores for a textfield. 
    Returns scores and original dataframe.'''

    analyzer = SentimentIntensityAnalyzer()

    print('Estimating polarity scores for %d cases.' % len(df))
    sentiment = df[textfield].apply(analyzer.polarity_scores)

    # convert to dataframe
    sdf = pd.DataFrame(sentiment.tolist()).add_prefix('vader_')

    # merge dataframes
    df_combined = pd.concat([df, sdf], axis=1)
    return df_combined
```




{:.input_area}
```python
df_vaderized = vaderize(nyt_df, 'commentBody')
```


{:.output .output_stream}
```
Estimating polarity scores for 10000 cases.

```



{:.input_area}
```python
df_vaderized.head()
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
      <th>articleID</th>
      <th>commentBody</th>
      <th>commentID</th>
      <th>commentType</th>
      <th>createDate</th>
      <th>editorsSelection</th>
      <th>recommendations</th>
      <th>replyCount</th>
      <th>sectionName</th>
      <th>userDisplayName</th>
      <th>userID</th>
      <th>userLocation</th>
      <th>afinn_score</th>
      <th>word_count</th>
      <th>afinn_adjusted</th>
      <th>afinn_adjusted_abs</th>
      <th>vader_compound</th>
      <th>vader_neg</th>
      <th>vader_neu</th>
      <th>vader_pos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>58ef8bfc7c459f24986da097</td>
      <td>Tragedies abound in war, precision munitions n...</td>
      <td>22148699</td>
      <td>comment</td>
      <td>1492123756</td>
      <td>False</td>
      <td>1</td>
      <td>0</td>
      <td>Middle East</td>
      <td>Bill Owens</td>
      <td>66371869</td>
      <td>Essex</td>
      <td>-4.0</td>
      <td>7</td>
      <td>-57.142857</td>
      <td>57.142857</td>
      <td>-0.7783</td>
      <td>0.576</td>
      <td>0.424</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>58e5a1507c459f24986d8a56</td>
      <td>"...but then again, please get off my lawn" ma...</td>
      <td>22053980</td>
      <td>comment</td>
      <td>1491481436</td>
      <td>False</td>
      <td>6</td>
      <td>0</td>
      <td>Unknown</td>
      <td>Mike P</td>
      <td>56758055</td>
      <td>Long Island</td>
      <td>1.0</td>
      <td>14</td>
      <td>7.142857</td>
      <td>7.142857</td>
      <td>0.3182</td>
      <td>0.000</td>
      <td>0.850</td>
      <td>0.150</td>
    </tr>
    <tr>
      <th>2</th>
      <td>58ff102d7c459f24986dbe81</td>
      <td>Just another flim-flam plan to shuffle mor...</td>
      <td>22263548</td>
      <td>comment</td>
      <td>1493128804</td>
      <td>False</td>
      <td>13</td>
      <td>1</td>
      <td>Politics</td>
      <td>giniajim</td>
      <td>1651431</td>
      <td>VA</td>
      <td>0.0</td>
      <td>12</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>58ec83fb7c459f24986d98cd</td>
      <td>What do you mean, nice try?  Moynihan Station ...</td>
      <td>22113999</td>
      <td>userReply</td>
      <td>1491924651</td>
      <td>False</td>
      <td>1</td>
      <td>0</td>
      <td>Unknown</td>
      <td>Guy Walker</td>
      <td>55823171</td>
      <td>New York City</td>
      <td>4.0</td>
      <td>104</td>
      <td>3.846154</td>
      <td>3.846154</td>
      <td>-0.5499</td>
      <td>0.069</td>
      <td>0.876</td>
      <td>0.055</td>
    </tr>
    <tr>
      <th>4</th>
      <td>58fcbc357c459f24986db9d0</td>
      <td>Where I live, in a city where cabs are plentif...</td>
      <td>22247141</td>
      <td>comment</td>
      <td>1492971817</td>
      <td>True</td>
      <td>124</td>
      <td>6</td>
      <td>Unknown</td>
      <td>plphillips</td>
      <td>18764882</td>
      <td>Washington DC</td>
      <td>8.0</td>
      <td>120</td>
      <td>6.666667</td>
      <td>6.666667</td>
      <td>0.9107</td>
      <td>0.033</td>
      <td>0.836</td>
      <td>0.131</td>
    </tr>
  </tbody>
</table>
</div>
</div>



The distribution of the combined variable shows peaks at the extremes and zero.



{:.input_area}
```python
%matplotlib inline


df_vaderized['vader_compound'].plot(kind='hist')


```





{:.output .output_data_text}
```
<matplotlib.axes._subplots.AxesSubplot at 0x115c7abe0>
```




{:.output .output_png}
![png](../../images/tmwp/text/sentiment_64_1.png)



Plotting the positive and negative scores shows that many comments have both attributes present.



{:.input_area}
```python
df_vaderized.plot.scatter(x='vader_pos', y = 'vader_neg')
```





{:.output .output_data_text}
```
<matplotlib.axes._subplots.AxesSubplot at 0x1166bb160>
```




{:.output .output_png}
![png](../../images/tmwp/text/sentiment_66_1.png)



Finally, unlike the Afinn score analysis, there's no strong evidence that the New York Times' editor selection is associated with the Vader sentiment scores.



{:.input_area}
```python
sentiment_variables = ['afinn_adjusted', 'vader_neg', 'vader_neu', 'vader_pos']

df_vaderized.groupby('editorsSelection')[sentiment_variables].mean()
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
      <th>afinn_adjusted</th>
      <th>vader_neg</th>
      <th>vader_neu</th>
      <th>vader_pos</th>
    </tr>
    <tr>
      <th>editorsSelection</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>False</th>
      <td>0.245986</td>
      <td>0.099534</td>
      <td>0.789326</td>
      <td>0.111140</td>
    </tr>
    <tr>
      <th>True</th>
      <td>-1.092828</td>
      <td>0.099820</td>
      <td>0.792991</td>
      <td>0.107161</td>
    </tr>
  </tbody>
</table>
</div>
</div>



#### Word List

Occasionally, you will have a sentiment list from a different source that you would like to use. Or, more generally, you have a word list about any subject, not just attitudes, and you want to count their occurrences in texts, such as the use of words associated with politics or hypothesis testing. 

The final section of the lesson shows the steps for building a function that can analyze texts for the presence of words on any given list. In this case, the sample list will be words associated with men that were assembled by [Danielle Sucher](https://github.com/DanielleSucher/Jailbreak-the-Patriarchy). 

The list is stored as a csv file. Pandas can be used to read the word list and turn it into a Python list.



{:.input_area}
```python
male_words_df = pd.read_csv('data/male_words.csv')
male_words_df.sample(10)
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
      <th>term</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18</th>
      <td>uncle</td>
    </tr>
    <tr>
      <th>36</th>
      <td>sons</td>
    </tr>
    <tr>
      <th>31</th>
      <td>boy</td>
    </tr>
    <tr>
      <th>19</th>
      <td>him</td>
    </tr>
    <tr>
      <th>9</th>
      <td>waiter</td>
    </tr>
    <tr>
      <th>32</th>
      <td>boys</td>
    </tr>
    <tr>
      <th>44</th>
      <td>male</td>
    </tr>
    <tr>
      <th>33</th>
      <td>dude</td>
    </tr>
    <tr>
      <th>24</th>
      <td>son</td>
    </tr>
    <tr>
      <th>26</th>
      <td>boyfriends</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
male_words_list = male_words_df['term'].values
```


The function that looks for cooccurences has two parts. A preliminary helper function transforms the original text string into a list of lower case words stripping out any punctuation. 



{:.input_area}
```python
def text_to_words(text):
    '''Transform a string to a list of words,
    removing all punctuation.'''
    text = text.lower()

    p = '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'
    text = ''.join([ch for ch in text if ch not in p])

    return text.split()
```




{:.input_area}
```python
text_to_words('Make this lower case and remove! All? Punctuation.')
```





{:.output .output_data_text}
```
['make', 'this', 'lower', 'case', 'and', 'remove', 'all', 'punctuation']
```



The main function takes two arguments, the text and the word list. First, the text string is transformed to a list using `text_to_words`. Second, a new list `intersection` is created which contains only those elements from the text list that are in the word list. Finally, the function returns the length of the intersection. 



{:.input_area}
```python
def count_occurences(text, word_list):
    '''Count occurences of words from a list in a text string.'''
    text_list = text_to_words(text)

    intersection = [w for w in text_list if w in word_list]

    return len(intersection)
```




{:.input_area}
```python
count_occurences('He went to the store.', male_words_list)
```





{:.output .output_data_text}
```
1
```



The function can now be applied on the Times dataframe to count ocurrences of male words in the comments. Since the function takes a second arguement, the word list, this is passed to the `apply` method as a tuple.



{:.input_area}
```python
nyt_df['male_words'] = nyt_df['commentBody'].apply(count_occurences, 
                                                   args=(male_words_list, ))
```


Most comments do not have words from our male list.



{:.input_area}
```python
nyt_df['male_words'].describe()
```





{:.output .output_data_text}
```
count    10000.000000
mean         1.101600
std          2.126812
min          0.000000
25%          0.000000
50%          0.000000
75%          1.000000
max         19.000000
Name: male_words, dtype: float64
```



Sorting the dataframe in descending order by the new variable reveals the comments with the highest number of male words. In order to view larger parts of the text field, I adjust the `max_colwidth` option in pandas.



{:.input_area}
```python
pd.set_option('display.max_colwidth', 1000)
nyt_df.sort_values(by='male_words', ascending=False)[['commentBody', 'male_words']].head()
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
      <th>commentBody</th>
      <th>male_words</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8386</th>
      <td>To interpret a change of words as a change of heart is a silly concept when considering what Trump says. He says, at any moment, what he sees will center attention on him and the result he wants is for people to like him. The point of saying uninformed inflammatory offhanded comments is to polarize a group large enough to feed his ego in 'support' of him. This is why he's attracted to the talking heads who garner big ratings and get rich by saying absurd things.\nHe would turn on 'his people' in a heartbeat if a larger group would fawn on him for doing so.\nThere is no policy other than his personal enrichment. Don't make the mistake of creating a reason on his motives than the one single principal, 'it's all about him'.\nIf the large block of people who hate his guts would fawn over him the moment he does something acceptable and I do mean the "moment" he would learn to feed on it. With him it's not complicated. We need to think and act like we're training a dog. Reward good behav...</td>
      <td>19</td>
    </tr>
    <tr>
      <th>6242</th>
      <td>Yesterday's chemical weapons attack in Syria and the recent subway bombing in St. Petersburg was a rude wake-up call for the president. After blithely announcing last year that he knew more about ISIS than the brass at the Pentagon and that he alone could fix the problems of the world, he now faces his first serious crisis and the global community will be watching closely to see how he responds. \n\nThe president brayed loudly to his red nation that he would bomb ISIS into submission, even at the expense of the non-combatant civilian population. It would be so easy, he said, to rid the world of terrorists who would cower and flee before American military might. Yesterday and last night, he didn't send out any tweets decrying the slaughter of civilians by Bashar al-Assad. His Twitter account was silent because now maybe he realizes that being president is more complicated than he thought it was. Idiotic and inane comments won't topple al-Assad's brutal regime.\n\nNo, this president ...</td>
      <td>19</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Nice try, Mr. Baker. Perhaps you wrote your stupefying article before Mr. Trump announced that he had invited the murdering thug Duterte of the Philippines to visit the White House. This is a man who has destroyed any vestige of due process or morality in his own country. So why does Trump honor him with a White House visti, during which I am sure he will gladly shake his hand? \n\nJust because Trump can occasionally read a speech that bears some resemblance to a normal presidential address and because once in a while he can be pulled back from the brink of disaster by the few rational heads around him does nothing to change who he is. I'm not going to repeat what everyone already knows about his character flaws, his intellectual weakness, and his appalling incuriousity and ignorance. But they are plain to see, and to celebrate his occasional lapses into sanity is only to underline how abnormal he is most of the time. So he now receives his intelligence briefings more often? We are...</td>
      <td>19</td>
    </tr>
    <tr>
      <th>2229</th>
      <td>I hate Mitch McConnel's politics. I am not entirely sure that I do not hate him.\nPower is something he has fought for and won with a focused singularity that takes ones breath away. Remember, he is the guy who said his sole focus was to make Barack Obama a one term President and he gave serious consideration to going after Ashley Judd for episodes of depression that she had experienced when she considered running against him. He is not inclusive, he does not advocate for the disadvantaged and he has not said one public word against the rampage of the Trump administration as it tears through the social safety net. His advocacy for coal was about corporations not individual miners. He has made bedfellows of the Christian Right and fiscal conservatives because they directly and indirectly to protect corporate interests.\n\nA conservative can capture in an empathetic way the best intentions of his\nPolitical opponent and acknowledge their worth. Mr. McConnel has no such interest. For ...</td>
      <td>19</td>
    </tr>
    <tr>
      <th>1574</th>
      <td>No one should have been surprised by Trump's character, or lack thereof. His narcissism and bombastic nonsense of always taking credit for the good things he really didn't do, and blaming others for the bad things he actually did, is pathetic. \n\nWhat was somewhat surprising is his stunning incompetence. He was elected with a populist message that Washington is broken and "he alone could fix it." No argument on the Washington is broken part, and the country was more than ready to throw out the establishment and bring in a "businessman" to shake things up. \n\nWhat may have been missed was the assumption that Trump must have had SOME level of competence in order to build and grow his businesses. But, what was the reality? He was born on third base and thought he hit a triple. He never had a boss except his "daddy." He never ran a public company, so he never had to report to a board that would keep him in check. And, of course, he's always surrounded himself with loyal sycophants wh...</td>
      <td>19</td>
    </tr>
  </tbody>
</table>
</div>
</div>



To highlight the flexibility of the `count_occurences` function, load a new list of female words, from the same source, in order to estimate the number of female words in each comment. After the word list is loaded, this is accomplished by supplying the new word list as an argument in the function. 



{:.input_area}
```python
female_words_df = pd.read_csv('data/female_words.csv')
female_words_list = female_words_df['term'].values

nyt_df['female_words'] = nyt_df['commentBody'].apply(count_occurences, args=(female_words_list, ))
```


Female words are rarer in comments.



{:.input_area}
```python
gender_words = ['male_words', 'female_words']
nyt_df[gender_words].describe()
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
      <th>male_words</th>
      <th>female_words</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>10000.000000</td>
      <td>10000.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1.101600</td>
      <td>0.301300</td>
    </tr>
    <tr>
      <th>std</th>
      <td>2.126812</td>
      <td>1.208993</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>19.000000</td>
      <td>27.000000</td>
    </tr>
  </tbody>
</table>
</div>
</div>



Finally, it appears that Editors select comments with more gendered words, both male and female. 



{:.input_area}
```python
nyt_df.groupby('editorsSelection')[gender_words].describe()
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

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="8" halign="left">male_words</th>
      <th colspan="8" halign="left">female_words</th>
    </tr>
    <tr>
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>editorsSelection</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>False</th>
      <td>9783.0</td>
      <td>1.096392</td>
      <td>2.122070</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>19.0</td>
      <td>9783.0</td>
      <td>0.296944</td>
      <td>1.198393</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>27.0</td>
    </tr>
    <tr>
      <th>True</th>
      <td>217.0</td>
      <td>1.336406</td>
      <td>2.323832</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>19.0</td>
      <td>217.0</td>
      <td>0.497696</td>
      <td>1.607633</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
    </tr>
  </tbody>
</table>
</div>
</div>



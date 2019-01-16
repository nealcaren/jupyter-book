---
interact_link: content/collecting/apis.ipynb
title: 'Web APIs'
prev_page:
  url: /collecting/file_reading
  title: 'From files'
next_page:
  url: /collecting/scraping
  title: 'Web Scraping'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---

# Session 3: Harvesting data from the web: APIs

### A first API

[Chronicling America](http://chroniclingamerica.loc.gov/about/) is a joint project of the National Endowment for the Humanities and the Library of Congress .

Search for articles that mention "[slavery](http://chroniclingamerica.loc.gov/search/pages/results/?andtext=slavery)".

<div class="alert alert-info">

Look at the URL. What happens if you change the word slavery to abolition? 

What happens to the URL when you go to the second page? Can you get to page 251?

</div>

What if we append ``&format=json`` to the end of the search URL? 


http://chroniclingamerica.loc.gov/search/pages/results/?andtext=slavery&format=json


[``requests``](http://docs.python-requests.org/en/master/) is a useful and commonly used HTTP library for python. It is not a part of the default installation, but is included with Anaconda Python Distribution. 



{:.input_area}
```python
import requests
```


It would be possible to use the API URL and parameters directly in the requests command, but since the most likely scenario involves making repeating calls to ``requests`` as part of a loop -- the search returned less than 1% of the results -- I store the strings first. 



{:.input_area}
```python
base_url   = 'http://chroniclingamerica.loc.gov/search/pages/results/'
parameters = '?andtext=slavery&format=json'
```


`requests.get()` is used for both accessing websites and APIs. The command can be modified by several arguements, but at a minimum, it requires the URL.



{:.input_area}
```python
r = requests.get(base_url + parameters)
```


`r` is a `requests` response object. Any JSON returned by the server are stored in `.json().`



{:.input_area}
```python
search_json = r.json()
```


JSONs are dictionary like objects, in that they have keys (think variable names) and values. `.keys()` returns a list of the keys.



{:.input_area}
```python
search_json.keys()
```


You can return the value of any key by putting the key name in brackets.



{:.input_area}
```python
search_json['totalItems']
```


<div class="alert alert-info">
What else is in there? Where is the stuff we want?
</div>

As is often the case with results from an API, most of the keys and values are metadate about either the search or what is being returned. These are useful for knowing if the search is returning what you want, which is particularly important when you are making multiple calls to the API. 

The data I'm intereted in is all in `items`. 



{:.input_area}
```python
type(search_json['items'])
```




{:.input_area}
```python
len(search_json['items'])
```


`items` is a list with 20 items.



{:.input_area}
```python
type(search_json['items'][3])
```


Each of the 20 items in the list is a dictionary. 



{:.input_area}
```python
first_item = search_json['items'][0]

first_item.keys()
```


<div class="alert alert-info">
What is the title of the first item?
</div>

While a standard CSV file has a header row that describes the contents of each column, a JSON file has keys identifying the values found in each case. Importantly, these keys need not be the same for each item. Additionally, values don't have to be numbers of strings, but could be lists or dictionaries. For example, this JSON could have included a `newspaper` key that was a dictionary with all the metadata about the newspaper the article and issue was published, an `article` key that include the article specific information as another dictionary, and a `text` key whose value was a string with the article text.

As before, we can examine the contents of a particular item, such as the publication's `title`.



{:.input_area}
```python
first_item['ocr_eng']
```




{:.input_area}
```python
print(first_item['ocr_eng'])
```


The easiest way to view or analyze this data is to convert it to a dataset-like structure. While Python does not have a builting in dataframe type, the popular `pandas` library does. By convention, it is imported as `pd`.



{:.input_area}
```python
print(first_item['ocr_eng'][:200])
```




{:.input_area}
```python
import pandas as pd

# Make sure all columns are displayed
pd.set_option("display.max_columns",101)
```


pandas is prety smart about importing different JSON-type objects and converting them to dataframes with its `.DataFrame()` function.



{:.input_area}
```python
df = pd.DataFrame(search_json['items'])

df.head(6)
```


Note that I converted `search_json['items']` to  dataframe and not the entire JSON file. This is because I wanted each row to be an article. 



{:.input_area}
```python
pd.DataFrame(search_json)
```


If this dataframe contained all the items that you were looking for, it would be easy to save this to a csv file for storage and later analysis.



{:.input_area}
```python
df.to_csv('lynching_articles.csv')
```




{:.input_area}
```python
df.to_csv('lynching_articles.csv', encoding='utf8')
```




{:.input_area}
```python
!head lynching_articles.csv
```


<div class="alert alert-info">
<h3> Your turn</h3>
<p> Conduct your own search of the API. Store the results in a csv file.

</div>





{:.input_area}
```python
r = requests.get('https://exchangeratesapi.io/api/latest?base=EUR')
```




{:.input_area}
```python
pd.DataFrame(r.json())
```


<div class="alert alert-info">
<h3> Your turn</h3>
<p>What is the current exchange rate using the Norwegian krone as the base rate?</code> Save the results in a new csv file.

</div>



This is only a small subset of the articles on lynching that are available, however. The API returns results in batches of 20 and this is only the first page of results. As is often the case, I'll need to make multiple calls to the API to retrieve all the data of interest. The easiest way to do that is to define a small function for getting the article information and put that in a loop. While it isn't a requirement that you create a function for making the API call, it will make your code easier to read and debug.


Looking at the API guidelines, there is an additional paramater `page` that tells the API which subset of results we want. This name varies by API but their is usually some mechanism for retrieiving results beyond the initial JSON.

Before creating the loop and making multiple calls to the API, I want to make sure that the API is working the way I think it is. 

<div class="alert alert-info">
Look at the API guidelines. How can we get the third page?
</div>


[Guidelines](https://chroniclingamerica.loc.gov/about/api/)



{:.input_area}
```python
base_url   = 'http://chroniclingamerica.loc.gov/search/pages/results/'
parameters = '?andtext=slavery&format=json&page=3'

r = requests.get(base_url + parameters)
results =  r.json()

print results['startIndex']
print results['endIndex']
```


A call to random selected page 3 returns results 41 through 60, which is what I expected since each page has 20 items.

The parameters are getting pretty ugly, so fortunately `requests` accepts a dictionary where the keys are the parameter names as defined by the API and the values are the search paramaters you are looking for. So the same request can be rewritten:



{:.input_area}
```python
base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
parameters = {'andtext': 'lynching',
              'page'   : 3,
              'format'  : 'json'}

r = requests.get(base_url, params=parameters)

results =  r.json()

results['startIndex'], results['endIndex']
```


This can be rewritten as function:



{:.input_area}
```python
def get_articles():
    '''
    Make calls to the Chronicling America API.
    '''

    base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
    parameters = {'andtext': 'lynching', 'page': 3, 'format': 'json'}

    r = requests.get(base_url, params=parameters)
    results = r.json()

    return results
```




{:.input_area}
```python
results = get_articles()

results['startIndex'], results['endIndex']
```


The advantage of writing a function, however, would be that you can pass along your own parameters, such as the search term and page number, which would make this much more useful. 



{:.input_area}
```python
def get_articles(search_term, page_number):
    '''
    Make calls to the Chronicling America API.
    '''
    
    base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
    parameters = {'andtext': search_term,
                  'page'   : page_number,
                  'format' : 'json'}
    
    r = requests.get(base_url, params = parameters)
    results =  r.json()

    return results
```




{:.input_area}
```python
results = get_articles('lynching', 3)

results['startIndex'], results['endIndex']
```




{:.input_area}
```python
results = get_articles('cows', 45)

results['startIndex'], results['endIndex']
```




{:.input_area}
```python
pd.DataFrame(results['items'])
```


<div class="alert alert-info">
<h3> Your turn</h3>
<p>This url
<p><code>https://itunes.apple.com/search?term=beyonce&entity=song</code>
<p>will return 50 songs in the iTunes store by Beyoncé.
<p>
<p>Write a function that will return the results of a call for any artists into a dataframe. Hint: inspect the contents of the resulting JSON to make sure you are getting what you want.</p>
<p>
<p><b>Bonus challenge:</b> Open up a new notebook. Se the first cell type to "Markdown" and write a brief introduction about your function. But your code in subsequent cells! Don't forget your <code>import</code> statements!  
    
</div>

[API Manual](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-web-service-search-api/)

Back to Chronicling America. Now, the first 60 results could downloaded in a just a few lines:



{:.input_area}
```python
for page_number in [1, 2, 3]: 
    print(page_number)
    
```




{:.input_area}
```python
for page_number in range(1, 4): 
    print(page_number)
    
```




{:.input_area}
```python
for page_number in range(1,4):
    
    results = get_articles('lynching', page_number)
    results['startIndex'], results['endIndex']
    
```


Everything appears to be working, but unfortunately I only have the last page of results still. Each call to the API was redefining `results` variable. In this case, I set up an empty dataframe to store the results and will append the items from each page of results.



{:.input_area}
```python
dfs = [] # empty list to store dataframes

for page_number in range(1,4):
    results = get_articles('lynching', page_number)
    new_df = pd.DataFrame(results['items'])
    
    dfs.append(new_df) 

df = pd.concat(dfs, ignore_index = True)
df.info()
```


For a large download, you would still want to tweak this a bit by pausing between each API call and making it robust to internet or API errors, but this is a solid framework for collecting data from an API.



{:.input_area}
```python
from time import sleep
```




{:.input_area}
```python
dfs = [] # empty list to store dataframes

for page_number in range(1,4):
    results = get_articles('lynching', page_number)
    new_df = pd.DataFrame(results['items'])
    
    dfs.append(new_df) 
    sleep(1)
    print('Getting page: ' + str(page_number))
    
df = pd.concat(dfs, ignore_index = True)
df.info()
```


<div class="alert alert-info">
<h3> Your turn</h3>
<p>Can you modify your iTunes search to get more results?  
    
</div>

[API Manual](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-web-service-search-api/)

### How about Twitter?



{:.input_area}
```python
import pandas as pd
```




{:.input_area}
```python
from twython import Twython
```


Sign up as a developer



{:.input_area}
```python
APP_KEY            = 'J8TGgv1SlKgAtqvxGZzc9XiNx'
APP_SECRET         = '8bEieGM73FLqbnWu6WcTR3vM6ICfEBEmQ8lXgqojw5IL1uzQ0Z'
OAUTH_TOKEN        = '594565064-erQRFPOFk520ePJjR86b9H2PTPxAF9i1d3A7pzjp'
OAUTH_TOKEN_SECRET = 'qGwK0Jz7f0YyYlQIoLSJ8FJCIX3ydFQBo0yJOePsaWXL9'


```


Store your credentials



{:.input_area}
```python
twitter = Twython(APP_KEY,
                  APP_SECRET,
                  OAUTH_TOKEN,
                  OAUTH_TOKEN_SECRET)
```


Start your searches!



{:.input_area}
```python
user_timeline = twitter.get_user_timeline(screen_name='oprah')
```




{:.input_area}
```python
pd.DataFrame(user_timeline)
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
      <th>contributors</th>
      <th>coordinates</th>
      <th>created_at</th>
      <th>entities</th>
      <th>favorite_count</th>
      <th>favorited</th>
      <th>geo</th>
      <th>id</th>
      <th>id_str</th>
      <th>in_reply_to_screen_name</th>
      <th>...</th>
      <th>is_quote_status</th>
      <th>lang</th>
      <th>place</th>
      <th>possibly_sensitive</th>
      <th>retweet_count</th>
      <th>retweeted</th>
      <th>source</th>
      <th>text</th>
      <th>truncated</th>
      <th>user</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>None</td>
      <td>Sat Dec 08 16:00:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>8860</td>
      <td>False</td>
      <td>None</td>
      <td>1071434225944068104</td>
      <td>1071434225944068104</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>1200</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Every father has a dream for their family and ...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>None</td>
      <td>None</td>
      <td>Fri Dec 07 16:00:10 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>3549</td>
      <td>False</td>
      <td>None</td>
      <td>1071071878901456896</td>
      <td>1071071878901456896</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>566</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>.@ItsGabrielleU and @DwyaneWade dispel many my...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 04 23:34:30 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>2714</td>
      <td>False</td>
      <td>None</td>
      <td>1070099055382908928</td>
      <td>1070099055382908928</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>375</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Why wait until after the holidays to get healt...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 04 18:51:13 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>10935</td>
      <td>False</td>
      <td>None</td>
      <td>1070027764072296448</td>
      <td>1070027764072296448</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>3175</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>This story struck my heart. I’ve done this a 1...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Nov 21 21:00:01 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>3928</td>
      <td>False</td>
      <td>None</td>
      <td>1065349132452122624</td>
      <td>1065349132452122624</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>551</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Roll call! Greenleafers…this is the EXPLOSIVE ...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Nov 20 22:12:30 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>11077</td>
      <td>False</td>
      <td>None</td>
      <td>1065004989372743680</td>
      <td>1065004989372743680</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>1159</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Look who I got to meet! Little Kaavia James, t...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Nov 19 21:00:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>5827</td>
      <td>False</td>
      <td>None</td>
      <td>1064624353227169792</td>
      <td>1064624353227169792</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>1035</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Did you know that 1 in 8 Americans struggles w...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>None</td>
      <td>None</td>
      <td>Sat Nov 17 17:00:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>18033</td>
      <td>False</td>
      <td>None</td>
      <td>1063839179958878210</td>
      <td>1063839179958878210</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>2518</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Seeing all your comments on social &amp;amp; feeli...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>None</td>
      <td>None</td>
      <td>Thu Nov 15 18:03:56 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>28843</td>
      <td>False</td>
      <td>None</td>
      <td>1063130492969480192</td>
      <td>1063130492969480192</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>5982</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Michelle, I never thought of it that way befor...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>None</td>
      <td>None</td>
      <td>Thu Nov 15 03:16:43 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>34</td>
      <td>False</td>
      <td>None</td>
      <td>1062907220382375936</td>
      <td>1062907220382375936</td>
      <td>Deborahjoywina1</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>6</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@Deborahjoywina1 @DeborahJWinans @MerleDandrid...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>None</td>
      <td>None</td>
      <td>Thu Nov 15 03:13:50 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>23</td>
      <td>False</td>
      <td>None</td>
      <td>1062906494621007872</td>
      <td>1062906494621007872</td>
      <td>3LWTV</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>9</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@3LWTV @OWNTV @MsLynnWhitfield @MerleDandridge...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Nov 14 00:32:29 +0000 2018</td>
      <td>{'hashtags': [{'text': 'IAmBecoming', 'indices...</td>
      <td>40397</td>
      <td>False</td>
      <td>None</td>
      <td>1062503501518028800</td>
      <td>1062503501518028800</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>2998</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>Hey Chicago! I'm sitting down with @MichelleOb...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Nov 12 14:11:26 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>20997</td>
      <td>False</td>
      <td>None</td>
      <td>1061984820404711427</td>
      <td>1061984820404711427</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>3535</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>From the very first pages of “Becoming” I knew...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>None</td>
      <td>None</td>
      <td>Thu Nov 08 03:25:12 +0000 2018</td>
      <td>{'hashtags': [{'text': 'GreenLeaf', 'indices':...</td>
      <td>22</td>
      <td>False</td>
      <td>None</td>
      <td>1060372639477198848</td>
      <td>1060372639477198848</td>
      <td>benita_janea</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@benita_janea 😂😂😂 course you knew! smart woman...</td>
      <td>False</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>None</td>
      <td>None</td>
      <td>Thu Nov 08 03:14:34 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>150</td>
      <td>False</td>
      <td>None</td>
      <td>1060369963343474688</td>
      <td>1060369963343474688</td>
      <td>ImKeithDavid</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>26</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@ImKeithDavid @MsLynnWhitfield @GreenleafOWN Y...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Nov 07 14:19:21 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>5927</td>
      <td>False</td>
      <td>None</td>
      <td>1060174872372887557</td>
      <td>1060174872372887557</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>False</td>
      <td>667</td>
      <td>False</td>
      <td>&lt;a href="https://studio.twitter.com" rel="nofo...</td>
      <td>Can you believe it? Halloween is behind us. El...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Nov 06 15:05:20 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>84101</td>
      <td>False</td>
      <td>None</td>
      <td>1059824056659132416</td>
      <td>1059824056659132416</td>
      <td>None</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>10268</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>I woke up feeling compelled to pray for our co...</td>
      <td>True</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>None</td>
      <td>None</td>
      <td>Sun Nov 04 03:46:47 +0000 2018</td>
      <td>{'hashtags': [{'text': 'READYTOLOVE', 'indices...</td>
      <td>29</td>
      <td>False</td>
      <td>None</td>
      <td>1058928517109374976</td>
      <td>1058928517109374976</td>
      <td>msbasketball1</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>8</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@msbasketball1 And you are super fun and super...</td>
      <td>False</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>None</td>
      <td>None</td>
      <td>Sun Nov 04 02:43:26 +0000 2018</td>
      <td>{'hashtags': [{'text': 'readytolove', 'indices...</td>
      <td>13</td>
      <td>False</td>
      <td>None</td>
      <td>1058912577978159106</td>
      <td>1058912577978159106</td>
      <td>priM0E</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@priM0E What would you suggest?  I’m Open! #re...</td>
      <td>False</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>None</td>
      <td>None</td>
      <td>Sun Nov 04 02:03:24 +0000 2018</td>
      <td>{'hashtags': [{'text': 'ChadlovesMichelle', 'i...</td>
      <td>10</td>
      <td>False</td>
      <td>None</td>
      <td>1058902502215639040</td>
      <td>1058902502215639040</td>
      <td>cherrilizabiff</td>
      <td>...</td>
      <td>False</td>
      <td>en</td>
      <td>None</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@cherrilizabiff @RealMichelleW @PastorCJ I fee...</td>
      <td>False</td>
      <td>{'id': 19397785, 'id_str': '19397785', 'name':...</td>
    </tr>
  </tbody>
</table>
<p>20 rows × 24 columns</p>
</div>
</div>





<div class="alert alert-info">
<h3> Your turn</h3>
<p> Find the tweets from someone else. If you add <code>, count = 200 </code> after the username, you can get up to 200 tweets. Do it!

</div>





{:.input_area}
```python
python_tweets = twitter.search(q='ipynb', count=200)
```




{:.input_area}
```python
pd.DataFrame(python_tweets['statuses'])
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
      <th>contributors</th>
      <th>coordinates</th>
      <th>created_at</th>
      <th>entities</th>
      <th>extended_entities</th>
      <th>favorite_count</th>
      <th>favorited</th>
      <th>geo</th>
      <th>id</th>
      <th>id_str</th>
      <th>...</th>
      <th>quoted_status</th>
      <th>quoted_status_id</th>
      <th>quoted_status_id_str</th>
      <th>retweet_count</th>
      <th>retweeted</th>
      <th>retweeted_status</th>
      <th>source</th>
      <th>text</th>
      <th>truncated</th>
      <th>user</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:53:57 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072821855306825728</td>
      <td>1072821855306825728</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 369558203, 'id_str': '369558203', 'name...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:23:17 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072814138756354050</td>
      <td>1072814138756354050</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>stil transferi ile alakalı bir başka güzel dem...</td>
      <td>False</td>
      <td>{'id': 68239920, 'id_str': '68239920', 'name':...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:14:33 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072811943663153154</td>
      <td>1072811943663153154</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 1083927277, 'id_str': '1083927277', 'na...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:03:29 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072809155159883776</td>
      <td>1072809155159883776</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 636890886, 'id_str': '636890886', 'name...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:51:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072609199291547648, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072806089765216257</td>
      <td>1072806089765216257</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 21:49:07 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps:...</td>
      <td>False</td>
      <td>{'id': 2370471067, 'id_str': '2370471067', 'na...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:39:30 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072803120068128770</td>
      <td>1072803120068128770</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Hint of the day\nProbability Density Functions...</td>
      <td>True</td>
      <td>{'id': 126638564, 'id_str': '126638564', 'name...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:36:36 +0000 2018</td>
      <td>{'hashtags': [{'text': 'bpstudy', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072802391966154752</td>
      <td>1072802391966154752</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:36:08 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @mamono_jingu: https://t.co/fp8rtGXOie #bps...</td>
      <td>False</td>
      <td>{'id': 14946295, 'id_str': '14946295', 'name':...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:36:08 +0000 2018</td>
      <td>{'hashtags': [{'text': 'bpstudy', 'indices': [...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072802274823495682</td>
      <td>1072802274823495682</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>https://t.co/fp8rtGXOie #bpstudy googleコラボラトリー...</td>
      <td>False</td>
      <td>{'id': 623559636, 'id_str': '623559636', 'name...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:29:02 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072800488461090816</td>
      <td>1072800488461090816</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 611662254, 'id_str': '611662254', 'name...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:22:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>None</td>
      <td>1072798736676470784</td>
      <td>1072798736676470784</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>What's the orbital future of MarCO-A &amp;amp; B? ...</td>
      <td>True</td>
      <td>{'id': 989455044650119168, 'id_str': '98945504...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 09:32:08 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072786166569689089</td>
      <td>1072786166569689089</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 00:29:01 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @realpython: Unicorn! · GitHub https://t.co...</td>
      <td>False</td>
      <td>{'id': 24900939, 'id_str': '24900939', 'name':...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 06:48:33 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072745000226631680</td>
      <td>1072745000226631680</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>yeah, I can tell that MHRD is really practical...</td>
      <td>False</td>
      <td>{'id': 5283722, 'id_str': '5283722', 'name': '...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 05:44:59 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072729004203855873</td>
      <td>1072729004203855873</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 18:25:14 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @jeremyphoward: @honnibal I absolutely do. ...</td>
      <td>False</td>
      <td>{'id': 417985944, 'id_str': '417985944', 'name...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 05:38:22 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072609199291547648, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072727339279106048</td>
      <td>1072727339279106048</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 21:49:07 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps:...</td>
      <td>False</td>
      <td>{'id': 892732157294030848, 'id_str': '89273215...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:56:31 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072716808749105152</td>
      <td>1072716808749105152</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 02:34:35 +0000 2018...</td>
      <td>&lt;a href="https://mobile.twitter.com" rel="nofo...</td>
      <td>RT @bhutanisanyam1: A demo of checkpointing yo...</td>
      <td>False</td>
      <td>{'id': 2918040270, 'id_str': '2918040270', 'na...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:26:12 +0000 2018</td>
      <td>{'hashtags': [{'text': 'python', 'indices': [1...</td>
      <td>{'media': [{'id': 1071426422747598848, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072709175669547009</td>
      <td>1072709175669547009</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41</td>
      <td>False</td>
      <td>{'created_at': 'Sat Dec 08 15:29:35 +0000 2018...</td>
      <td>&lt;a href="https://mobile.twitter.com" rel="nofo...</td>
      <td>RT @algoritmic: A Jupyter notebook on how to p...</td>
      <td>False</td>
      <td>{'id': 321699282, 'id_str': '321699282', 'name...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:18:43 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072707292917772288</td>
      <td>1072707292917772288</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 2953663351, 'id_str': '2953663351', 'na...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:13:55 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072609199291547648, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072706085339811840</td>
      <td>1072706085339811840</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 21:49:07 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps:...</td>
      <td>False</td>
      <td>{'id': 941535479312158720, 'id_str': '94153547...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:57:59 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072686977554669570</td>
      <td>1072686977554669570</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 5294462, 'id_str': '5294462', 'name': '...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:55:55 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072686455519010816</td>
      <td>1072686455519010816</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 417131533, 'id_str': '417131533', 'name...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:51:21 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072685308930490369</td>
      <td>1072685308930490369</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 02:34:35 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @bhutanisanyam1: A demo of checkpointing yo...</td>
      <td>False</td>
      <td>{'id': 782469380667944960, 'id_str': '78246938...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:39:35 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072682345537421312</td>
      <td>1072682345537421312</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@hardmaru check out my notebook for Deep Pensi...</td>
      <td>True</td>
      <td>{'id': 2725381, 'id_str': '2725381', 'name': '...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:34:35 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>13</td>
      <td>False</td>
      <td>None</td>
      <td>1072681087367827456</td>
      <td>1072681087367827456</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>A demo of checkpointing your models to Google ...</td>
      <td>False</td>
      <td>{'id': 784597005825871876, 'id_str': '78459700...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 01:08:06 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072659323472482305</td>
      <td>1072659323472482305</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>@realpython Link: https://t.co/7kPtBJ1S1j</td>
      <td>False</td>
      <td>{'id': 587744668, 'id_str': '587744668', 'name...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 01:07:55 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072659278744485888</td>
      <td>1072659278744485888</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 18:25:14 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @jeremyphoward: @honnibal I absolutely do. ...</td>
      <td>False</td>
      <td>{'id': 911059352, 'id_str': '911059352', 'name...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 00:29:01 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>None</td>
      <td>1072649488043524097</td>
      <td>1072649488043524097</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://recurpost.com" rel="nofollow"...</td>
      <td>Unicorn! · GitHub https://t.co/uErXjAbpov Mult...</td>
      <td>False</td>
      <td>{'id': 745911914, 'id_str': '745911914', 'name...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:29:48 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072634585257775106</td>
      <td>1072634585257775106</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 65857278, 'id_str': '65857278', 'name':...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:23:57 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072633112398585857</td>
      <td>1072633112398585857</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.072575e+18</td>
      <td>1072574516931497986</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:42:36 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sar...</td>
      <td>False</td>
      <td>{'id': 3299730071, 'id_str': '3299730071', 'na...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:23:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072632896480075778</td>
      <td>1072632896480075778</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.072575e+18</td>
      <td>1072574516931497986</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:42:36 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sar...</td>
      <td>False</td>
      <td>{'id': 3698212043, 'id_str': '3698212043', 'na...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:12:51 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072630322045566976</td>
      <td>1072630322045566976</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 18:25:14 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @jeremyphoward: @honnibal I absolutely do. ...</td>
      <td>False</td>
      <td>{'id': 27330241, 'id_str': '27330241', 'name':...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 00:14:38 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>None</td>
      <td>1072283480522919937</td>
      <td>1072283480522919937</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>Just found Google colab via @cwcyau cc @rmcolq...</td>
      <td>True</td>
      <td>{'id': 2191109263, 'id_str': '2191109263', 'na...</td>
    </tr>
    <tr>
      <th>71</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 23:50:01 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072277287104434179</td>
      <td>1072277287104434179</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 23:29:38 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかな...</td>
      <td>False</td>
      <td>{'id': 25090605, 'id_str': '25090605', 'name':...</td>
    </tr>
    <tr>
      <th>72</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 23:29:38 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>12</td>
      <td>False</td>
      <td>None</td>
      <td>1072272157802094592</td>
      <td>1072272157802094592</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.c...</td>
      <td>False</td>
      <td>{'id': 4167551, 'id_str': '4167551', 'name': '...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:51:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072262431915851776</td>
      <td>1072262431915851776</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 21:22:16 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @fmasanori: Análise de Dados sobre AIDS, fe...</td>
      <td>False</td>
      <td>{'id': 1093788270, 'id_str': '1093788270', 'na...</td>
    </tr>
    <tr>
      <th>74</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:47:48 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072261626747731968</td>
      <td>1072261626747731968</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 05 13:39:11 +0000 2018...</td>
      <td>&lt;a href="https://mobile.twitter.com" rel="nofo...</td>
      <td>RT @ikkoham: 量子Advent Calendarの記事書いた。\nQiskitの...</td>
      <td>False</td>
      <td>{'id': 320951147, 'id_str': '320951147', 'name...</td>
    </tr>
    <tr>
      <th>75</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:38:14 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072259222543446016</td>
      <td>1072259222543446016</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>@seanmylaw I see why this can be misleading, b...</td>
      <td>True</td>
      <td>{'id': 865622395, 'id_str': '865622395', 'name...</td>
    </tr>
    <tr>
      <th>76</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:36:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072258732048990208, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072258735475777542</td>
      <td>1072258735475777542</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://collabmarket.org/" rel="nofol...</td>
      <td>Riesgo Sistemico fecha 2018-12-10\n https://t....</td>
      <td>False</td>
      <td>{'id': 721868325576404992, 'id_str': '72186832...</td>
    </tr>
    <tr>
      <th>77</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:25:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072255909504532481</td>
      <td>1072255909504532481</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 01:16:39 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @LivingHistUON: It’s not easy to get nice, ...</td>
      <td>False</td>
      <td>{'id': 350683618, 'id_str': '350683618', 'name...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:24:30 +0000 2018</td>
      <td>{'hashtags': [{'text': 'fitting', 'indices': [...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072255766176911360</td>
      <td>1072255766176911360</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>Highlight of the day: generic function #fittin...</td>
      <td>False</td>
      <td>{'id': 944291984675614721, 'id_str': '94429198...</td>
    </tr>
    <tr>
      <th>79</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:57:39 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072249007085367296</td>
      <td>1072249007085367296</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 21:22:16 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @fmasanori: Análise de Dados sobre AIDS, fe...</td>
      <td>False</td>
      <td>{'id': 459268029, 'id_str': '459268029', 'name...</td>
    </tr>
    <tr>
      <th>80</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:55:04 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072248356859273217</td>
      <td>1072248356859273217</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.071697e+18</td>
      <td>1071696984942219264</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 07:16:41 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @betatim: If you'd like to try the nbtutor ...</td>
      <td>False</td>
      <td>{'id': 2174482088, 'id_str': '2174482088', 'na...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:42:11 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072245115555594252</td>
      <td>1072245115555594252</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.071697e+18</td>
      <td>1071696984942219264</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 07:16:41 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @betatim: If you'd like to try the nbtutor ...</td>
      <td>False</td>
      <td>{'id': 933014576647548928, 'id_str': '93301457...</td>
    </tr>
    <tr>
      <th>82</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:26:58 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>8</td>
      <td>False</td>
      <td>None</td>
      <td>1072241286390956033</td>
      <td>1072241286390956033</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Outro interessante: Análise de Óbitos Fetais e...</td>
      <td>False</td>
      <td>{'id': 14502701, 'id_str': '14502701', 'name':...</td>
    </tr>
    <tr>
      <th>83</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:22:16 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>29</td>
      <td>False</td>
      <td>None</td>
      <td>1072240104943558656</td>
      <td>1072240104943558656</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Análise de Dados sobre AIDS, feito por um alun...</td>
      <td>False</td>
      <td>{'id': 14502701, 'id_str': '14502701', 'name':...</td>
    </tr>
    <tr>
      <th>84</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:20:07 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072239563001729024</td>
      <td>1072239563001729024</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://xa.bi/tek_news" rel="nofollow"...</td>
      <td>HNews: Movie genre prediction with the Object2...</td>
      <td>False</td>
      <td>{'id': 63468443, 'id_str': '63468443', 'name':...</td>
    </tr>
    <tr>
      <th>85</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:12:06 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>None</td>
      <td>1072237546007027712</td>
      <td>1072237546007027712</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>少々前に話題になったZITが現時点で付与されている特許の要約からのトピック分析もざっくり行っ...</td>
      <td>False</td>
      <td>{'id': 314634742, 'id_str': '314634742', 'name...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 20:44:03 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072230485789630464, 'id_str...</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072230487450517504</td>
      <td>1072230487450517504</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://dlvrit.com/" rel="nofollow"&gt;d...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 900033256011255808, 'id_str': '90003325...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 19:02:12 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072204854804795392</td>
      <td>1072204854804795392</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 220472453, 'id_str': '220472453', 'name...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:59:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072204125415325696</td>
      <td>1072204125415325696</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>🛒🦍@amazon: "Amazon Object2Vec: Document Classi...</td>
      <td>True</td>
      <td>{'id': 4874962780, 'id_str': '4874962780', 'na...</td>
    </tr>
    <tr>
      <th>89</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:58:56 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072204030246576128</td>
      <td>1072204030246576128</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://github.com/d4nt/HNTweets" rel=...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 116276133, 'id_str': '116276133', 'name...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:58:13 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072203853314056193</td>
      <td>1072203853314056193</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://taragana.com" rel="nofollow"&gt;P...</td>
      <td>Amazon Object2Vec: Document Classification wit...</td>
      <td>False</td>
      <td>{'id': 6987882, 'id_str': '6987882', 'name': '...</td>
    </tr>
    <tr>
      <th>91</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:57:43 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072203724347637760</td>
      <td>1072203724347637760</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 15042473, 'id_str': '15042473', 'name':...</td>
    </tr>
    <tr>
      <th>92</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:57:31 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072203675328700417</td>
      <td>1072203675328700417</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 3065181529, 'id_str': '3065181529', 'na...</td>
    </tr>
    <tr>
      <th>93</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:55:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072203060229824513, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072203061915873280</td>
      <td>1072203061915873280</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://dlvrit.com/" rel="nofollow"&gt;d...</td>
      <td>Amazon Object2Vec: Document Classification wit...</td>
      <td>False</td>
      <td>{'id': 1029603103123759105, 'id_str': '1029603...</td>
    </tr>
    <tr>
      <th>94</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:49:48 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072201734456524810</td>
      <td>1072201734456524810</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://hnfp.c17r.com" rel="nofollow"&gt;...</td>
      <td>Amazon Object2Vec: Document Classification wit...</td>
      <td>False</td>
      <td>{'id': 4617024083, 'id_str': '4617024083', 'na...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 17:58:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>None</td>
      <td>1072188772605927424</td>
      <td>1072188772605927424</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Playing with Binder &amp;amp; ipywidgets… TIL a 30...</td>
      <td>False</td>
      <td>{'id': 112876914, 'id_str': '112876914', 'name...</td>
    </tr>
    <tr>
      <th>96</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 17:12:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072177122121277440</td>
      <td>1072177122121277440</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Fri Dec 07 16:12:12 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @alexandernitz: With all the recent gravita...</td>
      <td>False</td>
      <td>{'id': 310155354, 'id_str': '310155354', 'name...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 16:51:16 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072171905338220544</td>
      <td>1072171905338220544</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>157</td>
      <td>False</td>
      <td>{'created_at': 'Mon Nov 26 09:55:04 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @iamtrask: What if your email server kept y...</td>
      <td>False</td>
      <td>{'id': 929066238319394824, 'id_str': '92906623...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 16:32:27 +0000 2018</td>
      <td>{'hashtags': [{'text': 'TensorFlow', 'indices'...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072167169419608065</td>
      <td>1072167169419608065</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Btw, I've added an example how to setup Tensor...</td>
      <td>True</td>
      <td>{'id': 66489856, 'id_str': '66489856', 'name':...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 16:25:01 +0000 2018</td>
      <td>{'hashtags': [{'text': 'python', 'indices': [1...</td>
      <td>{'media': [{'id': 1071426422747598848, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072165296352432128</td>
      <td>1072165296352432128</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41</td>
      <td>False</td>
      <td>{'created_at': 'Sat Dec 08 15:29:35 +0000 2018...</td>
      <td>&lt;a href="https://buffer.com" rel="nofollow"&gt;Bu...</td>
      <td>RT @algoritmic: A Jupyter notebook on how to p...</td>
      <td>False</td>
      <td>{'id': 957799350163333122, 'id_str': '95779935...</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 30 columns</p>
</div>
</div>





{:.input_area}
```python
python_tweets
```




{:.input_area}
```python
python_tweets['search_metadata']
```




{:.input_area}
```python
python_tweets.keys()
```




{:.input_area}
```python
pd.DataFrame(python_tweets['statuses'])
```




{:.input_area}
```python
python_tweets = twitter.search(q     = 'ipynb', 
                               count = 200,
                               maxid = 1023186440514142207)                        
```




{:.input_area}
```python
pd.DataFrame(python_tweets['statuses'])
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
      <th>contributors</th>
      <th>coordinates</th>
      <th>created_at</th>
      <th>entities</th>
      <th>extended_entities</th>
      <th>favorite_count</th>
      <th>favorited</th>
      <th>geo</th>
      <th>id</th>
      <th>id_str</th>
      <th>...</th>
      <th>quoted_status</th>
      <th>quoted_status_id</th>
      <th>quoted_status_id_str</th>
      <th>retweet_count</th>
      <th>retweeted</th>
      <th>retweeted_status</th>
      <th>source</th>
      <th>text</th>
      <th>truncated</th>
      <th>user</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:53:57 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072821855306825728</td>
      <td>1072821855306825728</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 369558203, 'id_str': '369558203', 'name...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:23:17 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072814138756354050</td>
      <td>1072814138756354050</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>stil transferi ile alakalı bir başka güzel dem...</td>
      <td>False</td>
      <td>{'id': 68239920, 'id_str': '68239920', 'name':...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:14:33 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072811943663153154</td>
      <td>1072811943663153154</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 1083927277, 'id_str': '1083927277', 'na...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 11:03:29 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072809155159883776</td>
      <td>1072809155159883776</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 636890886, 'id_str': '636890886', 'name...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:51:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072609199291547648, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072806089765216257</td>
      <td>1072806089765216257</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 21:49:07 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps:...</td>
      <td>False</td>
      <td>{'id': 2370471067, 'id_str': '2370471067', 'na...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:39:30 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072803120068128770</td>
      <td>1072803120068128770</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Hint of the day\nProbability Density Functions...</td>
      <td>True</td>
      <td>{'id': 126638564, 'id_str': '126638564', 'name...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:36:36 +0000 2018</td>
      <td>{'hashtags': [{'text': 'bpstudy', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072802391966154752</td>
      <td>1072802391966154752</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:36:08 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @mamono_jingu: https://t.co/fp8rtGXOie #bps...</td>
      <td>False</td>
      <td>{'id': 14946295, 'id_str': '14946295', 'name':...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:36:08 +0000 2018</td>
      <td>{'hashtags': [{'text': 'bpstudy', 'indices': [...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072802274823495682</td>
      <td>1072802274823495682</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>https://t.co/fp8rtGXOie #bpstudy googleコラボラトリー...</td>
      <td>False</td>
      <td>{'id': 623559636, 'id_str': '623559636', 'name...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:29:02 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072800488461090816</td>
      <td>1072800488461090816</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 10:22:05 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @poliastro_py: What's the orbital future of...</td>
      <td>False</td>
      <td>{'id': 611662254, 'id_str': '611662254', 'name...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 10:22:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>None</td>
      <td>1072798736676470784</td>
      <td>1072798736676470784</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>What's the orbital future of MarCO-A &amp;amp; B? ...</td>
      <td>True</td>
      <td>{'id': 989455044650119168, 'id_str': '98945504...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 09:32:08 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072786166569689089</td>
      <td>1072786166569689089</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 00:29:01 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @realpython: Unicorn! · GitHub https://t.co...</td>
      <td>False</td>
      <td>{'id': 24900939, 'id_str': '24900939', 'name':...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 06:48:33 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072745000226631680</td>
      <td>1072745000226631680</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>yeah, I can tell that MHRD is really practical...</td>
      <td>False</td>
      <td>{'id': 5283722, 'id_str': '5283722', 'name': '...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 05:44:59 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072729004203855873</td>
      <td>1072729004203855873</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 18:25:14 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @jeremyphoward: @honnibal I absolutely do. ...</td>
      <td>False</td>
      <td>{'id': 417985944, 'id_str': '417985944', 'name...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 05:38:22 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072609199291547648, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072727339279106048</td>
      <td>1072727339279106048</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 21:49:07 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps:...</td>
      <td>False</td>
      <td>{'id': 892732157294030848, 'id_str': '89273215...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:56:31 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072716808749105152</td>
      <td>1072716808749105152</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 02:34:35 +0000 2018...</td>
      <td>&lt;a href="https://mobile.twitter.com" rel="nofo...</td>
      <td>RT @bhutanisanyam1: A demo of checkpointing yo...</td>
      <td>False</td>
      <td>{'id': 2918040270, 'id_str': '2918040270', 'na...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:26:12 +0000 2018</td>
      <td>{'hashtags': [{'text': 'python', 'indices': [1...</td>
      <td>{'media': [{'id': 1071426422747598848, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072709175669547009</td>
      <td>1072709175669547009</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41</td>
      <td>False</td>
      <td>{'created_at': 'Sat Dec 08 15:29:35 +0000 2018...</td>
      <td>&lt;a href="https://mobile.twitter.com" rel="nofo...</td>
      <td>RT @algoritmic: A Jupyter notebook on how to p...</td>
      <td>False</td>
      <td>{'id': 321699282, 'id_str': '321699282', 'name...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:18:43 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072707292917772288</td>
      <td>1072707292917772288</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 2953663351, 'id_str': '2953663351', 'na...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 04:13:55 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072609199291547648, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072706085339811840</td>
      <td>1072706085339811840</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 21:49:07 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps:...</td>
      <td>False</td>
      <td>{'id': 941535479312158720, 'id_str': '94153547...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:57:59 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072686977554669570</td>
      <td>1072686977554669570</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 5294462, 'id_str': '5294462', 'name': '...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:55:55 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072686455519010816</td>
      <td>1072686455519010816</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 417131533, 'id_str': '417131533', 'name...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:51:21 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072685308930490369</td>
      <td>1072685308930490369</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 12 02:34:35 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @bhutanisanyam1: A demo of checkpointing yo...</td>
      <td>False</td>
      <td>{'id': 782469380667944960, 'id_str': '78246938...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:39:35 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072682345537421312</td>
      <td>1072682345537421312</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>@hardmaru check out my notebook for Deep Pensi...</td>
      <td>True</td>
      <td>{'id': 2725381, 'id_str': '2725381', 'name': '...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 02:34:35 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>13</td>
      <td>False</td>
      <td>None</td>
      <td>1072681087367827456</td>
      <td>1072681087367827456</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>A demo of checkpointing your models to Google ...</td>
      <td>False</td>
      <td>{'id': 784597005825871876, 'id_str': '78459700...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 01:08:06 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072659323472482305</td>
      <td>1072659323472482305</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>@realpython Link: https://t.co/7kPtBJ1S1j</td>
      <td>False</td>
      <td>{'id': 587744668, 'id_str': '587744668', 'name...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 01:07:55 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072659278744485888</td>
      <td>1072659278744485888</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 18:25:14 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @jeremyphoward: @honnibal I absolutely do. ...</td>
      <td>False</td>
      <td>{'id': 911059352, 'id_str': '911059352', 'name...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>None</td>
      <td>None</td>
      <td>Wed Dec 12 00:29:01 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>None</td>
      <td>1072649488043524097</td>
      <td>1072649488043524097</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://recurpost.com" rel="nofollow"...</td>
      <td>Unicorn! · GitHub https://t.co/uErXjAbpov Mult...</td>
      <td>False</td>
      <td>{'id': 745911914, 'id_str': '745911914', 'name...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:29:48 +0000 2018</td>
      <td>{'hashtags': [{'text': 'orgmode', 'indices': [...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072634585257775106</td>
      <td>1072634585257775106</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:47:29 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @johnkitchin: Need a new way to make a slid...</td>
      <td>False</td>
      <td>{'id': 65857278, 'id_str': '65857278', 'name':...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:23:57 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072633112398585857</td>
      <td>1072633112398585857</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.072575e+18</td>
      <td>1072574516931497986</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:42:36 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sar...</td>
      <td>False</td>
      <td>{'id': 3299730071, 'id_str': '3299730071', 'na...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:23:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072632896480075778</td>
      <td>1072632896480075778</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.072575e+18</td>
      <td>1072574516931497986</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 22:42:36 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sar...</td>
      <td>False</td>
      <td>{'id': 3698212043, 'id_str': '3698212043', 'na...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 23:12:51 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072630322045566976</td>
      <td>1072630322045566976</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>{'created_at': 'Tue Dec 11 18:25:14 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @jeremyphoward: @honnibal I absolutely do. ...</td>
      <td>False</td>
      <td>{'id': 27330241, 'id_str': '27330241', 'name':...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>None</td>
      <td>None</td>
      <td>Tue Dec 11 00:14:38 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>7</td>
      <td>False</td>
      <td>None</td>
      <td>1072283480522919937</td>
      <td>1072283480522919937</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>Just found Google colab via @cwcyau cc @rmcolq...</td>
      <td>True</td>
      <td>{'id': 2191109263, 'id_str': '2191109263', 'na...</td>
    </tr>
    <tr>
      <th>71</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 23:50:01 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072277287104434179</td>
      <td>1072277287104434179</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 23:29:38 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかな...</td>
      <td>False</td>
      <td>{'id': 25090605, 'id_str': '25090605', 'name':...</td>
    </tr>
    <tr>
      <th>72</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 23:29:38 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>12</td>
      <td>False</td>
      <td>None</td>
      <td>1072272157802094592</td>
      <td>1072272157802094592</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.c...</td>
      <td>False</td>
      <td>{'id': 4167551, 'id_str': '4167551', 'name': '...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:51:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072262431915851776</td>
      <td>1072262431915851776</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 21:22:16 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @fmasanori: Análise de Dados sobre AIDS, fe...</td>
      <td>False</td>
      <td>{'id': 1093788270, 'id_str': '1093788270', 'na...</td>
    </tr>
    <tr>
      <th>74</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:47:48 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072261626747731968</td>
      <td>1072261626747731968</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13</td>
      <td>False</td>
      <td>{'created_at': 'Wed Dec 05 13:39:11 +0000 2018...</td>
      <td>&lt;a href="https://mobile.twitter.com" rel="nofo...</td>
      <td>RT @ikkoham: 量子Advent Calendarの記事書いた。\nQiskitの...</td>
      <td>False</td>
      <td>{'id': 320951147, 'id_str': '320951147', 'name...</td>
    </tr>
    <tr>
      <th>75</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:38:14 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072259222543446016</td>
      <td>1072259222543446016</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>@seanmylaw I see why this can be misleading, b...</td>
      <td>True</td>
      <td>{'id': 865622395, 'id_str': '865622395', 'name...</td>
    </tr>
    <tr>
      <th>76</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:36:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072258732048990208, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072258735475777542</td>
      <td>1072258735475777542</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://collabmarket.org/" rel="nofol...</td>
      <td>Riesgo Sistemico fecha 2018-12-10\n https://t....</td>
      <td>False</td>
      <td>{'id': 721868325576404992, 'id_str': '72186832...</td>
    </tr>
    <tr>
      <th>77</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:25:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072255909504532481</td>
      <td>1072255909504532481</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 01:16:39 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @LivingHistUON: It’s not easy to get nice, ...</td>
      <td>False</td>
      <td>{'id': 350683618, 'id_str': '350683618', 'name...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 22:24:30 +0000 2018</td>
      <td>{'hashtags': [{'text': 'fitting', 'indices': [...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072255766176911360</td>
      <td>1072255766176911360</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>Highlight of the day: generic function #fittin...</td>
      <td>False</td>
      <td>{'id': 944291984675614721, 'id_str': '94429198...</td>
    </tr>
    <tr>
      <th>79</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:57:39 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072249007085367296</td>
      <td>1072249007085367296</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 21:22:16 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @fmasanori: Análise de Dados sobre AIDS, fe...</td>
      <td>False</td>
      <td>{'id': 459268029, 'id_str': '459268029', 'name...</td>
    </tr>
    <tr>
      <th>80</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:55:04 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072248356859273217</td>
      <td>1072248356859273217</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.071697e+18</td>
      <td>1071696984942219264</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 07:16:41 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @betatim: If you'd like to try the nbtutor ...</td>
      <td>False</td>
      <td>{'id': 2174482088, 'id_str': '2174482088', 'na...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:42:11 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072245115555594252</td>
      <td>1072245115555594252</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.071697e+18</td>
      <td>1071696984942219264</td>
      <td>3</td>
      <td>False</td>
      <td>{'created_at': 'Mon Dec 10 07:16:41 +0000 2018...</td>
      <td>&lt;a href="https://about.twitter.com/products/tw...</td>
      <td>RT @betatim: If you'd like to try the nbtutor ...</td>
      <td>False</td>
      <td>{'id': 933014576647548928, 'id_str': '93301457...</td>
    </tr>
    <tr>
      <th>82</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:26:58 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>8</td>
      <td>False</td>
      <td>None</td>
      <td>1072241286390956033</td>
      <td>1072241286390956033</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Outro interessante: Análise de Óbitos Fetais e...</td>
      <td>False</td>
      <td>{'id': 14502701, 'id_str': '14502701', 'name':...</td>
    </tr>
    <tr>
      <th>83</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:22:16 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>29</td>
      <td>False</td>
      <td>None</td>
      <td>1072240104943558656</td>
      <td>1072240104943558656</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Análise de Dados sobre AIDS, feito por um alun...</td>
      <td>False</td>
      <td>{'id': 14502701, 'id_str': '14502701', 'name':...</td>
    </tr>
    <tr>
      <th>84</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:20:07 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072239563001729024</td>
      <td>1072239563001729024</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://xa.bi/tek_news" rel="nofollow"...</td>
      <td>HNews: Movie genre prediction with the Object2...</td>
      <td>False</td>
      <td>{'id': 63468443, 'id_str': '63468443', 'name':...</td>
    </tr>
    <tr>
      <th>85</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 21:12:06 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>None</td>
      <td>1072237546007027712</td>
      <td>1072237546007027712</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>少々前に話題になったZITが現時点で付与されている特許の要約からのトピック分析もざっくり行っ...</td>
      <td>False</td>
      <td>{'id': 314634742, 'id_str': '314634742', 'name...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 20:44:03 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072230485789630464, 'id_str...</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072230487450517504</td>
      <td>1072230487450517504</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://dlvrit.com/" rel="nofollow"&gt;d...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 900033256011255808, 'id_str': '90003325...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 19:02:12 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072204854804795392</td>
      <td>1072204854804795392</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 220472453, 'id_str': '220472453', 'name...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:59:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072204125415325696</td>
      <td>1072204125415325696</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>🛒🦍@amazon: "Amazon Object2Vec: Document Classi...</td>
      <td>True</td>
      <td>{'id': 4874962780, 'id_str': '4874962780', 'na...</td>
    </tr>
    <tr>
      <th>89</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:58:56 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072204030246576128</td>
      <td>1072204030246576128</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://github.com/d4nt/HNTweets" rel=...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 116276133, 'id_str': '116276133', 'name...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:58:13 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072203853314056193</td>
      <td>1072203853314056193</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://taragana.com" rel="nofollow"&gt;P...</td>
      <td>Amazon Object2Vec: Document Classification wit...</td>
      <td>False</td>
      <td>{'id': 6987882, 'id_str': '6987882', 'name': '...</td>
    </tr>
    <tr>
      <th>91</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:57:43 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>1</td>
      <td>False</td>
      <td>None</td>
      <td>1072203724347637760</td>
      <td>1072203724347637760</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 15042473, 'id_str': '15042473', 'name':...</td>
    </tr>
    <tr>
      <th>92</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:57:31 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072203675328700417</td>
      <td>1072203675328700417</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://ifttt.com" rel="nofollow"&gt;IFT...</td>
      <td>Movie genre prediction with the Object2Vec alg...</td>
      <td>False</td>
      <td>{'id': 3065181529, 'id_str': '3065181529', 'na...</td>
    </tr>
    <tr>
      <th>93</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:55:05 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>{'media': [{'id': 1072203060229824513, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072203061915873280</td>
      <td>1072203061915873280</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="https://dlvrit.com/" rel="nofollow"&gt;d...</td>
      <td>Amazon Object2Vec: Document Classification wit...</td>
      <td>False</td>
      <td>{'id': 1029603103123759105, 'id_str': '1029603...</td>
    </tr>
    <tr>
      <th>94</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 18:49:48 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072201734456524810</td>
      <td>1072201734456524810</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://hnfp.c17r.com" rel="nofollow"&gt;...</td>
      <td>Amazon Object2Vec: Document Classification wit...</td>
      <td>False</td>
      <td>{'id': 4617024083, 'id_str': '4617024083', 'na...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 17:58:18 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>2</td>
      <td>False</td>
      <td>None</td>
      <td>1072188772605927424</td>
      <td>1072188772605927424</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Playing with Binder &amp;amp; ipywidgets… TIL a 30...</td>
      <td>False</td>
      <td>{'id': 112876914, 'id_str': '112876914', 'name...</td>
    </tr>
    <tr>
      <th>96</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 17:12:00 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072177122121277440</td>
      <td>1072177122121277440</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5</td>
      <td>False</td>
      <td>{'created_at': 'Fri Dec 07 16:12:12 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>RT @alexandernitz: With all the recent gravita...</td>
      <td>False</td>
      <td>{'id': 310155354, 'id_str': '310155354', 'name...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 16:51:16 +0000 2018</td>
      <td>{'hashtags': [], 'symbols': [], 'user_mentions...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072171905338220544</td>
      <td>1072171905338220544</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>157</td>
      <td>False</td>
      <td>{'created_at': 'Mon Nov 26 09:55:04 +0000 2018...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @iamtrask: What if your email server kept y...</td>
      <td>False</td>
      <td>{'id': 929066238319394824, 'id_str': '92906623...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 16:32:27 +0000 2018</td>
      <td>{'hashtags': [{'text': 'TensorFlow', 'indices'...</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072167169419608065</td>
      <td>1072167169419608065</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://twitter.com" rel="nofollow"&gt;Tw...</td>
      <td>Btw, I've added an example how to setup Tensor...</td>
      <td>True</td>
      <td>{'id': 66489856, 'id_str': '66489856', 'name':...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>None</td>
      <td>None</td>
      <td>Mon Dec 10 16:25:01 +0000 2018</td>
      <td>{'hashtags': [{'text': 'python', 'indices': [1...</td>
      <td>{'media': [{'id': 1071426422747598848, 'id_str...</td>
      <td>0</td>
      <td>False</td>
      <td>None</td>
      <td>1072165296352432128</td>
      <td>1072165296352432128</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41</td>
      <td>False</td>
      <td>{'created_at': 'Sat Dec 08 15:29:35 +0000 2018...</td>
      <td>&lt;a href="https://buffer.com" rel="nofollow"&gt;Bu...</td>
      <td>RT @algoritmic: A Jupyter notebook on how to p...</td>
      <td>False</td>
      <td>{'id': 957799350163333122, 'id_str': '95779935...</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 30 columns</p>
</div>
</div>





{:.input_area}
```python
df = pd.DataFrame(python_tweets['statuses'])
df['text'].values
```





{:.output .output_data_text}
```
array(["RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!\n\nhttps://t.co/…",
       'stil transferi ile alakalı bir başka güzel demo: https://t.co/6cLW79Pgb7',
       "RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!\n\nhttps://t.co/…",
       "RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!\n\nhttps://t.co/…",
       'RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps://t.co/SZrUoo8IAY https://t.co/71NJebzsfV',
       'Hint of the day\nProbability Density Functions in Python: the early evaluation approach (numpy array) and the lazy e… https://t.co/ZxAwo0zsrG',
       'RT @mamono_jingu: https://t.co/fp8rtGXOie #bpstudy googleコラボラトリーってこれかな？ #bpstudy',
       'https://t.co/fp8rtGXOie #bpstudy googleコラボラトリーってこれかな？ #bpstudy',
       "RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!\n\nhttps://t.co/…",
       "What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!… https://t.co/s0IEklxwCZ",
       'RT @realpython: Unicorn! · GitHub https://t.co/uErXjAbpov MultiIndex Tutorial.ipynb',
       'yeah, I can tell that MHRD is really practical since I can understand Magma tutorial more than before! https://t.co/U7CQIHPHlV',
       "RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…",
       'RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps://t.co/SZrUoo8IAY https://t.co/71NJebzsfV',
       'RT @bhutanisanyam1: A demo of checkpointing your models to Google Drive when using Collab\n\nhttps://t.co/EiWk4lViEZ',
       'RT @algoritmic: A Jupyter notebook on how to process images into PETSCII art https://t.co/z35VJEDvzk #python https://t.co/HzJg2B21b4',
       'RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…',
       'RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps://t.co/SZrUoo8IAY https://t.co/71NJebzsfV',
       'RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…',
       'RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…',
       'RT @bhutanisanyam1: A demo of checkpointing your models to Google Drive when using Collab\n\nhttps://t.co/EiWk4lViEZ',
       '@hardmaru check out my notebook for Deep Pensieve, a souped up VAE. keras made it super easy to collect and hack to… https://t.co/rObgNxFm1j',
       'A demo of checkpointing your models to Google Drive when using Collab\n\nhttps://t.co/EiWk4lViEZ',
       '@realpython Link: https://t.co/7kPtBJ1S1j',
       "RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…",
       'Unicorn! · GitHub https://t.co/uErXjAbpov MultiIndex Tutorial.ipynb',
       'RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…',
       'RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sargent and Balint Szoke\n\nhttps://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir',
       'RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sargent and Balint Szoke\n\nhttps://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir',
       "RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…",
       'RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sargent and Balint Szoke\n\nhttps://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir',
       'Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata tha… https://t.co/aUBVEyGonp',
       'By Daniel Csaba, Thomas J. Sargent and Balint Szoke\n\nhttps://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir',
       'RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps://t.co/SZrUoo8IAY https://t.co/71NJebzsfV',
       'Riesgo Sistemico fecha 2018-12-11\n https://t.co/CHmr9IWoA6 https://t.co/tNzE9UBwwg',
       'RT @ZaminIqbal: Just found Google colab via @cwcyau cc @rmcolq, @Phelimb, @mbhall88, @KerrMalone, @GraceBlackwel1, @martibartfast. I like t…',
       'ワイもCNNでくずし字の分類やってみた（＾ω＾）\nhttps://t.co/SZrUoo8IAY https://t.co/71NJebzsfV',
       "@jeremyjordan @PyTorch @GoogleColab @ProjectJupyter I too haven't had any luck outside of MNIST. So I might actuall… https://t.co/bS7AfbfCyl",
       "RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…",
       'Handwritten Digit Classification using Deep Feed Forward Neural Network Using #ApacheSpark and #Intel BigDL develop… https://t.co/pjUrO9QDSO',
       '@gaurav_bio @jakevdp Sorry wong link that is cloud datalab the colab pip is here https://t.co/qF0vXqzlr4',
       "RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…",
       "@honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and w… https://t.co/SuOgD2EwDr",
       "@Asmodaie @GabrielG439 The link to github is effectively me blogging about it. But I'm not sure the link works for… https://t.co/Wov9QwUQtA",
       '@dataquestio Step 2 complete! Humble Superman has new Superpowers : #Dataviz &amp; Data Cleaning.. Oh yeah!!… https://t.co/LdCM3U1qtC',
       'RT @neurokinetikz: Deep Pensieve™️, now with Residual Attention Modules (RAM), an idea that was published 11 days ago by some graduate stud…',
       'こういうのがあるのか\nhttps://t.co/JBTbtykZ4n',
       'RT @sigfpe: Three answers to a question I was asked about the probability of winning. I was pleased to find they were all equal! https://t.…',
       'RT @sigfpe: Three answers to a question I was asked about the probability of winning. I was pleased to find they were all equal! https://t.…',
       'RT @DeepMindAI: The BigGAN generators from our paper https://t.co/QUYlE9IBsE are now available on TF Hub (https://t.co/GHM9pIgQPw). Try the…',
       'Three answers to a question I was asked about the probability of winning. I was pleased to find they were all equal… https://t.co/0xZ2ddLpma',
       'https://t.co/iHlaIWTPso',
       '@NotFakeBrendan @Bellspringsteen check it out! https://t.co/4vrxZdBKU0',
       '@RegexTip @A_K_Nain This might help https://t.co/KXeq6rHDu0',
       'https://t.co/18aHDRQxp6 :D',
       "For whatever reason, @github isn't rendering ipynb files on @googlechrome for me today. Probably have a weird cooki… https://t.co/d5m3H4GSkN",
       'RT @psi_bar: Discover Sentiments in Tweets \n\nhttps://t.co/iDgsXHYKyE\n\n#Twitter #trends #python #Azure',
       'Discover Sentiments in Tweets \n\nhttps://t.co/iDgsXHYKyE\n\n#Twitter #trends #python #Azure',
       'Amazon Object2Vec: Document Classification with Deep Representation Learning by  via Hacker News… https://t.co/5vb0dOfqVp',
       'RT @zaidalyafeai: Eager Execution Enabled\nIn this notebook I explain different concepts in eager execution. I go over variables, ops, gradi…',
       'RT @lucasoft_co_uk: RT @algoritmic A Jupyter notebook on how to process images into PETSCII art https://t.co/dzmVbmRiFk #python #bigdata #e…',
       'RT @itiskj: "elegant scipy" 読み始めたけど、Python runtime もあるし .md も書けるし Mathjax も対応しているし。Jupyter Notebook でそのまま書籍にしてオンライン公開してしまうの、一番読者にとって嬉しい形かもし…',
       '@GoogleColab https://t.co/HVPs807Yc1 am I the only one not able to use %load magic in colab?… https://t.co/LGsv6wRXK7',
       'Deep Pensieve™️, now with Residual Attention Modules (RAM), an idea that was published 11 days ago by some graduate… https://t.co/JdMgiZj41W',
       'RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N',
       'RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N',
       'RT @ZaminIqbal: Just found Google colab via @cwcyau cc @rmcolq, @Phelimb, @mbhall88, @KerrMalone, @GraceBlackwel1, @martibartfast. I like t…',
       'RT @fmasanori: Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC\nhttps://t.co/YXd8n7FcwD',
       'é impressao minha ou os nteract nbs n ficam salvos  a n ser q vc mande? com isso finalmente  esta resolvida a velha… https://t.co/VPjVA27o57',
       "If you enjoyed @JohnLaTwC's detailed post couple of days back about automating investigations using Jupyter Noteboo… https://t.co/75NlXTa8wN",
       'Just found Google colab via @cwcyau cc @rmcolq, @Phelimb, @mbhall88, @KerrMalone, @GraceBlackwel1, @martibartfast.… https://t.co/RzW5adV3Gc',
       'RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N',
       'もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N',
       'RT @fmasanori: Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC\nhttps://t.co/YXd8n7FcwD',
       'RT @ikkoham: 量子Advent Calendarの記事書いた。\nQiskitのチュートリアルのチュートリアルです。ホンマに入門記事なので気楽に読んでください。\n\n今日から量子生活始めませんか？\n\nhttps://t.co/ScNIkmD7d1',
       '@seanmylaw I see why this can be misleading, but it was actually 500 out of 5000, not 5000 out of 50k (was too impa… https://t.co/i9HpubmyDP',
       'Riesgo Sistemico fecha 2018-12-10\n https://t.co/CHmr9IWoA6 https://t.co/Euk5iAzzHS',
       'RT @LivingHistUON: It’s not easy to get nice, big full-page images from TroveAustralia’s digitised newspapers, so I made a little app to he…',
       'Highlight of the day: generic function #fitting with a very nice and functional #GUI in #Python using #hyperspy: https://t.co/siH8QOEvJL',
       'RT @fmasanori: Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC\nhttps://t.co/YXd8n7FcwD',
       "RT @betatim: If you'd like to try the nbtutor live without installing anything @mybinderteam https://t.co/r6vQRJRGOr has you covered. All y…",
       "RT @betatim: If you'd like to try the nbtutor live without installing anything @mybinderteam https://t.co/r6vQRJRGOr has you covered. All y…",
       'Outro interessante: Análise de Óbitos Fetais em São Paulo https://t.co/IaA7sDtYyO',
       'Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC\nhttps://t.co/YXd8n7FcwD',
       'HNews: Movie genre prediction with the Object2Vec algorithm https://t.co/90CvNIctl4',
       '少々前に話題になったZITが現時点で付与されている特許の要約からのトピック分析もざっくり行ってみた。「前記」とかが多数出ているなどストップワード指定が甘いが。やはり結果の解釈は難度が高い。https://t.co/TaiLekBYHo',
       'Movie genre prediction with the Object2Vec algorithm https://t.co/qmMHY1zfeK https://t.co/TvE7qAayBl',
       'Movie genre prediction with the Object2Vec algorithm https://t.co/COUIliP0m4',
       '🛒🦍@amazon: "Amazon Object2Vec: Document Classification with Deep Representation Learning" https://t.co/C2upY7wFA3… https://t.co/Xiigx4bBI0',
       'Movie genre prediction with the Object2Vec algorithm: https://t.co/eW2opKJLsu Comments: https://t.co/XWUAcWqYub',
       'Amazon Object2Vec: Document Classification with Deep Representation Learning https://t.co/4bx1gm0xMo',
       'Movie genre prediction with the Object2Vec algorithm https://t.co/Zi4G324NhR (cmts https://t.co/3QIBaaJgoz)',
       'Movie genre prediction with the Object2Vec algorithm https://t.co/3gib9jSS0T',
       'Amazon Object2Vec: Document Classification with Deep Representation Learning https://t.co/y3g0S7Whs5 https://t.co/1sxNcatKHU',
       'Amazon Object2Vec: Document Classification with Deep Representation Learning\nL: https://t.co/H8Xtmn6UAw\nC: https://t.co/4hADMxkvok',
       'Playing with Binder &amp; ipywidgets… TIL a 30 point drift in @quarq zero offset is worth ~10W @ 100RPM.\nhttps://t.co/JMIEEcBfaK',
       'RT @alexandernitz: With all the recent gravitational waves found by @LIGO and @ego_virgo, why not try analyzing GW data yourself! Try this…',
       'RT @iamtrask: What if your email server kept your emails *encrypted* - even when running ML spellcheck on each email?\n\nIt is possible to ru…',
       "Btw, I've added an example how to setup TensorFlow serving in Colab - https://t.co/qQh3zi64zk #TensorFlow #ranking… https://t.co/4mmq5ubQLE",
       'RT @algoritmic: A Jupyter notebook on how to process images into PETSCII art https://t.co/z35VJEDvzk #python https://t.co/HzJg2B21b4'],
      dtype=object)
```





{:.input_area}
```python
for status_update in df['text'].values:
    print(status_update)
```


{:.output .output_stream}
```
RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!

https://t.co/…
stil transferi ile alakalı bir başka güzel demo: https://t.co/6cLW79Pgb7
RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!

https://t.co/…
RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!

https://t.co/…
RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）
https://t.co/SZrUoo8IAY https://t.co/71NJebzsfV
Hint of the day
Probability Density Functions in Python: the early evaluation approach (numpy array) and the lazy e… https://t.co/ZxAwo0zsrG
RT @mamono_jingu: https://t.co/fp8rtGXOie #bpstudy googleコラボラトリーってこれかな？ #bpstudy
https://t.co/fp8rtGXOie #bpstudy googleコラボラトリーってこれかな？ #bpstudy
RT @poliastro_py: What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!

https://t.co/…
What's the orbital future of MarCO-A &amp; B? Check out this notebook shared in the @LibreSpace_Fnd community!… https://t.co/s0IEklxwCZ
RT @realpython: Unicorn! · GitHub https://t.co/uErXjAbpov MultiIndex Tutorial.ipynb
yeah, I can tell that MHRD is really practical since I can understand Magma tutorial more than before! https://t.co/U7CQIHPHlV
RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…
RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）
https://t.co/SZrUoo8IAY https://t.co/71NJebzsfV
RT @bhutanisanyam1: A demo of checkpointing your models to Google Drive when using Collab

https://t.co/EiWk4lViEZ
RT @algoritmic: A Jupyter notebook on how to process images into PETSCII art https://t.co/z35VJEDvzk #python https://t.co/HzJg2B21b4
RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…
RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）
https://t.co/SZrUoo8IAY https://t.co/71NJebzsfV
RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…
RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…
RT @bhutanisanyam1: A demo of checkpointing your models to Google Drive when using Collab

https://t.co/EiWk4lViEZ
@hardmaru check out my notebook for Deep Pensieve, a souped up VAE. keras made it super easy to collect and hack to… https://t.co/rObgNxFm1j
A demo of checkpointing your models to Google Drive when using Collab

https://t.co/EiWk4lViEZ
@realpython Link: https://t.co/7kPtBJ1S1j
RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…
Unicorn! · GitHub https://t.co/uErXjAbpov MultiIndex Tutorial.ipynb
RT @johnkitchin: Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata that can b…
RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sargent and Balint Szoke

https://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir
RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sargent and Balint Szoke

https://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir
RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…
RT @M_De_Nardi: By Daniel Csaba, Thomas J. Sargent and Balint Szoke

https://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir
Need a new way to make a slideshow from #orgmode? https://t.co/2XdfQcJ1az now lets you put custom cell metadata tha… https://t.co/aUBVEyGonp
By Daniel Csaba, Thomas J. Sargent and Balint Szoke

https://t.co/hX0bGYpAfc https://t.co/fOc1P5G1ir
RT @tdualdir: ワイもCNNでくずし字の分類やってみた（＾ω＾）
https://t.co/SZrUoo8IAY https://t.co/71NJebzsfV
Riesgo Sistemico fecha 2018-12-11
 https://t.co/CHmr9IWoA6 https://t.co/tNzE9UBwwg
RT @ZaminIqbal: Just found Google colab via @cwcyau cc @rmcolq, @Phelimb, @mbhall88, @KerrMalone, @GraceBlackwel1, @martibartfast. I like t…
ワイもCNNでくずし字の分類やってみた（＾ω＾）
https://t.co/SZrUoo8IAY https://t.co/71NJebzsfV
@jeremyjordan @PyTorch @GoogleColab @ProjectJupyter I too haven't had any luck outside of MNIST. So I might actuall… https://t.co/bS7AfbfCyl
RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…
Handwritten Digit Classification using Deep Feed Forward Neural Network Using #ApacheSpark and #Intel BigDL develop… https://t.co/pjUrO9QDSO
@gaurav_bio @jakevdp Sorry wong link that is cloud datalab the colab pip is here https://t.co/qF0vXqzlr4
RT @jeremyphoward: @honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and why th…
@honnibal I absolutely do. And the next course will take students thru those notebooks so they'll exactly how and w… https://t.co/SuOgD2EwDr
@Asmodaie @GabrielG439 The link to github is effectively me blogging about it. But I'm not sure the link works for… https://t.co/Wov9QwUQtA
@dataquestio Step 2 complete! Humble Superman has new Superpowers : #Dataviz &amp; Data Cleaning.. Oh yeah!!… https://t.co/LdCM3U1qtC
RT @neurokinetikz: Deep Pensieve™️, now with Residual Attention Modules (RAM), an idea that was published 11 days ago by some graduate stud…
こういうのがあるのか
https://t.co/JBTbtykZ4n
RT @sigfpe: Three answers to a question I was asked about the probability of winning. I was pleased to find they were all equal! https://t.…
RT @sigfpe: Three answers to a question I was asked about the probability of winning. I was pleased to find they were all equal! https://t.…
RT @DeepMindAI: The BigGAN generators from our paper https://t.co/QUYlE9IBsE are now available on TF Hub (https://t.co/GHM9pIgQPw). Try the…
Three answers to a question I was asked about the probability of winning. I was pleased to find they were all equal… https://t.co/0xZ2ddLpma
https://t.co/iHlaIWTPso
@NotFakeBrendan @Bellspringsteen check it out! https://t.co/4vrxZdBKU0
@RegexTip @A_K_Nain This might help https://t.co/KXeq6rHDu0
https://t.co/18aHDRQxp6 :D
For whatever reason, @github isn't rendering ipynb files on @googlechrome for me today. Probably have a weird cooki… https://t.co/d5m3H4GSkN
RT @psi_bar: Discover Sentiments in Tweets 

https://t.co/iDgsXHYKyE

#Twitter #trends #python #Azure
Discover Sentiments in Tweets 

https://t.co/iDgsXHYKyE

#Twitter #trends #python #Azure
Amazon Object2Vec: Document Classification with Deep Representation Learning by  via Hacker News… https://t.co/5vb0dOfqVp
RT @zaidalyafeai: Eager Execution Enabled
In this notebook I explain different concepts in eager execution. I go over variables, ops, gradi…
RT @lucasoft_co_uk: RT @algoritmic A Jupyter notebook on how to process images into PETSCII art https://t.co/dzmVbmRiFk #python #bigdata #e…
RT @itiskj: "elegant scipy" 読み始めたけど、Python runtime もあるし .md も書けるし Mathjax も対応しているし。Jupyter Notebook でそのまま書籍にしてオンライン公開してしまうの、一番読者にとって嬉しい形かもし…
@GoogleColab https://t.co/HVPs807Yc1 am I the only one not able to use %load magic in colab?… https://t.co/LGsv6wRXK7
Deep Pensieve™️, now with Residual Attention Modules (RAM), an idea that was published 11 days ago by some graduate… https://t.co/JdMgiZj41W
RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N
RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N
RT @ZaminIqbal: Just found Google colab via @cwcyau cc @rmcolq, @Phelimb, @mbhall88, @KerrMalone, @GraceBlackwel1, @martibartfast. I like t…
RT @fmasanori: Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC
https://t.co/YXd8n7FcwD
é impressao minha ou os nteract nbs n ficam salvos  a n ser q vc mande? com isso finalmente  esta resolvida a velha… https://t.co/VPjVA27o57
If you enjoyed @JohnLaTwC's detailed post couple of days back about automating investigations using Jupyter Noteboo… https://t.co/75NlXTa8wN
Just found Google colab via @cwcyau cc @rmcolq, @Phelimb, @mbhall88, @KerrMalone, @GraceBlackwel1, @martibartfast.… https://t.co/RzW5adV3Gc
RT @yutakashino: もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N
もうイキナリcolabで実行できたりして，最高かよ感しかないですね… https://t.co/XMGf2pWl6N
RT @fmasanori: Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC
https://t.co/YXd8n7FcwD
RT @ikkoham: 量子Advent Calendarの記事書いた。
Qiskitのチュートリアルのチュートリアルです。ホンマに入門記事なので気楽に読んでください。

今日から量子生活始めませんか？

https://t.co/ScNIkmD7d1
@seanmylaw I see why this can be misleading, but it was actually 500 out of 5000, not 5000 out of 50k (was too impa… https://t.co/i9HpubmyDP
Riesgo Sistemico fecha 2018-12-10
 https://t.co/CHmr9IWoA6 https://t.co/Euk5iAzzHS
RT @LivingHistUON: It’s not easy to get nice, big full-page images from TroveAustralia’s digitised newspapers, so I made a little app to he…
Highlight of the day: generic function #fitting with a very nice and functional #GUI in #Python using #hyperspy: https://t.co/siH8QOEvJL
RT @fmasanori: Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC
https://t.co/YXd8n7FcwD
RT @betatim: If you'd like to try the nbtutor live without installing anything @mybinderteam https://t.co/r6vQRJRGOr has you covered. All y…
RT @betatim: If you'd like to try the nbtutor live without installing anything @mybinderteam https://t.co/r6vQRJRGOr has you covered. All y…
Outro interessante: Análise de Óbitos Fetais em São Paulo https://t.co/IaA7sDtYyO
Análise de Dados sobre AIDS, feito por um aluno do 1o semestre da FATEC
https://t.co/YXd8n7FcwD
HNews: Movie genre prediction with the Object2Vec algorithm https://t.co/90CvNIctl4
少々前に話題になったZITが現時点で付与されている特許の要約からのトピック分析もざっくり行ってみた。「前記」とかが多数出ているなどストップワード指定が甘いが。やはり結果の解釈は難度が高い。https://t.co/TaiLekBYHo
Movie genre prediction with the Object2Vec algorithm https://t.co/qmMHY1zfeK https://t.co/TvE7qAayBl
Movie genre prediction with the Object2Vec algorithm https://t.co/COUIliP0m4
🛒🦍@amazon: "Amazon Object2Vec: Document Classification with Deep Representation Learning" https://t.co/C2upY7wFA3… https://t.co/Xiigx4bBI0
Movie genre prediction with the Object2Vec algorithm: https://t.co/eW2opKJLsu Comments: https://t.co/XWUAcWqYub
Amazon Object2Vec: Document Classification with Deep Representation Learning https://t.co/4bx1gm0xMo
Movie genre prediction with the Object2Vec algorithm https://t.co/Zi4G324NhR (cmts https://t.co/3QIBaaJgoz)
Movie genre prediction with the Object2Vec algorithm https://t.co/3gib9jSS0T
Amazon Object2Vec: Document Classification with Deep Representation Learning https://t.co/y3g0S7Whs5 https://t.co/1sxNcatKHU
Amazon Object2Vec: Document Classification with Deep Representation Learning
L: https://t.co/H8Xtmn6UAw
C: https://t.co/4hADMxkvok
Playing with Binder &amp; ipywidgets… TIL a 30 point drift in @quarq zero offset is worth ~10W @ 100RPM.
https://t.co/JMIEEcBfaK
RT @alexandernitz: With all the recent gravitational waves found by @LIGO and @ego_virgo, why not try analyzing GW data yourself! Try this…
RT @iamtrask: What if your email server kept your emails *encrypted* - even when running ML spellcheck on each email?

It is possible to ru…
Btw, I've added an example how to setup TensorFlow serving in Colab - https://t.co/qQh3zi64zk #TensorFlow #ranking… https://t.co/4mmq5ubQLE
RT @algoritmic: A Jupyter notebook on how to process images into PETSCII art https://t.co/z35VJEDvzk #python https://t.co/HzJg2B21b4

```



<div class="alert alert-info">
<h3> Your turn</h3>
<p> Do a search!
</div>




Get 3,200 tweets from somone



{:.input_area}
```python
pd.DataFrame(user_timeline).iloc[-1]['id']
```




{:.input_area}
```python
user_timeline = twitter.get_user_timeline(screen_name='oprah', 
                                         count = 200,
                                         max_id = 929540230465458177)
```




{:.input_area}
```python
def get_timeline(screen_name):
    tweets = []
    user_timeline = twitter.get_user_timeline(screen_name=screen_name,
                                              count = 200)
    df = pd.DataFrame(user_timeline)
    tweets.append(df)
    
    most_recent = pd.DataFrame(user_timeline).iloc[-1]['id']
    
    for i in range(0,15):
        user_timeline = twitter.get_user_timeline(screen_name=screen_name,
                                              count = 200,
                                                 max_id = most_recent)
        df = pd.DataFrame(user_timeline)
        tweets.append(df)
        most_recent = pd.DataFrame(user_timeline).iloc[-1]['id']
    
    tweet_df = pd.concat(tweets, ignore_index = True)
    return tweets    
    
```


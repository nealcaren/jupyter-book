---
interact_link: content/collecting/scraping.ipynb
title: 'Web Scraping'
prev_page:
  url: /collecting/apis
  title: 'Web APIs'
next_page:
  url: /placeholder
  title: 'Counting words'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---

## Scraping websites



{:.input_area}
```python
%matplotlib inline

import pandas as pd
```


Will pandas solve my problems?

Some table on web pages can also be read in with `read_html`. This works for tables that are in the document's HTML, rather than displayed using JavaScript or some other technique. You can confirm by inspecting the HTML code, but trial and error is better.

The Titanic data might have been displayed on a website like this: 

![](images/html_table.png)


![](images/moby.png)

Unlike the other `read` methods which return a dataframe, `read_html` returns a list dataframes. This is useful when a page contains more than one table. In this case there is only one table, so it will return a list of one item.



{:.input_area}
```python
html_table_list = pd.read_html('data/titanic.html')
```




{:.input_area}
```python
len(html_table_list)
```





{:.output .output_data_text}
```
1
```





{:.input_area}
```python
html_table_list[0]
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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>0</td>
      <td>3</td>
      <td>Moran, Mr. James</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>330877</td>
      <td>8.4583</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>0</td>
      <td>1</td>
      <td>McCarthy, Mr. Timothy J</td>
      <td>male</td>
      <td>54.0</td>
      <td>0</td>
      <td>0</td>
      <td>17463</td>
      <td>51.8625</td>
      <td>E46</td>
      <td>S</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>0</td>
      <td>3</td>
      <td>Palsson, Master. Gosta Leonard</td>
      <td>male</td>
      <td>2.0</td>
      <td>3</td>
      <td>1</td>
      <td>349909</td>
      <td>21.0750</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>1</td>
      <td>3</td>
      <td>Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)</td>
      <td>female</td>
      <td>27.0</td>
      <td>0</td>
      <td>2</td>
      <td>347742</td>
      <td>11.1333</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>1</td>
      <td>2</td>
      <td>Nasser, Mrs. Nicholas (Adele Achem)</td>
      <td>female</td>
      <td>14.0</td>
      <td>1</td>
      <td>0</td>
      <td>237736</td>
      <td>30.0708</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11</td>
      <td>1</td>
      <td>3</td>
      <td>Sandstrom, Miss. Marguerite Rut</td>
      <td>female</td>
      <td>4.0</td>
      <td>1</td>
      <td>1</td>
      <td>PP 9549</td>
      <td>16.7000</td>
      <td>G6</td>
      <td>S</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>1</td>
      <td>1</td>
      <td>Bonnell, Miss. Elizabeth</td>
      <td>female</td>
      <td>58.0</td>
      <td>0</td>
      <td>0</td>
      <td>113783</td>
      <td>26.5500</td>
      <td>C103</td>
      <td>S</td>
    </tr>
    <tr>
      <th>12</th>
      <td>13</td>
      <td>0</td>
      <td>3</td>
      <td>Saundercock, Mr. William Henry</td>
      <td>male</td>
      <td>20.0</td>
      <td>0</td>
      <td>0</td>
      <td>A/5. 2151</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>13</th>
      <td>14</td>
      <td>0</td>
      <td>3</td>
      <td>Andersson, Mr. Anders Johan</td>
      <td>male</td>
      <td>39.0</td>
      <td>1</td>
      <td>5</td>
      <td>347082</td>
      <td>31.2750</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>14</th>
      <td>15</td>
      <td>0</td>
      <td>3</td>
      <td>Vestrom, Miss. Hulda Amanda Adolfina</td>
      <td>female</td>
      <td>14.0</td>
      <td>0</td>
      <td>0</td>
      <td>350406</td>
      <td>7.8542</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>15</th>
      <td>16</td>
      <td>1</td>
      <td>2</td>
      <td>Hewlett, Mrs. (Mary D Kingcome)</td>
      <td>female</td>
      <td>55.0</td>
      <td>0</td>
      <td>0</td>
      <td>248706</td>
      <td>16.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>16</th>
      <td>17</td>
      <td>0</td>
      <td>3</td>
      <td>Rice, Master. Eugene</td>
      <td>male</td>
      <td>2.0</td>
      <td>4</td>
      <td>1</td>
      <td>382652</td>
      <td>29.1250</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
    <tr>
      <th>17</th>
      <td>18</td>
      <td>1</td>
      <td>2</td>
      <td>Williams, Mr. Charles Eugene</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>244373</td>
      <td>13.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>18</th>
      <td>19</td>
      <td>0</td>
      <td>3</td>
      <td>Vander Planke, Mrs. Julius (Emelia Maria Vande...</td>
      <td>female</td>
      <td>31.0</td>
      <td>1</td>
      <td>0</td>
      <td>345763</td>
      <td>18.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>19</th>
      <td>20</td>
      <td>1</td>
      <td>3</td>
      <td>Masselmani, Mrs. Fatima</td>
      <td>female</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>2649</td>
      <td>7.2250</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
    <tr>
      <th>20</th>
      <td>21</td>
      <td>0</td>
      <td>2</td>
      <td>Fynney, Mr. Joseph J</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>239865</td>
      <td>26.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>21</th>
      <td>22</td>
      <td>1</td>
      <td>2</td>
      <td>Beesley, Mr. Lawrence</td>
      <td>male</td>
      <td>34.0</td>
      <td>0</td>
      <td>0</td>
      <td>248698</td>
      <td>13.0000</td>
      <td>D56</td>
      <td>S</td>
    </tr>
    <tr>
      <th>22</th>
      <td>23</td>
      <td>1</td>
      <td>3</td>
      <td>McGowan, Miss. Anna "Annie"</td>
      <td>female</td>
      <td>15.0</td>
      <td>0</td>
      <td>0</td>
      <td>330923</td>
      <td>8.0292</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
    <tr>
      <th>23</th>
      <td>24</td>
      <td>1</td>
      <td>1</td>
      <td>Sloper, Mr. William Thompson</td>
      <td>male</td>
      <td>28.0</td>
      <td>0</td>
      <td>0</td>
      <td>113788</td>
      <td>35.5000</td>
      <td>A6</td>
      <td>S</td>
    </tr>
    <tr>
      <th>24</th>
      <td>25</td>
      <td>0</td>
      <td>3</td>
      <td>Palsson, Miss. Torborg Danira</td>
      <td>female</td>
      <td>8.0</td>
      <td>3</td>
      <td>1</td>
      <td>349909</td>
      <td>21.0750</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>25</th>
      <td>26</td>
      <td>1</td>
      <td>3</td>
      <td>Asplund, Mrs. Carl Oscar (Selma Augusta Emilia...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>5</td>
      <td>347077</td>
      <td>31.3875</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>26</th>
      <td>27</td>
      <td>0</td>
      <td>3</td>
      <td>Emir, Mr. Farred Chehab</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>2631</td>
      <td>7.2250</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
    <tr>
      <th>27</th>
      <td>28</td>
      <td>0</td>
      <td>1</td>
      <td>Fortune, Mr. Charles Alexander</td>
      <td>male</td>
      <td>19.0</td>
      <td>3</td>
      <td>2</td>
      <td>19950</td>
      <td>263.0000</td>
      <td>C23 C25 C27</td>
      <td>S</td>
    </tr>
    <tr>
      <th>28</th>
      <td>29</td>
      <td>1</td>
      <td>3</td>
      <td>O'Dwyer, Miss. Ellen "Nellie"</td>
      <td>female</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>330959</td>
      <td>7.8792</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
    <tr>
      <th>29</th>
      <td>30</td>
      <td>0</td>
      <td>3</td>
      <td>Todoroff, Mr. Lalio</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>349216</td>
      <td>7.8958</td>
      <td>NaN</td>
      <td>S</td>
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
    </tr>
    <tr>
      <th>861</th>
      <td>862</td>
      <td>0</td>
      <td>2</td>
      <td>Giles, Mr. Frederick Edward</td>
      <td>male</td>
      <td>21.0</td>
      <td>1</td>
      <td>0</td>
      <td>28134</td>
      <td>11.5000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>862</th>
      <td>863</td>
      <td>1</td>
      <td>1</td>
      <td>Swift, Mrs. Frederick Joel (Margaret Welles Ba...</td>
      <td>female</td>
      <td>48.0</td>
      <td>0</td>
      <td>0</td>
      <td>17466</td>
      <td>25.9292</td>
      <td>D17</td>
      <td>S</td>
    </tr>
    <tr>
      <th>863</th>
      <td>864</td>
      <td>0</td>
      <td>3</td>
      <td>Sage, Miss. Dorothy Edith "Dolly"</td>
      <td>female</td>
      <td>NaN</td>
      <td>8</td>
      <td>2</td>
      <td>CA. 2343</td>
      <td>69.5500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>864</th>
      <td>865</td>
      <td>0</td>
      <td>2</td>
      <td>Gill, Mr. John William</td>
      <td>male</td>
      <td>24.0</td>
      <td>0</td>
      <td>0</td>
      <td>233866</td>
      <td>13.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>865</th>
      <td>866</td>
      <td>1</td>
      <td>2</td>
      <td>Bystrom, Mrs. (Karolina)</td>
      <td>female</td>
      <td>42.0</td>
      <td>0</td>
      <td>0</td>
      <td>236852</td>
      <td>13.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>866</th>
      <td>867</td>
      <td>1</td>
      <td>2</td>
      <td>Duran y More, Miss. Asuncion</td>
      <td>female</td>
      <td>27.0</td>
      <td>1</td>
      <td>0</td>
      <td>SC/PARIS 2149</td>
      <td>13.8583</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
    <tr>
      <th>867</th>
      <td>868</td>
      <td>0</td>
      <td>1</td>
      <td>Roebling, Mr. Washington Augustus II</td>
      <td>male</td>
      <td>31.0</td>
      <td>0</td>
      <td>0</td>
      <td>PC 17590</td>
      <td>50.4958</td>
      <td>A24</td>
      <td>S</td>
    </tr>
    <tr>
      <th>868</th>
      <td>869</td>
      <td>0</td>
      <td>3</td>
      <td>van Melkebeke, Mr. Philemon</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>345777</td>
      <td>9.5000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>869</th>
      <td>870</td>
      <td>1</td>
      <td>3</td>
      <td>Johnson, Master. Harold Theodor</td>
      <td>male</td>
      <td>4.0</td>
      <td>1</td>
      <td>1</td>
      <td>347742</td>
      <td>11.1333</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>870</th>
      <td>871</td>
      <td>0</td>
      <td>3</td>
      <td>Balkic, Mr. Cerin</td>
      <td>male</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>349248</td>
      <td>7.8958</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>871</th>
      <td>872</td>
      <td>1</td>
      <td>1</td>
      <td>Beckwith, Mrs. Richard Leonard (Sallie Monypeny)</td>
      <td>female</td>
      <td>47.0</td>
      <td>1</td>
      <td>1</td>
      <td>11751</td>
      <td>52.5542</td>
      <td>D35</td>
      <td>S</td>
    </tr>
    <tr>
      <th>872</th>
      <td>873</td>
      <td>0</td>
      <td>1</td>
      <td>Carlsson, Mr. Frans Olof</td>
      <td>male</td>
      <td>33.0</td>
      <td>0</td>
      <td>0</td>
      <td>695</td>
      <td>5.0000</td>
      <td>B51 B53 B55</td>
      <td>S</td>
    </tr>
    <tr>
      <th>873</th>
      <td>874</td>
      <td>0</td>
      <td>3</td>
      <td>Vander Cruyssen, Mr. Victor</td>
      <td>male</td>
      <td>47.0</td>
      <td>0</td>
      <td>0</td>
      <td>345765</td>
      <td>9.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>874</th>
      <td>875</td>
      <td>1</td>
      <td>2</td>
      <td>Abelson, Mrs. Samuel (Hannah Wizosky)</td>
      <td>female</td>
      <td>28.0</td>
      <td>1</td>
      <td>0</td>
      <td>P/PP 3381</td>
      <td>24.0000</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
    <tr>
      <th>875</th>
      <td>876</td>
      <td>1</td>
      <td>3</td>
      <td>Najib, Miss. Adele Kiamie "Jane"</td>
      <td>female</td>
      <td>15.0</td>
      <td>0</td>
      <td>0</td>
      <td>2667</td>
      <td>7.2250</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
    <tr>
      <th>876</th>
      <td>877</td>
      <td>0</td>
      <td>3</td>
      <td>Gustafsson, Mr. Alfred Ossian</td>
      <td>male</td>
      <td>20.0</td>
      <td>0</td>
      <td>0</td>
      <td>7534</td>
      <td>9.8458</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>877</th>
      <td>878</td>
      <td>0</td>
      <td>3</td>
      <td>Petroff, Mr. Nedelio</td>
      <td>male</td>
      <td>19.0</td>
      <td>0</td>
      <td>0</td>
      <td>349212</td>
      <td>7.8958</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>878</th>
      <td>879</td>
      <td>0</td>
      <td>3</td>
      <td>Laleff, Mr. Kristo</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>349217</td>
      <td>7.8958</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>879</th>
      <td>880</td>
      <td>1</td>
      <td>1</td>
      <td>Potter, Mrs. Thomas Jr (Lily Alexenia Wilson)</td>
      <td>female</td>
      <td>56.0</td>
      <td>0</td>
      <td>1</td>
      <td>11767</td>
      <td>83.1583</td>
      <td>C50</td>
      <td>C</td>
    </tr>
    <tr>
      <th>880</th>
      <td>881</td>
      <td>1</td>
      <td>2</td>
      <td>Shelley, Mrs. William (Imanita Parrish Hall)</td>
      <td>female</td>
      <td>25.0</td>
      <td>0</td>
      <td>1</td>
      <td>230433</td>
      <td>26.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>881</th>
      <td>882</td>
      <td>0</td>
      <td>3</td>
      <td>Markun, Mr. Johann</td>
      <td>male</td>
      <td>33.0</td>
      <td>0</td>
      <td>0</td>
      <td>349257</td>
      <td>7.8958</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>882</th>
      <td>883</td>
      <td>0</td>
      <td>3</td>
      <td>Dahlberg, Miss. Gerda Ulrika</td>
      <td>female</td>
      <td>22.0</td>
      <td>0</td>
      <td>0</td>
      <td>7552</td>
      <td>10.5167</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>883</th>
      <td>884</td>
      <td>0</td>
      <td>2</td>
      <td>Banfield, Mr. Frederick James</td>
      <td>male</td>
      <td>28.0</td>
      <td>0</td>
      <td>0</td>
      <td>C.A./SOTON 34068</td>
      <td>10.5000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>884</th>
      <td>885</td>
      <td>0</td>
      <td>3</td>
      <td>Sutehall, Mr. Henry Jr</td>
      <td>male</td>
      <td>25.0</td>
      <td>0</td>
      <td>0</td>
      <td>SOTON/OQ 392076</td>
      <td>7.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>885</th>
      <td>886</td>
      <td>0</td>
      <td>3</td>
      <td>Rice, Mrs. William (Margaret Norton)</td>
      <td>female</td>
      <td>39.0</td>
      <td>0</td>
      <td>5</td>
      <td>382652</td>
      <td>29.1250</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
    <tr>
      <th>886</th>
      <td>887</td>
      <td>0</td>
      <td>2</td>
      <td>Montvila, Rev. Juozas</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>211536</td>
      <td>13.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>887</th>
      <td>888</td>
      <td>1</td>
      <td>1</td>
      <td>Graham, Miss. Margaret Edith</td>
      <td>female</td>
      <td>19.0</td>
      <td>0</td>
      <td>0</td>
      <td>112053</td>
      <td>30.0000</td>
      <td>B42</td>
      <td>S</td>
    </tr>
    <tr>
      <th>888</th>
      <td>889</td>
      <td>0</td>
      <td>3</td>
      <td>Johnston, Miss. Catherine Helen "Carrie"</td>
      <td>female</td>
      <td>NaN</td>
      <td>1</td>
      <td>2</td>
      <td>W./C. 6607</td>
      <td>23.4500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>889</th>
      <td>890</td>
      <td>1</td>
      <td>1</td>
      <td>Behr, Mr. Karl Howell</td>
      <td>male</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>111369</td>
      <td>30.0000</td>
      <td>C148</td>
      <td>C</td>
    </tr>
    <tr>
      <th>890</th>
      <td>891</td>
      <td>0</td>
      <td>3</td>
      <td>Dooley, Mr. Patrick</td>
      <td>male</td>
      <td>32.0</td>
      <td>0</td>
      <td>0</td>
      <td>370376</td>
      <td>7.7500</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
  </tbody>
</table>
<p>891 rows × 12 columns</p>
</div>
</div>





{:.input_area}
```python
df_html = html_table_list[0]
df_html.info()
```


{:.output .output_stream}
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Name           891 non-null object
Sex            891 non-null object
Age            714 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Ticket         891 non-null object
Fare           891 non-null float64
Cabin          204 non-null object
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(5)
memory usage: 83.6+ KB

```


<div class="alert alert-info">
<h3> Your turn</h3>
<p> What courses were offered last summer here?
<p>     https://www.sv.uio.no/english/research/phd/summer-school/courses-2017/


</div>




{:.input_area}
```python
courses = pd.read_html('https://www.sv.uio.no/english/research/phd/summer-school/courses-2018/')
```




{:.input_area}
```python
pd.concat(courses)
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
      <th>PhD courses 23 - 27 July 2018:</th>
      <th>PhD courses 30 July - 3 August 2018:</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Case Study Research Methods  Professor Andrew ...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Urban Culture in Global Cities  Professor Wend...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The Political Economy of Public Policy  Dr. Ch...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>The Nordic Model in a Global Context  Professo...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Introduction to Agent-based Modeling and Compu...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Psychoanalysis is not what you think:  Subject...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>Elections and Democracy  Professor José Antoni...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>Anthropologies and Aftermaths: Thinking, Narra...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>Collecting and Analyzing Big Data  Associate P...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>Public Space: People, Power, and Political Eco...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>Mixed and Merged Methods: Toward a Methodologi...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>NaN</td>
      <td>Comparative Policy Studies: Theories, Methods,...</td>
    </tr>
  </tbody>
</table>
</div>
</div>



If that doesn't work, see if the data is in a json.

https://www.indivisible.org



{:.input_area}
```python
indy = pd.read_json('https://indivisible-data.firebaseio.com/indivisible_groups.json')
```




{:.input_area}
```python
indy.head(15)
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
      <th>12029911</th>
      <th>12029912</th>
      <th>12029913</th>
      <th>12029914</th>
      <th>12029915</th>
      <th>12029917</th>
      <th>12029918</th>
      <th>12029919</th>
      <th>12029920</th>
      <th>12029921</th>
      <th>...</th>
      <th>23817638</th>
      <th>23950129</th>
      <th>24047864</th>
      <th>24075701</th>
      <th>24309894</th>
      <th>24390351</th>
      <th>24869547</th>
      <th>25027188</th>
      <th>26147098</th>
      <th>26462557</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>city</th>
      <td>Fort Worth</td>
      <td>Hawley</td>
      <td>Charleston</td>
      <td>Ada</td>
      <td>Athens</td>
      <td>Kingwood</td>
      <td>Orange</td>
      <td>Long Beach</td>
      <td>Dana Point</td>
      <td>Tyler</td>
      <td>...</td>
      <td>Clarkston</td>
      <td>Vienna</td>
      <td>Goldsboro</td>
      <td>Corpus Christi</td>
      <td>Scottsdale</td>
      <td>Missoula</td>
      <td>West Covina</td>
      <td></td>
      <td>Shelbyville</td>
      <td>Rogersville</td>
    </tr>
    <tr>
      <th>country</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>details</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>email</th>
      <td>NaN</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>NaN</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>facebook</th>
      <td>https://www.facebook.com/indivisiblefw/</td>
      <td>https://www.facebook.com/groups/penn10indivisi...</td>
      <td>NaN</td>
      <td>https://www.facebook.com/aheadoh/</td>
      <td>http://www.facebook.com/100daysathens</td>
      <td>https://www.facebook.com/groups/1639853466311375/</td>
      <td>https://www.facebook.com/ctprogressivecoalition/</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>https://www.facebook.com/indivisibleasotincounty/</td>
      <td>https://www.facebook.com/groups/1644467099182559/</td>
      <td>NaN</td>
      <td>https://www.facebook.com/Corpus-Christi-Indivi...</td>
      <td>https://www.facebook.com/groups/Standindivisib...</td>
      <td>https://www.facebook.com/groups/1463471077007886</td>
      <td>http://facebook.com/just.advocacy</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/204843326800292/</td>
      <td>https://www.facebook.com/indivisiblehawkinscou...</td>
    </tr>
    <tr>
      <th>id</th>
      <td>12029911</td>
      <td>12029912</td>
      <td>12029913</td>
      <td>12029914</td>
      <td>12029915</td>
      <td>12029917</td>
      <td>12029918</td>
      <td>12029919</td>
      <td>12029920</td>
      <td>12029921</td>
      <td>...</td>
      <td>23817638</td>
      <td>23950129</td>
      <td>24047864</td>
      <td>24075701</td>
      <td>24309894</td>
      <td>24390351</td>
      <td>24869547</td>
      <td>25027188</td>
      <td>26147098</td>
      <td>26462557</td>
    </tr>
    <tr>
      <th>interaction_count</th>
      <td>2</td>
      <td>9</td>
      <td>0</td>
      <td>5</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>17</td>
      <td>2</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>34</td>
      <td>207</td>
      <td>0</td>
      <td>0</td>
      <td>50</td>
      <td>0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>latitude</th>
      <td>32.742058</td>
      <td>41.404304</td>
      <td>38.393184</td>
      <td>40.784394</td>
      <td>33.905911</td>
      <td>30.046777</td>
      <td>42.5773</td>
      <td>33.756289</td>
      <td>33.475120</td>
      <td>32.235097</td>
      <td>...</td>
      <td>46.362367</td>
      <td>38.938421</td>
      <td>35.381174</td>
      <td>27.729894</td>
      <td>33.601112</td>
      <td>46.971063</td>
      <td>34.066964</td>
      <td>34.0489</td>
      <td>35.4834</td>
      <td>36.4066</td>
    </tr>
    <tr>
      <th>longitude</th>
      <td>-97.381730</td>
      <td>-75.118065</td>
      <td>-81.595470</td>
      <td>-83.813286</td>
      <td>-83.323577</td>
      <td>-95.221022</td>
      <td>-72.3079</td>
      <td>-118.130636</td>
      <td>-117.705675</td>
      <td>-95.320779</td>
      <td>...</td>
      <td>-117.282597</td>
      <td>-77.275520</td>
      <td>-78.062514</td>
      <td>-97.385247</td>
      <td>-111.809488</td>
      <td>-114.111212</td>
      <td>-117.937007</td>
      <td>-111.094</td>
      <td>-86.4603</td>
      <td>-83.0063</td>
    </tr>
    <tr>
      <th>name</th>
      <td>IndivisibleTX12</td>
      <td>PENN8 Indivisible</td>
      <td>Appalachian Americans United</td>
      <td>AHEAD: Allen and Hardin for Election Action &amp; ...</td>
      <td>100+ Days of Action / Athens</td>
      <td>Indivisible TX-02</td>
      <td>Connecticut Progressive Coalition</td>
      <td>MOVI Indivisible, Los Angeles</td>
      <td>N.O.P.E. (Not Our President Ever)</td>
      <td>Voices of East Texas</td>
      <td>...</td>
      <td>Indivisible Asotin County</td>
      <td>Team Rise</td>
      <td>Wayne County Strong</td>
      <td>Corpus Christi Indivisible</td>
      <td>Stand Indivisible Arizona</td>
      <td>Boomer Brigade</td>
      <td>Social Justice Advocacy Project</td>
      <td>Indivisible Tohono</td>
      <td>Shelbyville Indivisible</td>
      <td>Hawkins County Indivisible</td>
    </tr>
    <tr>
      <th>socials</th>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>NaN</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'http://www.f...</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>NaN</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'http://faceb...</td>
      <td>NaN</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
    </tr>
    <tr>
      <th>state</th>
      <td>TX</td>
      <td>PA</td>
      <td>WV</td>
      <td>OH</td>
      <td>GA</td>
      <td>TX</td>
      <td>CT</td>
      <td>CA</td>
      <td>CA</td>
      <td>TX</td>
      <td>...</td>
      <td>WA</td>
      <td>VA</td>
      <td>NC</td>
      <td>TX</td>
      <td>AZ</td>
      <td>MT</td>
      <td>CA</td>
      <td>AZ</td>
      <td>TN</td>
      <td>Tennessee</td>
    </tr>
    <tr>
      <th>tags</th>
      <td>[tx-12, companies import-1492081903938, red-st...</td>
      <td>[pa-10, companies import-1492081903938, margue...</td>
      <td>[wv-02, companies import-1492081903938, whitne...</td>
      <td>[oh-04, oh-05, companies import-1492081903938,...</td>
      <td>[ga-09, ga-10, companies import-1492081903938,...</td>
      <td>[tx-02, tx-08, companies import-1492081903938,...</td>
      <td>[ct-03, companies import-1492081903938, margue...</td>
      <td>[ca-47, companies import-1492081903938, ca dat...</td>
      <td>[ca-48, ca-49, companies import-1492081903938,...</td>
      <td>[tx-01, companies import-1492081903938, red-st...</td>
      <td>...</td>
      <td>[wa-05]</td>
      <td>[va-10]</td>
      <td>NaN</td>
      <td>[tx-27]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>twitter</th>
      <td>NaN</td>
      <td>https://twitter.com/PA10INDIVISIBLE</td>
      <td>NaN</td>
      <td>https://twitter.com/ahead_oh</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://twitter.com/stand_az</td>
      <td>NaN</td>
      <td>http://twitter.com/just_advocacy</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://twitter.com/HctnIndivisible</td>
    </tr>
    <tr>
      <th>zip</th>
      <td>76107</td>
      <td>18428</td>
      <td>25302</td>
      <td>45810</td>
      <td>30605</td>
      <td>77339</td>
      <td>6477</td>
      <td>90803</td>
      <td>92629</td>
      <td>75703</td>
      <td>...</td>
      <td>99403</td>
      <td>22182</td>
      <td>27530</td>
      <td>78411</td>
      <td>85259</td>
      <td>59808</td>
      <td>91790</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
<p>15 rows × 5230 columns</p>
</div>
</div>





{:.input_area}
```python
indy.T
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
      <th>city</th>
      <th>country</th>
      <th>details</th>
      <th>email</th>
      <th>facebook</th>
      <th>id</th>
      <th>interaction_count</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>name</th>
      <th>socials</th>
      <th>state</th>
      <th>tags</th>
      <th>twitter</th>
      <th>zip</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12029911</th>
      <td>Fort Worth</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/indivisiblefw/</td>
      <td>12029911</td>
      <td>2</td>
      <td>32.742058</td>
      <td>-97.381730</td>
      <td>IndivisibleTX12</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TX</td>
      <td>[tx-12, companies import-1492081903938, red-st...</td>
      <td>NaN</td>
      <td>76107</td>
    </tr>
    <tr>
      <th>12029912</th>
      <td>Hawley</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/penn10indivisi...</td>
      <td>12029912</td>
      <td>9</td>
      <td>41.404304</td>
      <td>-75.118065</td>
      <td>PENN8 Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>PA</td>
      <td>[pa-10, companies import-1492081903938, margue...</td>
      <td>https://twitter.com/PA10INDIVISIBLE</td>
      <td>18428</td>
    </tr>
    <tr>
      <th>12029913</th>
      <td>Charleston</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029913</td>
      <td>0</td>
      <td>38.393184</td>
      <td>-81.595470</td>
      <td>Appalachian Americans United</td>
      <td>NaN</td>
      <td>WV</td>
      <td>[wv-02, companies import-1492081903938, whitne...</td>
      <td>NaN</td>
      <td>25302</td>
    </tr>
    <tr>
      <th>12029914</th>
      <td>Ada</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/aheadoh/</td>
      <td>12029914</td>
      <td>5</td>
      <td>40.784394</td>
      <td>-83.813286</td>
      <td>AHEAD: Allen and Hardin for Election Action &amp; ...</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>OH</td>
      <td>[oh-04, oh-05, companies import-1492081903938,...</td>
      <td>https://twitter.com/ahead_oh</td>
      <td>45810</td>
    </tr>
    <tr>
      <th>12029915</th>
      <td>Athens</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://www.facebook.com/100daysathens</td>
      <td>12029915</td>
      <td>2</td>
      <td>33.905911</td>
      <td>-83.323577</td>
      <td>100+ Days of Action / Athens</td>
      <td>[{'category': 'facebook', 'url': 'http://www.f...</td>
      <td>GA</td>
      <td>[ga-09, ga-10, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>30605</td>
    </tr>
    <tr>
      <th>12029917</th>
      <td>Kingwood</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/1639853466311375/</td>
      <td>12029917</td>
      <td>0</td>
      <td>30.046777</td>
      <td>-95.221022</td>
      <td>Indivisible TX-02</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TX</td>
      <td>[tx-02, tx-08, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>77339</td>
    </tr>
    <tr>
      <th>12029918</th>
      <td>Orange</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/ctprogressivecoalition/</td>
      <td>12029918</td>
      <td>0</td>
      <td>42.5773</td>
      <td>-72.3079</td>
      <td>Connecticut Progressive Coalition</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>CT</td>
      <td>[ct-03, companies import-1492081903938, margue...</td>
      <td>NaN</td>
      <td>6477</td>
    </tr>
    <tr>
      <th>12029919</th>
      <td>Long Beach</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12029919</td>
      <td>17</td>
      <td>33.756289</td>
      <td>-118.130636</td>
      <td>MOVI Indivisible, Los Angeles</td>
      <td>NaN</td>
      <td>CA</td>
      <td>[ca-47, companies import-1492081903938, ca dat...</td>
      <td>NaN</td>
      <td>90803</td>
    </tr>
    <tr>
      <th>12029920</th>
      <td>Dana Point</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029920</td>
      <td>2</td>
      <td>33.475120</td>
      <td>-117.705675</td>
      <td>N.O.P.E. (Not Our President Ever)</td>
      <td>NaN</td>
      <td>CA</td>
      <td>[ca-48, ca-49, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>92629</td>
    </tr>
    <tr>
      <th>12029921</th>
      <td>Tyler</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029921</td>
      <td>0</td>
      <td>32.235097</td>
      <td>-95.320779</td>
      <td>Voices of East Texas</td>
      <td>NaN</td>
      <td>TX</td>
      <td>[tx-01, companies import-1492081903938, red-st...</td>
      <td>NaN</td>
      <td>75703</td>
    </tr>
    <tr>
      <th>12029922</th>
      <td>Kalamazoo</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/2020kzoosquad/</td>
      <td>12029922</td>
      <td>0</td>
      <td>42.263841</td>
      <td>-85.617047</td>
      <td>20/20 Kzoo Squad</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>MI</td>
      <td>[mi-06, companies import-1492081903938, elena'...</td>
      <td>NaN</td>
      <td>49008</td>
    </tr>
    <tr>
      <th>12029923</th>
      <td>Smithville</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029923</td>
      <td>0</td>
      <td>35.917978</td>
      <td>-85.786903</td>
      <td>Forward DeKalb</td>
      <td>NaN</td>
      <td>TN</td>
      <td>[tn-04, tn-06, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>37166</td>
    </tr>
    <tr>
      <th>12029925</th>
      <td>Houston</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/441401122867071/</td>
      <td>12029925</td>
      <td>0</td>
      <td>29.740970</td>
      <td>-95.391301</td>
      <td>Indivisible Houston Creative</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TX</td>
      <td>[tx-02, tx-18, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>77006</td>
    </tr>
    <tr>
      <th>12029926</th>
      <td>Cutler</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029926</td>
      <td>2</td>
      <td>39.383437</td>
      <td>-81.800411</td>
      <td>Pioneer Resisters</td>
      <td>NaN</td>
      <td>OH</td>
      <td>[oh-06, companies import-1492081903938, elena'...</td>
      <td>NaN</td>
      <td>45724</td>
    </tr>
    <tr>
      <th>12029927</th>
      <td>Upland</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/1833432906910865</td>
      <td>12029927</td>
      <td>2</td>
      <td>34.105282</td>
      <td>-117.662035</td>
      <td>Indivisible California District 31</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>CA</td>
      <td>[ca-27, ca-31, companies import-1492081903938,...</td>
      <td>https://twitter.com/indivisibleca31</td>
      <td>91786</td>
    </tr>
    <tr>
      <th>12029929</th>
      <td>Orange</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/1881639238735288/</td>
      <td>12029929</td>
      <td>2</td>
      <td>33.808450</td>
      <td>-117.791737</td>
      <td>Constituents of the California 45th Congressio...</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>CA</td>
      <td>[ca-45, companies import-1492081903938, ca dat...</td>
      <td>NaN</td>
      <td>92869</td>
    </tr>
    <tr>
      <th>12029930</th>
      <td>Orange</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/45thdistrictvo...</td>
      <td>12029930</td>
      <td>2</td>
      <td>33.808450</td>
      <td>-117.791737</td>
      <td>45th Congressional District of California Cons...</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>CA</td>
      <td>[ca-45, companies import-1492081903938, ca dat...</td>
      <td>NaN</td>
      <td>92869</td>
    </tr>
    <tr>
      <th>12029932</th>
      <td>New York</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/311758132544636/</td>
      <td>12029932</td>
      <td>7</td>
      <td>40.731829</td>
      <td>-73.989181</td>
      <td>4hours4years</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>NY</td>
      <td>[ny-16, ny-17, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>10003</td>
    </tr>
    <tr>
      <th>12029933</th>
      <td>Atlanta</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029933</td>
      <td>2</td>
      <td>33.711546</td>
      <td>-84.331796</td>
      <td>5th District Resistance</td>
      <td>NaN</td>
      <td>GA</td>
      <td>[ga-05, companies import-1492081903938, whitne...</td>
      <td>NaN</td>
      <td>30316</td>
    </tr>
    <tr>
      <th>12029934</th>
      <td>Everson</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/search/top/?q=indivis...</td>
      <td>12029934</td>
      <td>2</td>
      <td>48.911829</td>
      <td>-122.330175</td>
      <td>Indivisible North Whatcom</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>WA</td>
      <td>[wa-01, shohet wa groups import 1.3 -151501893...</td>
      <td>NaN</td>
      <td>98247</td>
    </tr>
    <tr>
      <th>12029935</th>
      <td>Media</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/383406765329243</td>
      <td>12029935</td>
      <td>128</td>
      <td>39.920460</td>
      <td>-75.416182</td>
      <td>DelCo PA Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>PA</td>
      <td>[pa-01, pa-07, companies import-1492081903938,...</td>
      <td>NaN</td>
      <td>19063</td>
    </tr>
    <tr>
      <th>12029936</th>
      <td>Salem</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/togethernorthshore</td>
      <td>12029936</td>
      <td>1</td>
      <td>40.9009</td>
      <td>-80.8568</td>
      <td>Together North Shore</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>MA</td>
      <td>[ma-06, companies import-1492081903938, margue...</td>
      <td>NaN</td>
      <td>1945</td>
    </tr>
    <tr>
      <th>12029938</th>
      <td>Buffalo</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/361760577506208/</td>
      <td>12029938</td>
      <td>6</td>
      <td>42.951932</td>
      <td>-78.898883</td>
      <td>Stronger Together WNY</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>NY</td>
      <td>[ny-26, companies import-1492081903938, active...</td>
      <td>NaN</td>
      <td>14207</td>
    </tr>
    <tr>
      <th>12029939</th>
      <td>Pleasant Hill</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/413800492297720/</td>
      <td>12029939</td>
      <td>59</td>
      <td>37.954131</td>
      <td>-122.076140</td>
      <td>Indivisible Central Contra Costa County (indiv...</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>CA</td>
      <td>[ca-11, companies import-1492081903938, chloe ...</td>
      <td>https://twitter.com/IndivisibleCCCC</td>
      <td>94523</td>
    </tr>
    <tr>
      <th>12029940</th>
      <td>San Francisco</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/1250893321638409/</td>
      <td>12029940</td>
      <td>2</td>
      <td>37.750021</td>
      <td>-122.415201</td>
      <td>An Action A Day</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>CA</td>
      <td>[ca-12, companies import-1492081903938, chloe ...</td>
      <td>NaN</td>
      <td>94110</td>
    </tr>
    <tr>
      <th>12029944</th>
      <td>San Diego</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029944</td>
      <td>2</td>
      <td>32.861727</td>
      <td>-117.171224</td>
      <td>Berniecrats</td>
      <td>NaN</td>
      <td>CA</td>
      <td>[ca-52, companies import-1492081903938, ca dat...</td>
      <td>NaN</td>
      <td>92122</td>
    </tr>
    <tr>
      <th>12029945</th>
      <td>Oklahoma City</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/ppgpvotes</td>
      <td>12029945</td>
      <td>0</td>
      <td>35.5417</td>
      <td>-97.5649</td>
      <td>Planned Parenthood Great Plains Votes</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>OK</td>
      <td>[ok-05, companies import-1492081903938, compan...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>12029946</th>
      <td>Seattle</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/SeattleIndivisible/</td>
      <td>12029946</td>
      <td>78</td>
      <td>47.632810</td>
      <td>-122.288511</td>
      <td>Seattle Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>WA</td>
      <td>[wa-09, shohet wa groups import 1.3 -151501893...</td>
      <td>https://twitter.com/SEAindivisible</td>
      <td>98112</td>
    </tr>
    <tr>
      <th>12029947</th>
      <td>West Warwick</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/groups/1770753343174786/</td>
      <td>12029947</td>
      <td>0</td>
      <td>41.7238</td>
      <td>-71.4806</td>
      <td>The Democratic Socialist Party (Facebook Group)</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>RI</td>
      <td>[ri-02, companies import-1492081903938, margue...</td>
      <td>NaN</td>
      <td>2893</td>
    </tr>
    <tr>
      <th>12029948</th>
      <td>Houston</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>12029948</td>
      <td>0</td>
      <td>29.798249</td>
      <td>-95.416933</td>
      <td>Progressive Happenings (Houston)</td>
      <td>NaN</td>
      <td>TX</td>
      <td>[tx-02, tx-18, tx-29, companies import-1492081...</td>
      <td>NaN</td>
      <td>77008</td>
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
    </tr>
    <tr>
      <th>22567286</th>
      <td>Adams</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22567286</td>
      <td>0</td>
      <td>43.886723</td>
      <td>-89.807759</td>
      <td>Adams County Wisconsin Indivisible</td>
      <td>NaN</td>
      <td>WI</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>53910</td>
    </tr>
    <tr>
      <th>22707973</th>
      <td>Belfast</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22707973</td>
      <td>0</td>
      <td>44.463502</td>
      <td>-69.037571</td>
      <td>Indivisble Belfast</td>
      <td>NaN</td>
      <td>ME</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>04915</td>
    </tr>
    <tr>
      <th>22718554</th>
      <td>Highland Park</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22718554</td>
      <td>0</td>
      <td>40.500795</td>
      <td>-74.427911</td>
      <td>Writers in League with Libraries</td>
      <td>NaN</td>
      <td>NJ</td>
      <td>[nj-06]</td>
      <td>NaN</td>
      <td>08904</td>
    </tr>
    <tr>
      <th>22728305</th>
      <td>Cahokia</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/Indivisible-Metro-Eas...</td>
      <td>22728305</td>
      <td>57</td>
      <td>38.572218</td>
      <td>-90.166692</td>
      <td>Indivisible Metro East</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>IL</td>
      <td>[il-12, distributed fundraising - participant]</td>
      <td>https://twitter.com/IndivMetroEast</td>
      <td>62206</td>
    </tr>
    <tr>
      <th>22765123</th>
      <td>San Jose</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22765123</td>
      <td>0</td>
      <td>37.186141</td>
      <td>-121.843555</td>
      <td>Together Indivisible - South Bay</td>
      <td>NaN</td>
      <td>CA</td>
      <td>[ca-18]</td>
      <td>NaN</td>
      <td>95120</td>
    </tr>
    <tr>
      <th>22788401</th>
      <td>Newton</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/583530138665247/</td>
      <td>22788401</td>
      <td>0</td>
      <td>42.344457</td>
      <td>-71.248617</td>
      <td>Reach Teach Impeach</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>MA</td>
      <td>[ma-04]</td>
      <td>NaN</td>
      <td>02466</td>
    </tr>
    <tr>
      <th>22795423</th>
      <td>Fridley</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22795423</td>
      <td>0</td>
      <td>45.096702</td>
      <td>-93.253726</td>
      <td>Purple People</td>
      <td>NaN</td>
      <td>MN</td>
      <td>[mn-05]</td>
      <td>NaN</td>
      <td>55432</td>
    </tr>
    <tr>
      <th>22796813</th>
      <td>San Antonio</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/SATXIndivisible/</td>
      <td>22796813</td>
      <td>222</td>
      <td>29.689580</td>
      <td>-98.402411</td>
      <td>SATX Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TX</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>78261</td>
    </tr>
    <tr>
      <th>22827251</th>
      <td>KILMARNOCK</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22827251</td>
      <td>2</td>
      <td>37.735631</td>
      <td>-76.346113</td>
      <td>Rappahannock Indivisible - We The People</td>
      <td>NaN</td>
      <td>VA</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>22482</td>
    </tr>
    <tr>
      <th>22902041</th>
      <td>Alexandria</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22902041</td>
      <td>0</td>
      <td>38.771982</td>
      <td>-77.057273</td>
      <td>Indivisible Below the Beltway</td>
      <td>NaN</td>
      <td>VA</td>
      <td>[va-08]</td>
      <td>NaN</td>
      <td>22307</td>
    </tr>
    <tr>
      <th>22909792</th>
      <td>Eugene</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22909792</td>
      <td>0</td>
      <td>43.939557</td>
      <td>-123.192759</td>
      <td>South Hills</td>
      <td>NaN</td>
      <td>OR</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>97405</td>
    </tr>
    <tr>
      <th>22926046</th>
      <td>Spring Valley</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>22926046</td>
      <td>0</td>
      <td>32.726237</td>
      <td>-116.994318</td>
      <td>Spring Valley Vigilant Voters</td>
      <td>NaN</td>
      <td>CA</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>91977</td>
    </tr>
    <tr>
      <th>22953832</th>
      <td>new york</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>22953832</td>
      <td>0</td>
      <td>40.731829</td>
      <td>-73.989181</td>
      <td>Equality League</td>
      <td>NaN</td>
      <td>NY</td>
      <td>[ny-10]</td>
      <td>NaN</td>
      <td>10003</td>
    </tr>
    <tr>
      <th>22954822</th>
      <td>Virginia Beach</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/HRIndivisible/</td>
      <td>22954822</td>
      <td>0</td>
      <td>36.736543</td>
      <td>-76.035469</td>
      <td>Hampton Roads Indivisble Parent Action Group</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>VA</td>
      <td>[va-02]</td>
      <td>https://twitter.com/IndivisibleVAHR</td>
      <td>23456</td>
    </tr>
    <tr>
      <th>22965111</th>
      <td>Brooklyn</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/BrightestInc/</td>
      <td>22965111</td>
      <td>0</td>
      <td>40.678308</td>
      <td>-73.919936</td>
      <td>Brightest Stands With Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>NY</td>
      <td>[ny-09]</td>
      <td>https://twitter.com/brightest_inc</td>
      <td>11233</td>
    </tr>
    <tr>
      <th>23066874</th>
      <td>Cuyahoga Falls</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/1716405838439650/</td>
      <td>23066874</td>
      <td>0</td>
      <td>41.139266</td>
      <td>-81.474873</td>
      <td>Crooked River Action</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>OH</td>
      <td>[oh-13]</td>
      <td>NaN</td>
      <td>44221</td>
    </tr>
    <tr>
      <th>23125421</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/ratifyERAil/</td>
      <td>23125421</td>
      <td>0</td>
      <td>40.704146</td>
      <td>-89.417889</td>
      <td>Ratify ERA Illinois</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>IL</td>
      <td>NaN</td>
      <td>https://twitter.com/RatifyERAIL</td>
      <td>61571</td>
    </tr>
    <tr>
      <th>23170290</th>
      <td>Minneapolis</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>23170290</td>
      <td>0</td>
      <td>44.938689</td>
      <td>-93.221042</td>
      <td>Indivisible: Lake Street Speaks</td>
      <td>NaN</td>
      <td>MN</td>
      <td>[mn-05]</td>
      <td>NaN</td>
      <td>55406</td>
    </tr>
    <tr>
      <th>23233495</th>
      <td>Brenham</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/txruralvoices</td>
      <td>23233495</td>
      <td>0</td>
      <td>30.215075</td>
      <td>-96.410272</td>
      <td>Texas Rural Voices</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TX</td>
      <td>[tx-10]</td>
      <td>NaN</td>
      <td>77833</td>
    </tr>
    <tr>
      <th>23379027</th>
      <td>Titusville</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/IndivisbleTitu...</td>
      <td>23379027</td>
      <td>0</td>
      <td>28.533319</td>
      <td>-80.792029</td>
      <td>Indivisible Titusville</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>FL</td>
      <td>[fl-08]</td>
      <td>NaN</td>
      <td>32780</td>
    </tr>
    <tr>
      <th>23817638</th>
      <td>Clarkston</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/indivisibleasotincounty/</td>
      <td>23817638</td>
      <td>0</td>
      <td>46.362367</td>
      <td>-117.282597</td>
      <td>Indivisible Asotin County</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>WA</td>
      <td>[wa-05]</td>
      <td>NaN</td>
      <td>99403</td>
    </tr>
    <tr>
      <th>23950129</th>
      <td>Vienna</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/1644467099182559/</td>
      <td>23950129</td>
      <td>0</td>
      <td>38.938421</td>
      <td>-77.275520</td>
      <td>Team Rise</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>VA</td>
      <td>[va-10]</td>
      <td>NaN</td>
      <td>22182</td>
    </tr>
    <tr>
      <th>24047864</th>
      <td>Goldsboro</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>24047864</td>
      <td>0</td>
      <td>35.381174</td>
      <td>-78.062514</td>
      <td>Wayne County Strong</td>
      <td>NaN</td>
      <td>NC</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>27530</td>
    </tr>
    <tr>
      <th>24075701</th>
      <td>Corpus Christi</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/Corpus-Christi-Indivi...</td>
      <td>24075701</td>
      <td>34</td>
      <td>27.729894</td>
      <td>-97.385247</td>
      <td>Corpus Christi Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TX</td>
      <td>[tx-27]</td>
      <td>NaN</td>
      <td>78411</td>
    </tr>
    <tr>
      <th>24309894</th>
      <td>Scottsdale</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/Standindivisib...</td>
      <td>24309894</td>
      <td>207</td>
      <td>33.601112</td>
      <td>-111.809488</td>
      <td>Stand Indivisible Arizona</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>AZ</td>
      <td>NaN</td>
      <td>https://twitter.com/stand_az</td>
      <td>85259</td>
    </tr>
    <tr>
      <th>24390351</th>
      <td>Missoula</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/1463471077007886</td>
      <td>24390351</td>
      <td>0</td>
      <td>46.971063</td>
      <td>-114.111212</td>
      <td>Boomer Brigade</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>MT</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>59808</td>
    </tr>
    <tr>
      <th>24869547</th>
      <td>West Covina</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>http://facebook.com/just.advocacy</td>
      <td>24869547</td>
      <td>0</td>
      <td>34.066964</td>
      <td>-117.937007</td>
      <td>Social Justice Advocacy Project</td>
      <td>[{'category': 'facebook', 'url': 'http://faceb...</td>
      <td>CA</td>
      <td>NaN</td>
      <td>http://twitter.com/just_advocacy</td>
      <td>91790</td>
    </tr>
    <tr>
      <th>25027188</th>
      <td></td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>NaN</td>
      <td>25027188</td>
      <td>50</td>
      <td>34.0489</td>
      <td>-111.094</td>
      <td>Indivisible Tohono</td>
      <td>NaN</td>
      <td>AZ</td>
      <td>NaN</td>
      <td>NaN</td>
      <td></td>
    </tr>
    <tr>
      <th>26147098</th>
      <td>Shelbyville</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>https://www.facebook.com/groups/204843326800292/</td>
      <td>26147098</td>
      <td>0</td>
      <td>35.4834</td>
      <td>-86.4603</td>
      <td>Shelbyville Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>TN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td></td>
    </tr>
    <tr>
      <th>26462557</th>
      <td>Rogersville</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://www.facebook.com/indivisiblehawkinscou...</td>
      <td>26462557</td>
      <td>8</td>
      <td>36.4066</td>
      <td>-83.0063</td>
      <td>Hawkins County Indivisible</td>
      <td>[{'category': 'facebook', 'url': 'https://www....</td>
      <td>Tennessee</td>
      <td>NaN</td>
      <td>https://twitter.com/HctnIndivisible</td>
      <td></td>
    </tr>
  </tbody>
</table>
<p>5230 rows × 15 columns</p>
</div>
</div>





{:.input_area}
```python
pd.read_json?
```




{:.input_area}
```python
indy = pd.read_json('https://indivisible-data.firebaseio.com/indivisible_groups.json', orient = 'index')
```




{:.input_area}
```python
indy = indy.T
```




{:.input_area}
```python
indy.info()
```


{:.output .output_stream}
```
<class 'pandas.core.frame.DataFrame'>
Int64Index: 5230 entries, 12029911 to 26462557
Data columns (total 15 columns):
city                 5130 non-null object
country              42 non-null object
details              3 non-null object
email                2599 non-null object
facebook             3081 non-null object
id                   5230 non-null object
interaction_count    5230 non-null object
latitude             5217 non-null object
longitude            5217 non-null object
name                 5230 non-null object
socials              3310 non-null object
state                5204 non-null object
tags                 5149 non-null object
twitter              702 non-null object
zip                  5057 non-null object
dtypes: object(15)
memory usage: 813.8+ KB

```



{:.input_area}
```python
indy.plot.scatter(y = 'latitude', x ='longitude')
```



{:.output .output_traceback_line}
```
---------------------------------------------------------------------------
```

{:.output .output_traceback_line}
```
ValueError                                Traceback (most recent call last)
```

{:.output .output_traceback_line}
```
<ipython-input-35-a513694423d3> in <module>()
----> 1 indy.plot.scatter(y = 'latitude', x ='longitude')

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/pandas/plotting/_core.py in scatter(self, x, y, s, c, **kwds)
   2853         axes : matplotlib.AxesSubplot or np.array of them
   2854         """
-> 2855         return self(kind='scatter', x=x, y=y, c=c, s=s, **kwds)
   2856 
   2857     def hexbin(self, x, y, C=None, reduce_C_function=None, gridsize=None,

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/pandas/plotting/_core.py in __call__(self, x, y, kind, ax, subplots, sharex, sharey, layout, figsize, use_index, title, grid, legend, style, logx, logy, loglog, xticks, yticks, xlim, ylim, rot, fontsize, colormap, table, yerr, xerr, secondary_y, sort_columns, **kwds)
   2675                           fontsize=fontsize, colormap=colormap, table=table,
   2676                           yerr=yerr, xerr=xerr, secondary_y=secondary_y,
-> 2677                           sort_columns=sort_columns, **kwds)
   2678     __call__.__doc__ = plot_frame.__doc__
   2679 

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/pandas/plotting/_core.py in plot_frame(data, x, y, kind, ax, subplots, sharex, sharey, layout, figsize, use_index, title, grid, legend, style, logx, logy, loglog, xticks, yticks, xlim, ylim, rot, fontsize, colormap, table, yerr, xerr, secondary_y, sort_columns, **kwds)
   1900                  yerr=yerr, xerr=xerr,
   1901                  secondary_y=secondary_y, sort_columns=sort_columns,
-> 1902                  **kwds)
   1903 
   1904 

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/pandas/plotting/_core.py in _plot(data, x, y, subplots, ax, kind, **kwds)
   1685         if isinstance(data, DataFrame):
   1686             plot_obj = klass(data, x=x, y=y, subplots=subplots, ax=ax,
-> 1687                              kind=kind, **kwds)
   1688         else:
   1689             raise ValueError("plot kind %r can only be used for data frames"

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/pandas/plotting/_core.py in __init__(self, data, x, y, s, c, **kwargs)
    835             # the handling of this argument later
    836             s = 20
--> 837         super(ScatterPlot, self).__init__(data, x, y, s=s, **kwargs)
    838         if is_integer(c) and not self.data.columns.holds_integer():
    839             c = self.data.columns[c]

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/pandas/plotting/_core.py in __init__(self, data, x, y, **kwargs)
    810             y = self.data.columns[y]
    811         if len(self.data[x]._get_numeric_data()) == 0:
--> 812             raise ValueError(self._kind + ' requires x column to be numeric')
    813         if len(self.data[y]._get_numeric_data()) == 0:
    814             raise ValueError(self._kind + ' requires y column to be numeric')

```

{:.output .output_traceback_line}
```
ValueError: scatter requires x column to be numeric
```


If that doesn't work, see if someone has already written a program for it.



scrape gofundme python



{:.input_area}
```python
from bs4 import BeautifulSoup    
import requests

page = 1
links = set()
length = 0

while True:
    print("Page {}".format(page))
    gofundme = requests.get('https://www.gofundme.com/mvc.php?route=category/loadMoreTiles&page={}&term=medical-fundraising&country=GB&initialTerm='.format(page))
    soup = BeautifulSoup(gofundme.content, "html.parser")
    links.update([a['href'] for a in soup.find_all('a', href=True)])

    # Stop when no new links are found
    if len(links) == length:
        break

    length = len(links)
    page += 1

for link in sorted(links):
    print(link)
```


{:.output .output_stream}
```
Page 1
Page 2
Page 3
Page 4

```


{:.output .output_traceback_line}
```
---------------------------------------------------------------------------
```

{:.output .output_traceback_line}
```
TypeError                                 Traceback (most recent call last)
```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/urllib3/connectionpool.py in _make_request(self, conn, method, url, timeout, chunked, **httplib_request_kw)
    379             try:  # Python 2.7, use buffering of HTTP responses
--> 380                 httplib_response = conn.getresponse(buffering=True)
    381             except TypeError:  # Python 2.6 and older, Python 3

```

{:.output .output_traceback_line}
```
TypeError: getresponse() got an unexpected keyword argument 'buffering'
```

{:.output .output_traceback_line}
```

During handling of the above exception, another exception occurred:

```

{:.output .output_traceback_line}
```
KeyboardInterrupt                         Traceback (most recent call last)
```

{:.output .output_traceback_line}
```
<ipython-input-38-86da546b6ae1> in <module>()
      8 while True:
      9     print("Page {}".format(page))
---> 10     gofundme = requests.get('https://www.gofundme.com/mvc.php?route=category/loadMoreTiles&page={}&term=medical-fundraising&country=GB&initialTerm='.format(page))
     11     soup = BeautifulSoup(gofundme.content, "html.parser")
     12     links.update([a['href'] for a in soup.find_all('a', href=True)])

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/requests/api.py in get(url, params, **kwargs)
     70 
     71     kwargs.setdefault('allow_redirects', True)
---> 72     return request('get', url, params=params, **kwargs)
     73 
     74 

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/requests/api.py in request(method, url, **kwargs)
     56     # cases, and look like a memory leak in others.
     57     with sessions.Session() as session:
---> 58         return session.request(method=method, url=url, **kwargs)
     59 
     60 

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/requests/sessions.py in request(self, method, url, params, data, headers, cookies, files, auth, timeout, allow_redirects, proxies, hooks, stream, verify, cert, json)
    506         }
    507         send_kwargs.update(settings)
--> 508         resp = self.send(prep, **send_kwargs)
    509 
    510         return resp

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/requests/sessions.py in send(self, request, **kwargs)
    616 
    617         # Send the request
--> 618         r = adapter.send(request, **kwargs)
    619 
    620         # Total elapsed time of the request (approximately)

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/requests/adapters.py in send(self, request, stream, timeout, verify, cert, proxies)
    438                     decode_content=False,
    439                     retries=self.max_retries,
--> 440                     timeout=timeout
    441                 )
    442 

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/urllib3/connectionpool.py in urlopen(self, method, url, body, headers, retries, redirect, assert_same_host, timeout, pool_timeout, release_conn, chunked, body_pos, **response_kw)
    599                                                   timeout=timeout_obj,
    600                                                   body=body, headers=headers,
--> 601                                                   chunked=chunked)
    602 
    603             # If we're going to release the connection in ``finally:``, then

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/urllib3/connectionpool.py in _make_request(self, conn, method, url, timeout, chunked, **httplib_request_kw)
    381             except TypeError:  # Python 2.6 and older, Python 3
    382                 try:
--> 383                     httplib_response = conn.getresponse()
    384                 except Exception as e:
    385                     # Remove the TypeError from the exception chain in Python 3;

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/http/client.py in getresponse(self)
   1329         try:
   1330             try:
-> 1331                 response.begin()
   1332             except ConnectionError:
   1333                 self.close()

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/http/client.py in begin(self)
    295         # read until we get a non-100 response
    296         while True:
--> 297             version, status, reason = self._read_status()
    298             if status != CONTINUE:
    299                 break

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/http/client.py in _read_status(self)
    256 
    257     def _read_status(self):
--> 258         line = str(self.fp.readline(_MAXLINE + 1), "iso-8859-1")
    259         if len(line) > _MAXLINE:
    260             raise LineTooLong("status line")

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/socket.py in readinto(self, b)
    584         while True:
    585             try:
--> 586                 return self._sock.recv_into(b)
    587             except timeout:
    588                 self._timeout_occurred = True

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/urllib3/contrib/pyopenssl.py in recv_into(self, *args, **kwargs)
    278     def recv_into(self, *args, **kwargs):
    279         try:
--> 280             return self.connection.recv_into(*args, **kwargs)
    281         except OpenSSL.SSL.SysCallError as e:
    282             if self.suppress_ragged_eofs and e.args == (-1, 'Unexpected EOF'):

```

{:.output .output_traceback_line}
```
~/anaconda3/lib/python3.6/site-packages/OpenSSL/SSL.py in recv_into(self, buffer, nbytes, flags)
   1712             result = _lib.SSL_peek(self._ssl, buf, nbytes)
   1713         else:
-> 1714             result = _lib.SSL_read(self._ssl, buf, nbytes)
   1715         self._raise_ssl_error(self._ssl, result)
   1716 

```

{:.output .output_traceback_line}
```
KeyboardInterrupt: 
```




{:.input_area}
```python
links
```





{:.output .output_data_text}
```
{'https://www.gofundme.com/2ckfhnjd',
 'https://www.gofundme.com/338ii34',
 'https://www.gofundme.com/46snno8',
 'https://www.gofundme.com/47bd9nc',
 'https://www.gofundme.com/4mgv7cw',
 'https://www.gofundme.com/52kh49-help-save-skittles',
 'https://www.gofundme.com/58rz1k',
 'https://www.gofundme.com/882v7-medical-fundraising',
 'https://www.gofundme.com/Coleen-s-Medical-Fundraising',
 'https://www.gofundme.com/c8tn3nfw',
 'https://www.gofundme.com/chogtaa',
 'https://www.gofundme.com/cswjm-medical-fundraising',
 'https://www.gofundme.com/delinas-medical-fundraising',
 'https://www.gofundme.com/jeannette039s-medical-fundraising',
 'https://www.gofundme.com/joeyneedssomeeyes',
 'https://www.gofundme.com/medical-fundraising-for-barry-boys',
 'https://www.gofundme.com/medical-fundraising-for-leah',
 'https://www.gofundme.com/mrhueu',
 'https://www.gofundme.com/ovlee4',
 'https://www.gofundme.com/pahouasmedical',
 'https://www.gofundme.com/poes-medical-fundraising',
 'https://www.gofundme.com/pxd4u8',
 'https://www.gofundme.com/savingpaws4love',
 'https://www.gofundme.com/standupforedison',
 'https://www.gofundme.com/t8vfjrek',
 'https://www.gofundme.com/troy-alberding',
 'https://www.gofundme.com/umd642xg'}
```



If that doesn't work, see if someone has already written a general program for it.



<code>from newspaper import Article</code>

![](images/fail_to_install.png)

pip install newspaper3k



{:.input_area}
```python
from newspaper import Article
```




{:.input_area}
```python
url = 'http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html'
```




{:.input_area}
```python
article = Article(url)
```




{:.input_area}
```python
article.download()
```




{:.input_area}
```python
article.parse()
```




{:.input_area}
```python
article.authors
```





{:.output .output_data_text}
```
['Christopher Carbone']
```





{:.input_area}
```python
article.publish_date
```





{:.output .output_data_text}
```
datetime.datetime(2018, 7, 31, 0, 0)
```





{:.input_area}
```python
article.publish_date
```





{:.output .output_data_text}
```
datetime.datetime(2018, 7, 31, 0, 0)
```





{:.input_area}
```python
article.meta_data


```





{:.output .output_data_text}
```
defaultdict(dict,
            {'classification': '/FOX NEWS/TECH/COMPANIES/Facebook,/FOX NEWS/TECH,/FOX NEWS/NEWS EVENTS/Russia Investigation',
             'classification-isa': 'facebook,tech',
             'dc.creator': 'Christopher Carbone',
             'dc.date': '2018-07-31',
             'dc.description': 'Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..',
             'dc.format': 'text/html',
             'dc.identifier': 'ec9da88c-98b1-4f65-af33-45b67a0a1b81',
             'dc.language': 'en-US',
             'dc.publisher': 'Fox News',
             'dc.source': 'Fox News',
             'dc.title': "Facebook finds 'sophisticated' efforts to disrupt US politics, removes 32 accounts",
             'dc.type': 'Text.Article',
             'dcterms.abstract': 'Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..',
             'dcterms.created': '2018-07-31 07:17:12 EDT',
             'dcterms.modified': '2018-07-31 07:17:12 EDT',
             'description': 'Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..',
             'fb': {'app_id': 113186182048399, 'pages': 15704546335},
             'og': {'description': 'Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..',
              'image': 'http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/0/0/694940094001_5816244313001_5816244794001-vs.jpg?ve=1',
              'site_name': 'Fox News',
              'title': "Facebook finds 'sophisticated' efforts to disrupt US politics, removes 32 accounts",
              'type': 'article',
              'url': 'http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html'},
             'pagetype': 'article',
             'prism.aggregationType': 'subsection',
             'prism.channel': 'fnc',
             'prism.section': 'tech',
             'robots': 'noarchive, noodp',
             'twitter': {'card': 'summary_large_image',
              'creator': '@foxnews',
              'description': 'Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..',
              'image': 'http://a57.foxnews.com/images.foxnews.com/content/dam/fox-news/images/2018/04/04/facebook-logo-reuters.jpg.img.png/0/0/1522853765622.png?ve=1',
              'site': '@foxnews',
              'title': "Facebook finds 'sophisticated' efforts to disrupt US politics, removes 32 accounts",
              'url': 'http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html'},
             'viewport': 'width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no'})
```





{:.input_area}
```python
print(article.text)
```


{:.output .output_stream}
```
Facebook uncovered "sophisticated" efforts, possibly linked to Russia, to influence American politics in advance of the U.S. midterm elections.

The company said in a blog post that it removed 32 accounts from Facebook and Instagram because they were involved in "coordinated" political behavior and appeared to be fake.

Facebook did not explicitly say that the effort was aimed at influencing the midterm elections in November, but the timing of the suspicious activity would be consistent with such an attempt.

The company, which said it is in the early stages of its investigation, held briefings in the House and Senate this week.

The company said it doesn't know who is behind the efforts, but said there may be connections to Russia. Facebook said it has found some connections between the accounts it removed and the accounts connected to Russia's Internet Research Agency that it removed before and after the 2016 U.S. presidential elections.

TWITTER BRINGS IN ANTI-TRUMP ACADEMICS TO FIGHT BIAS

U.S. Senator Mark Warner, D-Va., who introduced the Honest Ads Act earlier this year to help prevent foreign interference in U.S. elections, praised Facebook's elections.

“Today’s disclosure is further evidence that the Kremlin continues to exploit platforms like Facebook to sow division and spread disinformation, and I am glad that Facebook is taking some steps to pinpoint and address this activity," Warner said in a statement to Fox News.

"I also expect Facebook, along with other platform companies, will continue to identify Russian troll activity and to work with Congress on updating our laws to better protect our democracy in the future,” he continued.

"It’s clear that whoever set up these accounts went to much greater lengths to obscure their true identities than the Russian-based Internet Research Agency (IRA) has in the past," Facebook said in its statement. "We believe this could be partly due to changes we’ve made over the last year to make this kind of abuse much harder."

FACEBOOK, TECH GIANTS CREATING 'CRISIS IN DEMOCRACY,' UK REPORT SAYS

"The goal of these operations is to sow discord, distrust, and division in an attempt to undermine public faith in our institutions and our political system," Sen. Richard Burr, R-N.C., said in a statement. "The Russians want a weak America. There is still much that needs to be done to prevent and counter foreign interference on social media."

The president has made it clear that his administration will not tolerate foreign interference into our electoral process from any nation state or other malicious actors," White House Deputy Press Secretary Hogan Gidley said in a statement late Tuesday.

The earliest page was created in March 2017. Facebook says more than 290,000 accounts followed at least one of the fake pages. The most followed Facebook Pages had names such as "Aztlan Warriors," ''Black Elevation," ''Mindful Being," and "Resisters."

Facebook says the pages ran about 150 ads for $11,000 on Facebook and Instagram, paid for in U.S. and Canadian dollars. The first ad was created in April 2017; the last was created in June 2018.

The perpetrators used virtual private networks and internet phone services, and paid third parties to run ads on their behalf, according to Facebook.

In addition, Facebook revealed that its partnership with the Atlantic Council helped it to identify the bad actors. One of the groups with roughly 4,000 members was located based on leads from U.S. Special Counsel Robert Mueller's recent indictment of 12 Russian nationals for their role in hacking and disinformation efforts during the 2016 U.S. presidential election.

That group was created by Russian government figures but had been dormant since Facebook disabled its administrators last year. However, the tech company chose to remove the group to protect the privacy of its members in advance of a forthcoming report from the Atlantic Council that will analyze the Pages, profiles and accounts that Facebook disabled today.

Freedom From Facebook, a group that has been pushing for the tech company to be broken up, said that today's announcement was not enough.

“While it is good news that this latest attack on democracy via Facebook was discovered early, the fact that it happened highlights the danger posed when so much power is concentrated in a single company," said Sarah Miller, a campaigner with Freedom From Facebook.

Miller continued: “We will not be safe from foreign interference -- and Facebook’s own business model of profiting off of bad actors -- until Congress and the FTC step in to break up the company and impose strong privacy rules.”

Fox News' Bree Tracey and The Associated Press contributed to this report.

```



{:.input_area}
```python
def get_article_info(url):
    article = Article(url)
    article.download()
    article.parse()
    
    article_details = {'title'       : article.title,
                       'text'        : article.text,
                       'url'         : article.url,
                       'authors'     : article.authors,
                       'html'        : article.html,
                       'date'        : article.publish_date,
                       'description' : article.meta_description,
                       'publisher'   : article.meta_data['dc.publisher']}
    return article_details
                       
                       
                       
```




{:.input_area}
```python
article_details = get_article_info(url)
```




{:.input_area}
```python
article_details
```





{:.output .output_data_text}
```
{'authors': ['Christopher Carbone'],
 'date': datetime.datetime(2018, 7, 31, 0, 0),
 'description': 'Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..',
 'html': '\n\n\n<!DOCTYPE html>\n<html lang="en">\n    \n\n\n\n<head>\n    \n    \n    \n\n\n\n    <script type="text/javascript">\n        var FOX_pagedescriptor = window.FOX_pagedescriptor = {breadcrumb:"/tech", isMgmt: "false", site: "fn2", cq: "true" };\n    </script>\n    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">\n    <meta charset="UTF-8">\n    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">\n    \n\n\n\t\t\t    \n\n<title>Facebook finds \'sophisticated\' efforts to disrupt US politics, removes 32 accounts | Fox News</title>\n<meta name="dc.title" content="Facebook finds \'sophisticated\' efforts to disrupt US politics, removes 32 accounts">\n<meta name="description" content="Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..">\n<meta name="dc.description" content="Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..">\n<meta name="dcterms.abstract" content="Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..">\n<meta name="dc.publisher" content="Fox News">\n<meta name="dc.creator" content="Christopher Carbone">\n<meta name="dc.date" content="2018-07-31">\n<meta name="dcterms.created" scheme="dcterms.ISO8601" content="2018-07-31 07:17:12 EDT">\n<meta name="dcterms.modified" scheme="dcterms.ISO8601" content="2018-07-31 07:17:12 EDT">\n<meta name="dc.source" content="Fox News">\n<meta name="dc.identifier" scheme="dcterms.URI" content="ec9da88c-98b1-4f65-af33-45b67a0a1b81">\n<meta name="dc.subject" content="">\n<meta name="keywords" content="">\n<link rel="shorturl" href="https://fxn.ws/2M95GED">\n<link rel="canonical" href="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html">\n\n    <link rel="amphtml" href="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.amp.html">\n\n\n<link rel="standout" href="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html"> \n<meta name="prism.aggregationType" content="subsection">\n<meta name="prism.genre" content="">\n<meta name="dc.type" scheme="dcterms.DCMIType" content="Text.Article">\n<meta name="prism.channel" content="fnc">\n<meta name="prism.section" content="tech">\n<meta name="prism.subsection1" content="">\n<meta name="prism.subsection2" content="">\n<meta name="prism.subsection3" content="">\n<meta name="prism.subsection4" content="">\n<meta property="og:title" content="Facebook finds \'sophisticated\' efforts to disrupt US politics, removes 32 accounts">\n<meta property="og:description" content="Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..">\n<meta property="og:type" content="article">\n\n    <link rel="image_src" href="http://a57.foxnews.com/images.foxnews.com/content/dam/fox-news/images/2018/04/04/facebook-logo-reuters.jpg.img.png/0/0/1522853765622.png?ve=1">\n    <meta property="og:image" content="http://a57.foxnews.com/images.foxnews.com/content/dam/fox-news/images/2018/04/04/facebook-logo-reuters.jpg.img.png/0/0/1522853765622.png?ve=1">\n\n    \n    <meta property="og:image" content="http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/0/0/694940094001_5816244313001_5816244794001-vs.jpg?ve=1">\n\n<meta property="og:url" content="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html">\n<meta property="og:site_name" content="Fox News">\n<meta property="fb:pages" content="15704546335">\n\n\n    \n    \n        <meta name="classification" content="/FOX NEWS/TECH/COMPANIES/Facebook,/FOX NEWS/TECH,/FOX NEWS/NEWS EVENTS/Russia Investigation">\n    \n    \n        <meta name="classification-isa" content="facebook,tech">\n    \n\n<meta property="fb:app_id" content="113186182048399">\n\n\n<meta name="twitter:site" content="@foxnews">\n<meta name="twitter:card" content="summary_large_image">\n\n    <meta name="twitter:title" content="Facebook finds \'sophisticated\' efforts to disrupt US politics, removes 32 accounts">\n    <meta name="twitter:description" content="Facebook said it has uncovered sophisticated efforts, possibly linked to Russia, to influence U.S. politics on its platforms..">\n    <meta name="twitter:url" content="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html">\n    <meta name="twitter:creator" content="@foxnews">\n    \n        <meta name="twitter:image" content="http://a57.foxnews.com/images.foxnews.com/content/dam/fox-news/images/2018/04/04/facebook-logo-reuters.jpg.img.png/0/0/1522853765622.png?ve=1">\n    \n\n<meta name="dc.format" scheme="dcterms.IMT" content="text/html">\n<meta name="dc.language" scheme="dcterms.RFC4646" content="en-US">\n<meta http-equiv="content-type" content="text/html; charset=UTF-8">\n<meta http-equiv="content-language" content="en">\n<meta name="livefyreconfig" collectionMeta="eyJhbGciOiJIUzI1NiJ9.eyJ0YWdzIjoiZm94LW5ld3MsbmV3cy1ldmVudHMscnVzc2lhLWludmVzdGlnYXRpb24sZm94LW5ld3MsdGVjaCxmb3gtbmV3cyx0ZWNoLGNvbXBhbmllcyxmYWNlYm9vayIsInRpdGxlIjoiRmFjZWJvb2sgZmluZHMgJ3NvcGhpc3RpY2F0ZWQnIGVmZm9ydHMgdG8gZGlzcnVwdCBVUyBwb2xpdGljcywgcmVtb3ZlcyAzMiBhY2NvdW50cyIsImNoZWNrc3VtIjoiOGU1ODhkYmY0NmYwNTIxODNlMjMxZGYzZjgyZGNjYmUiLCJhcnRpY2xlSWQiOiJlYzlkYTg4Yy05OGIxLTRmNjUtYWYzMy00NWI2N2EwYTFiODEiLCJ1cmwiOiJodHRwOi8vd3d3LmZveG5ld3MuY29tL3RlY2gvMjAxOC8wNy8zMS9mYWNlYm9vay1maW5kcy1zb3BoaXN0aWNhdGVkLWVmZm9ydHMtdG8tZGlzcnVwdC11cy1wb2xpdGljcy1yZW1vdmVzLTMyLWFjY291bnRzLmh0bWwifQ.IRg99Fc9Ri-GEsADWavKTqUDQRg94IcGUl7eofdojGI" checksum="8e588dbf46f052183e231df3f82dccbe" articleId="ec9da88c-98b1-4f65-af33-45b67a0a1b81" siteId="310256" enable="true"/>\n<meta name="livefyreblogconfig" collectionMeta="eyJhbGciOiJIUzI1NiJ9.eyJ0YWdzIjoiZm94LW5ld3MsbmV3cy1ldmVudHMscnVzc2lhLWludmVzdGlnYXRpb24sZm94LW5ld3MsdGVjaCxmb3gtbmV3cyx0ZWNoLGNvbXBhbmllcyxmYWNlYm9vayIsInRpdGxlIjoiRmFjZWJvb2sgZmluZHMgJ3NvcGhpc3RpY2F0ZWQnIGVmZm9ydHMgdG8gZGlzcnVwdCBVUyBwb2xpdGljcywgcmVtb3ZlcyAzMiBhY2NvdW50cyIsImNoZWNrc3VtIjoiZDFlNmQ5NDBlNjk2NjgwZWIyYjlkZWZlMTIwNWJiYjciLCJ0eXBlIjoibGl2ZWJsb2ciLCJhcnRpY2xlSWQiOiJlYzlkYTg4Yy05OGIxLTRmNjUtYWYzMy00NWI2N2EwYTFiODFibG9nIiwidXJsIjoiaHR0cDovL3d3dy5mb3huZXdzLmNvbS90ZWNoLzIwMTgvMDcvMzEvZmFjZWJvb2stZmluZHMtc29waGlzdGljYXRlZC1lZmZvcnRzLXRvLWRpc3J1cHQtdXMtcG9saXRpY3MtcmVtb3Zlcy0zMi1hY2NvdW50cy5odG1sIn0.0v_2A7_pG3cJ1jPTIyOOwQwfsYyUh48noXvIq7Kw5hU" checksum="d1e6d940e696680eb2b9defe1205bbb7" articleId="ec9da88c-98b1-4f65-af33-45b67a0a1b81blog" siteId="310256" enable="false"/>\n<meta name="livefyrereviewsconfig" collectionMeta="eyJhbGciOiJIUzI1NiJ9.eyJ0YWdzIjoiZm94LW5ld3MsbmV3cy1ldmVudHMscnVzc2lhLWludmVzdGlnYXRpb24sZm94LW5ld3MsdGVjaCxmb3gtbmV3cyx0ZWNoLGNvbXBhbmllcyxmYWNlYm9vayIsInRpdGxlIjoiRmFjZWJvb2sgZmluZHMgJ3NvcGhpc3RpY2F0ZWQnIGVmZm9ydHMgdG8gZGlzcnVwdCBVUyBwb2xpdGljcywgcmVtb3ZlcyAzMiBhY2NvdW50cyIsImNoZWNrc3VtIjoiNzgxMzBiZjc2NWJiNDBjMTNlN2ViOTdmNThiODZiNzUiLCJ0eXBlIjoicmV2aWV3cyIsImFydGljbGVJZCI6ImVjOWRhODhjLTk4YjEtNGY2NS1hZjMzLTQ1YjY3YTBhMWI4MXJldmlld3MiLCJ1cmwiOiJodHRwOi8vd3d3LmZveG5ld3MuY29tL3RlY2gvMjAxOC8wNy8zMS9mYWNlYm9vay1maW5kcy1zb3BoaXN0aWNhdGVkLWVmZm9ydHMtdG8tZGlzcnVwdC11cy1wb2xpdGljcy1yZW1vdmVzLTMyLWFjY291bnRzLmh0bWwifQ.tIQ-CBqsSIy_e9UVberRlNntR-XrmNlRo2hA2o9WS20" checksum="78130bf765bb40c13e7eb97f58b86b75" articleId="ec9da88c-98b1-4f65-af33-45b67a0a1b81reviews" siteId="310256" enable="true"/>\n\n<meta name="pagetype" content="article"/>\n\n\n\t\n\t\n\t\t<meta name="robots" content="noarchive, noodp ">\n\t\n\n\n<meta http-equiv="x-dns-prefetch-control" content="on"> \n<link rel="dns-prefetch" href="//a57.foxnews.com"> \n<link rel="dns-prefetch" href="//global.fncstatic.com"> \n<link rel="dns-prefetch" href="//images.outbrain.com"> \n<link rel="dns-prefetch" href="//widgets.outbrain.com"> \n<link rel="dns-prefetch" href="//widget-cdn.rpxnow.com"> \n<link rel="dns-prefetch" href="//pagead2.googlesyndication.com"> \n<link rel="dns-prefetch" href="//securepubads.g.doubleclick.net"> \n<link rel="dns-prefetch" href="//z.moatads.com"> \n<link rel="dns-prefetch" href="//hpr.outbrain.com"> \n<link rel="dns-prefetch" href="//metrics.foxnews.com"> \n<link rel="dns-prefetch" href="//cdn.tt.omtrdc.net"> \n<link rel="dns-prefetch" href="//foxnews.demdex.net"> \n<link rel="dns-prefetch" href="//static.chartbeat.com"> \n<link rel="dns-prefetch" href="//cdn.taboola.com"> \n<link rel="dns-prefetch" href="//assets.tapad.com"> \n<link rel="dns-prefetch" href="//www.googletagservices.com"> \n<link rel="dns-prefetch" href="//cdn.livefyre.com"> \n<link rel="dns-prefetch" href="//foxnews.tt.omtrdc.net"> \n<link rel="dns-prefetch" href="//log.outbrain.com"> \n<link rel="dns-prefetch" href="//cdn.optimizely.com"> \n<link rel="dns-prefetch" href="//b.scorecardresearch.com">\n\n\n\n\n\n  \n    <link rel="schema.dc" href="http://purl.org/dc/elements/1.1/">\n\t<link href=\'https://fonts.googleapis.com/css?family=Source+Sans+Pro:200,300,400,600,700,900|Roboto:300,400,700,900\' rel=\'stylesheet\' type=\'text/css\'>\n \t<link rel="schema.dcterms" href="http://purl.org/dc/terms/">\n    <link rel="schema.prism" href="http://prismstandard.org/namespaces/basic/2.1/"> \n    <link rel="schema.iptc" href="http://iptc.org/std/nar/2006-10-01/">   \n    <link rel="icon" href="http://global.fncstatic.com/static/v/fn2/img/favicon.png" type="image/png">\n    <link rel="shortcut icon" href="http://global.fncstatic.com/static/v/fn2/img/favicon.ico">   \n    \n       \n    \n    <script src="http://global.fncstatic.com/static/orion/scripts/core/ag.core.js?201701171314"></script>\n\t<script src="http://global.fncstatic.com/static/isa/core.js?201701171314"></script>\n\t\n        \n    \n\n\n\n\n<script src="http://global.fncstatic.com/static/orion/scripts/core/pages/articles.new/loader.js?201701171314"></script>\n<link href="http://global.fncstatic.com/static/orion/styles/css/fox-news/article-new.rs.css?201701171314" rel="stylesheet" type="text/css">\n\n\n\n\n\n<script type="application/ld+json">\n      {\n        "@context": "http://schema.org",\n        "@type": "NewsArticle",\n        "mainEntityOfPage": "http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html",\n        "headline": "Facebook finds &#x27;sophisticated&#x27; efforts to disrupt US politics, removes 32 accounts",\n        "datePublished": "Tue Jul 31 19:15:00 EDT 2018",\n        "dateModified": "Tue Jul 31 19:15:00 EDT 2018",\n        "description": "Facebook said it has uncovered &quot;sophisticated&quot; efforts, possibly linked to Russia, to influence U.S. politics on its platforms..",\n        "author": {\n          "@type": "Person",\n          "name": "Christopher Carbone"\n        },\n        "publisher": {\n          "@type": "Organization",\n          "name": "Fox News",\n          "logo": {\n            "@type": "ImageObject",\n            "url": "http://global.fncstatic.com/static/cq/fn/img/global/fox-news-logo.png",\n            "width": 133,\n            "height": 60\n          }\n        },\n        "image": {\n          "@type": "ImageObject",\n          "url": "http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/696/392/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1",\n          "height": 392,\n          "width": 696\n        }\n      }\n</script>\n\n\n<script type="application/ld+json">\n\t{\n\t\t"@context": "http://schema.org/", \n\t\t"@type": "WebPage",\n\t\t"name": "Facebook finds &#x27;sophisticated&#x27; efforts to disrupt US politics, removes 32 accounts", \n\t\t"speakable": {"@type": "SpeakableSpecification","cssSelector": [".head1", ".speakable"]}, \n\t\t"url": "http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html"\n\t}\n</script>\n\n\n\n\n\n  \n       \n       \n</head>\n\n\n    \n \n\t\n<body class="fn article-single">\n\t\n\n\n\n\n    <div id="wrapper" class="wrapper">     \t\n\t    \n\n\n\n<header class="site-header nav-closed">\n\n    \n       \t\n  \t\t\n\n\n\n\t<div class="branding">\n\t\t<a class="logo" href="//www.foxnews.com">Fox News</a>\n\t\t<div class="current-time">\n\t\t\t\t<span class="time"></span>\n\t\t\t\t<span class="zone"></span>\n\t\t</div>\n\t</div>\n\n\t<div class="nav-row nav-row-upper">\n\t\t<div class="inner">\n\t\t\t<div class="primary-nav tablet-desktop">\n\t\t\t\t<nav id="main-nav">\n\t\t\t\t\t<ul>\n\t\t\t\t\t\t<li class="menu-us"><a href="//www.foxnews.com/us.html">U.S.</a></li>\n\t\t\t\t\t\t<li class="menu-world"><a href="//www.foxnews.com/world.html">World</a></li>\n\t\t\t\t\t\t<li class="menu-opinion"><a href="//www.foxnews.com/opinion.html">Opinion</a></li>\n\t\t\t\t\t\t<li class="menu-politics"><a href="//www.foxnews.com/politics.html">Politics</a></li>\n\t\t\t\t\t\t<li class="menu-entertainment"><a href="//www.foxnews.com/entertainment.html">Entertainment</a></li>\n\t\t\t\t\t\t<li class="menu-business"><a href="//www.foxbusiness.com">Business</a></li>\n\t\t\t\t\t\t<li class="menu-lifestyle"><a href="//www.foxnews.com/lifestyle.html">Lifestyle</a></li>\n\t\t\t\t\t\t<li class="menu-tv"><a href="//www.foxnews.com/shows.html">TV</a></li>\n\t\t\t\t\t\t<li class="menu-radio"><a href="//radio.foxnews.com">Radio</a></li>\n\t\t\t\t\t\t<li class="menu-more"><a href="#" class="js-menu-toggle">More</a></li>\n\t\t\t\t\t</ul>\n\t\t\t\t</nav>\n\t\t\t</div>\n\n\t\t\t<div class="meta">\n\t\t\t\t<div class="weather">\n\t\t\t\t\t<a href="http://www.foxnews.com/weather/your-weather/index.html">\n\t\t\t\t\t\t<div class="info">\n\t\t\t\t\t\t\t<div class="location"></div>\n\t\t\t\t\t\t\t<div class="condition"><span class="icon"></span></div>\n\t\t\t\t\t\t</div>\n\t\t\t\t\t\t<div class="temp"></div>\n\t\t\t\t\t</a>\n\t\t\t\t</div>\t\t\n\t\t\t\t<div class="search-toggle tablet-desktop">\n\t\t\t\t\t<a href="#" class="js-focus-search">Expand / Collapse search</a>\n\t\t\t\t</div>\n\t\t\t\t<!-- show image when logged in -->\n\t\t\t\t<div class="network-access logged-out logged-in" id="account">\n\t\t\t\t\t<div class="user-profile user-login button">\n\t\t\t\t\t\t<a href="#" class="login" data-omtr-intcmp="hp1navlg">Login</a>\n\t\t\t\t\t</div>\n\t\t\t\t</div>\n\t\t\t\t<!-- user div switch -->\n\n\t\t\t\t<div class="button watch">\n\t\t\t\t\t<a href="http://video.foxnews.com/v/5614615980001/?#sp=watch-live">Watch TV</a>\n\t\t\t\t</div>\n\n\t\t\t\t<div class="menu mobile">\n\t\t\t\t\t<a href="#" class="js-menu-toggle">☰</a>\n\t\t\t\t</div>\n\t\t\t</div>\n\t\t</div>\n\t</div>\n \t\t\n\n\n<script type="text/javascript">\n\tvar navItems = \' https://s3.amazonaws.com/aurora-cms-static/mobile/prod/homepage-header.json\';\n\tvar jqxhr = $.getJSON( navItems, function(data) {\n\t\t$.each( data, function( i, item ) {\t        \n\t\t\tvar counter = 1;\n\t\t\t$.each( item.result.items, function( i, linkItem ) {\n\t\t\t\tconsole.log("linkItem.url=" + linkItem.url);\n\t\t\t\t$("#menu-subnav ul").append(\'<li><a href=\\"\' + linkItem.url + \'\\"\' + \' data-omtr-intcmp=subnav\' + counter+  \'"\\">\' + linkItem.text + \'</a></li>\');\n\t\t\t\tcounter ++ ;\n\t\t\t});\n\t\t});\n\t})\n</script>\n\n\n    \n   \t\n\t\t<div class="nav-row nav-row-lower tablet-desktop">\n\t\t\t<div class="inner">\n\t\t\t\t<div class="secondary-nav">\n\t\t\t\t\t<nav class="hot-topics menu-subnav" id="menu-subnav">\n\t\t\t\t\t\t<span class="subnav-title">Hot Topics</span>\n\t\t\t\t\t\t<ul class="rotate-items"></ul>\t\t\t\t\t\n\t\t\t\t\t</nav>\n\t\t\t\t</div>\n\t\t\t</div>\n\t\t</div>\t\t\n\t\n\n  \t\t\n\t\n\t<div class="expandable-nav">\n\t\t<div class="inner">\n\t\t\t<div class="search">\n\t\t\t\t<div class="search-wrap">\n\t\t\t\t\t<form action="http://www.foxnews.com/search-results/search" method="get">\n\t\t\t\t\t\t<fieldset>\n\t\t\t\t\t\t\t<input type="text" placeholder="Search foxnews.com" name="q" class="resp_site_search" onblur="" onfocus="" autocomplete="off">\n\t\t\t\t\t\t\t<input type="submit" class="resp_site_submit" value="Search">\n\t\t\t\t\t\t</fieldset>\n\t\t\t\t\t</form>\n\t\t\t\t</div>\n\t\t\t</div>\n\t\t</div>\n\t\t<div class="section-nav">\n\t\t\t<div class="inner">\n\t\t\t\t\t\t\t\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/us.html">U.S.</a>\n\t\t</h6>\n\t<ul>\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/crime.html">\t\t\tCrime\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/military.html">\t\t\tMilitary\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/education.html">\t\t\tEducation\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/terror.html">\t\t\tTerror\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/immigration.html">\t\t\tImmigration\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/economy.html">\t\t\tEconomy\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/personal-freedoms.html">\t\t\tPersonal Freedoms\t\t\t</a>\t\t</li>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/news-events/fox-news-investigates.html">\t\t\tFox News Investigates\t\t\t</a>\t\t</li>\n   \t\t\t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/world.html">World</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/united-nations.html">\t\t\tU.N.\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/conflicts.html">\t\t\tConflicts\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/terrorism.html">\t\t\tTerrorism\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/disasters.html">\t\t\tDisasters\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/global-economy.html">\t\t\tGlobal Economy\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/environment.html">\t\t\tEnvironment\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/world-religion.html">\t\t\tReligion\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/scandals.html">\t\t\tScandals\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\t\t\t\n\t\n<nav class="mobile">\n    <h6 class="nav-title">\n        <a href="//www.foxnews.com/opinion.html">Opinion</a>\n    </h6>\n</nav>\n\t\t\t\t\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/politics.html">Politics</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/executive.html">\t\t\tExecutive\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/senate.html">\t\t\tSenate\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/house.html">\t\t\tHouse \t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/judiciary.html">\t\t\tJudiciary\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/foreign-policy.html">\t\t\tForeign policy\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/official-polls/index.html">\t\t\tPolls\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/politics/elections.html">\t\t\tElections\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/entertainment.html">Entertainment</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/celebrity-news/index.html">\t\t\tCelebrity News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/movies/index.html">\t\t\tMovies\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/tv/index.html">\t\t\tTV News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/music/index.html">\t\t\tMusic News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/style/index.html">\t\t\tStyle News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/playlist/entertainment-latest-entertainment/">\t\t\tEntertainment Video\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxbusiness.com/">Business</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/markets.html">\t\t\tMarkets\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/politics.html">\t\t\tPolitics\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/category/technology.html">\t\t\tTechnology\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/features.html">\t\t\tFeatures\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/category/business-leaders.html">\t\t\tBusiness Leaders\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/lifestyle.html">Lifestyle</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/food-drink.html">\t\t\tFood + Drink\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/auto.html">\t\t\tCars + Trucks\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/travel.html">\t\t\tTravel + Outdoors\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/real-estate.html">\t\t\tHouse + Home\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/fitness-and-wellbeing.html">\t\t\tFitness + Well-being\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/style-and-beauty.html">\t\t\tStyle + Beauty\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/science.html">Science</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/archaeology.html">\t\t\tArchaeology\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/air-and-space.html">\t\t\tAir & Space\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/planet-earth.html">\t\t\tPlanet Earth\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/wild-nature.html">\t\t\tWild Nature\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/natural-science.html">\t\t\tNatural Science\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/archaeology/dinosaurs.html">\t\t\tDinosaurs\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/tech.html">Tech</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/security.html">\t\t\tSecurity\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/innovation.html">\t\t\tInnovation\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/technologies/drones.html">\t\t\tDrones\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/computers.html">\t\t\tComputers\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/video-games.html">\t\t\tVideo Games\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/military-tech.html">\t\t\tMilitary Tech\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/health.html">Health</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/healthy-living.html">\t\t\tHealthy Living\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/medical-research.html">\t\t\tMedical Research\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/mental-health.html">\t\t\tMental Health\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/cancer.html">\t\t\tCancer\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/heart-health.html">\t\t\tHeart Health\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/healthy-living/childrens-health.html">\t\t\tChildren\'s Health\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/shows.html">TV</a>\n\t\t</h6>\n\t<ul>\n    \t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/shows.html">\t\t\tShows\t\t\t</a>\t\t</li>\n\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/person/personalities.html">\t\t\tPersonalities\t\t\t</a>\t\t</li>\n\t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/v/5614615980001/?#sp=watch-live">\t\t\tWatch Live\t\t\t</a>\t\t</li>\n\t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/playlist/episodic-most-recent-episodes/">\t\t\tFull Episodes\t\t\t</a>\t\t</li> \n\t    \t\t\n   \t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/#sp=show-clips">\t\t\tShow Clips\t\t\t</a>\t\t</li>\n   \t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/#sp=news-clips">\t\t\tNews Clips\t\t\t</a>\t\t</li>\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\tAbout\t\t</h6>\n\t<ul>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//careers.foxnews.com/">\t\t\tCareers\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//careers.foxnews.com/students">\t\t\tCollege Students\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/foxaroundtheworld/">\t\t\tFox Around the World\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="mailto:adsales@foxnews.com?subject=Advertising%20Inquiry">\t\t\tAdvertise With Us\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/about/privacy-policy/#ad-choice">\t\t\tAd Choices\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="mailto:newsmanager@foxnews.com">\t\t\tEmail Newsroom\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//press.foxnews.com/">\t\t\tMedia Relations\t\t\t</a>\t\t</li>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/compliance">\t\t\tCompliance\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\tOther\t\t</h6>\n\t<ul>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//insider.foxnews.com">\t\t\tFox News Insider\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/radio/">\t\t\tFox News Radio\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//nation.foxnews.com">\t\t\tFox Nation\t\t\t</a>\t\t</li>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnewsgo.com/">\t\t\tFox News Go\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/alerts/subscribe.html">\t\t\tNewsletters\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/newsletter-signup/alerts">\t\t\tAlerts\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/podcasts/">\t\t\tPodcasts\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/about/apps-downloads">\t\t\tApps & Products\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n\t\t\t</div>\n\t\t</div>\n\t\t\n\t\t<div class="header-lower">\t\t\t\n\t\t\t <div class="social-icons">\n\t\t\t      <ul>\n\t\t\t         <li class="fb"><a href="//www.facebook.com/FoxNews">Facebook</a></li>\n\t\t\t         <li class="tw"><a href="//twitter.com/foxnews">Twitter</a></li>\n\t\t\t         <li class="gp"><a href="//www.google.com/+FoxNews">Google+</a></li>\n\t\t\t         <li class="ig"><a href="//www.instagram.com/foxnews">Instagram</a></li>\n\t\t\t         <li class="rss"><a href="//www.foxnews.com/rss/index.html">RSS</a></li>\n\t\t\t         <li class="email"><a href="//www.foxnews.com/alerts/subscribe.html">Email</a></li>\n\t\t\t      </ul>\n\t\t    </div>\n\t\t    <div class="branding"><a class="logo" href="//www.foxnews.com">Fox News</a></div>\n\t\t   <div class="legal">\n\t\t      <ul>\n\t\t         <li><a href="//www.foxnews.com/about/terms-of-use">Terms of Use</a></li>\n\t\t         <li><a href="//www.foxnews.com/about/privacy-policy">Privacy Policy</a></li>\n\t\t         <li><a href="//www.foxnews.com/closed-captioning">Closed Captioning Policy</a></li>\n\t\t         <li><a href="//help.foxnews.com/index.html">Help</a></li>\n\t\t      </ul>\n\t\t      <p class="copyright">This material may not be published, broadcast, rewritten, or redistributed. &copy;2018 FOX News Network, LLC. All rights reserved. All market data delayed 20 minutes.</p>\n\t\t   </div>\t  \n\t\t</div>\n\t</div>\n\t\n        \n    \n\n</header>\n \t\n\t\t\n\n\n\n\n<div class="page">\n\t<div class="pre-content">\n \t\t\n\n\t\t\n\t\t\n\n\n \t\n    \n    \n        \n    \t\t\n\t    \t\t\n\t    \t\t\n\t\t    \t\t\n\t\t\t\t    \n\t\t\t\t\t\n\t\t\t\t\t\n\t\t\t\t\t\t<div class="ad-container desktop">\n\t\t\t\t\t\t  <div class="ad gpt ad-h-66" data-ad-pos="desktop" data-ad-size="970x66,970x250,728x90" ></div>\n\t\t\t\t\t\t  \n\t\t\t\t\t\t</div>\n\t\t\t\t\t\n\t\t\t\t\t\n\t\t    \t\n\t\t    \n\t\n\n\n\t\t\r\n\n \t</div>\n\t\t\n\t<div class="page-content">\n\t\n\t\t <div class="row full">\n\t\t\t<main class="main-content">\n\t\t\n\t\t\t\t<article class="article-wrap has-video">\n\t\t\n\t\t\t\t\t\n\n\n\n<header class="article-header">\n\n\n\t\n\t\n\t\t<div class="article-meta article-meta-upper">\n\t\n\t\t\t\n\t            \n\t            \n\t            \n\t            \n\t            \n\t                \n\t            \n\t        \n\t\t\t\n\t\t\t    <div class="eyebrow"><a href="/category/tech/companies/facebook.html">Facebook</a></div>\n\t\t\t\t\t\n\t\t    \n\t\t\t\t<div class="article-date">\t\t\t\t\t\n\t\t\t\t\t<time data-time-published="2018-07-31T19:15:00Z-0400"></time>\n\t\t\t\t</div>\n\t\t\t\t\n\t\t\t\n\t\t\t\n\t\t</div>\n        \n\t\t\n\t\t<h1 class="headline head1">Facebook finds &#x27;sophisticated&#x27; efforts to disrupt US politics, removes 32 accounts</h1>\n\t\t\n\t\t\n\t\t\n\t\t\n\t\t\t<div class="article-meta article-meta-lower">\n\t\t\t\t<div class="author-byline">\n\t\t\t\t\t\n\t\t\t\t\t\t<div class="author-headshot">\n\t\t\t\t\t\t\t<div class="m">\n\t\t\t\t\t\t\t\t<img alt="Christopher Carbone" src="http://a57.foxnews.com/images.foxnews.com/content/fox-news/person/c/christopher-carbone/_jcr_content/image.img.jpg/48/48/1531046309532.jpg">\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t<span> By\n\t\t\t\t\t\t\t\t\t \n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\t<a href="/person/c/christopher-carbone.html" rel="author">\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\tChristopher Carbone\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\t</a>\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t \t\t\t\t\t           \n\t\t\t\t\t\t             <span class="article-source">\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\t\t<a target=\'_blank\' href="http://www.foxnews.com/"> | Fox News</a>\n\t\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t</span>\t\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t</span>\t\n\t\t\t\t\t\t\n\t\t\t\t\t\t\n\t\t\t\t\t\t\n\t\t\t\t</div>\n\t\t\t\t\n\t\t\t\t<div class="article-social">\n\t\t\t\t\t<div class="social-icons">\n\t\t\t\t\t\t<ul>\n\t\t\t\t\t\t\t<li class="fb"><a href="#">Facebook</a></li>\n\t\t\t\t\t\t\t<li class="tw"><a href="#">Twitter</a></li>\n\t\t\t\t\t\t\t<li class="flipboard"><a href="#">Flipboard</a></li>\n\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t<li class="comments"><a href="#"><span>Comments</span></a></li>\n\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t<li class="print"><a href="/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.print.html">Print</a></li>\n\t\t\t\t\t\t\t<li class="email"><a href="#">Email</a></li>\n\t\t\t\t\t\t</ul>\n\t\t\t\t\t</div>\n\t\t\t\t</div>\t\t\t\t\n\t\t\t</div>\t\t\t\n\t\t\n\t\n</header> \n\n\t\t\t\t\t\n\t\t\t\t\t<div class="article-content-wrap sticky-columns">\n\t\t\n\t\t\t\t\t\t<div class="article-content">\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t<div class="article-body">\n\t\t\t\t\t\t\t\t\n\n\n\n\n\n    \n    \n\t\n\t\t\n\t\t\t\n\t\t\t\n              <div class="featured featured-video video-ct">\n\t              <div class="contain">\n\t\t              \t<div class="control">\n\t\t\t\t\t\t\t<a class="top" href="#"></a>\n\t\t\t\t\t\t\t<a class="close" href="#">close</a>\n\t\t\t\t\t\t</div>\n\t                    <div class="video-container">\n\t                        <div class="m video-player" data-widget-type="embed" data-video-id="5816244794001" data-video-domain="foxnews">\n\t                            <a href="http://video.foxnews.com/v/5816244794001/facebook-detects-fake-accounts-trying-to-interfere-with-midterm-elections" class="hide">\n\t                                <img src="http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/896/504/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1" alt="Facebook discovered a new ongoing political campaign reportedly aimed to influence the midterm elections. The company removed dozens of fake accounts and cites Russia could be behind the fraudulent pages."/>\n\t                                <span class="overlay">Video</span>\n\t                            </a>\n\t                        </div>\n\t                   </div>\n\t                   <div class="info">\n\t                            <div class="caption">\n\t\t                            <h4 class="title"><a href="http://video.foxnews.com/v/5816244794001/facebook-detects-fake-accounts-trying-to-interfere-with-midterm-elections">Facebook detects fake accounts trying to interfere with midterm elections</a></h4>\n\t\t                                <p>Facebook discovered a new ongoing political campaign reportedly aimed to influence the midterm elections. The company removed dozens of fake accounts and cites Russia could be behind the fraudulent pages.</p>\n\t                        \t</div>\n\t\t\t\t\t </div>\n\t\t\t\t  </div>\n              </div>\n            \n\t\t \n\n    \n\n\n\n\n\r\n\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\n\n\n\n \n\n\n\n       \n       \n      \n          \n              <p class="speakable">Facebook uncovered &quot;sophisticated&quot; efforts, possibly linked to Russia, to influence American politics in advance of the U.S. midterm elections.</p>\n              \n                  <div class="ad-container tablet">                      \n                      <div class="ad gpt ad-h-250" data-ad-pos="tablet" data-ad-size="300x250"></div>\n                  </div>\n                  <div class="ad-container mobile">                      \n                      <div class="ad gpt ad-h-250" data-ad-pos="mobile" data-ad-size="300x250"></div>\n                  </div>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p class="speakable">The company said in a blog post that it removed 32 accounts from Facebook and Instagram because they were involved in &quot;coordinated&quot; political behavior and appeared to be fake.</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p><a href="http://www.foxnews.com/category/tech/companies/facebook.html" target="_blank">Facebook</a> did not explicitly say that the effort was aimed at influencing the midterm elections in November, but the timing of the suspicious activity would be consistent with such an attempt.</p>\n              \n             \n                           \t\t\n\t\t\t\t\t<div class="ad gpt ad-h-1" data-ad-pos="inread" data-ad-size="1x1"></div>\n              \n              \n              \n              \r\n\n\n              \n          \n              <p>The company, which said it is in the early stages of its investigation, held briefings in the House and Senate this week.</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>The company <a href="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html" target="_blank">said</a> it doesn\'t know who is behind the efforts, but said there may be connections to Russia. Facebook said it has found some connections between the accounts it removed and the accounts connected to Russia\'s Internet Research Agency that it removed before and after the 2016 U.S. presidential elections.</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p><b><a href="http://www.foxnews.com/tech/2018/07/31/twitter-brings-in-anti-trump-academics-to-combat-bias.html" target="_blank">TWITTER BRINGS IN ANTI-TRUMP ACADEMICS TO FIGHT BIAS</a></b></p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n                  <div class="ad-container mobile">                      \n                      <div class="ad gpt ad-h-250" data-ad-pos="mobile" data-ad-size="300x251"></div>\n                  </div>\n              \n          \n              <p>U.S. Senator Mark Warner, D-Va., who introduced the Honest Ads Act earlier this year to help prevent foreign interference in U.S. elections, praised Facebook\'s elections.</p>\n              \n             \n               \n              \n              \n              \n\n\n\n\n\n\t\n    \n    \n    \n\t    <div class="inline  image-ct">\n\t    \t\t\n\t\t\t\t<div class="m">\n\t\t\t\t\t \n\t\t                \n\t\t\t\t\t\t\n\t\t\t\t\t\t\t<img src="http://a57.foxnews.com/images.foxnews.com/content/fox-news/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts/_jcr_content/article-text/article-par-7/inline_spotlight_ima/image.img.jpg/612/344/1533059700388.jpg?ve=1&tl=1" alt="facebook bad actors">\n\t\t\t\t\t\t\n\t\t\t\t\t \n\t\t\t\t</div>\t\n\t\t\t\n\t\t\t\n\t\t\t \t        \n\t\t        \t\n\t\t       \t\n\t\t\t       \t\n\t\t\t       \t\t<div class="caption">\n\t\t\t\t\t\t\t<p>Two of the pages that the tech giant recently disabled are seen above. \n\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t&nbsp;<span class="copyright">(Facebook)</span>\n\t\t\t\t\t\t\t\t\n\t\t\t\t\t        </p>\n\t\t\t\t       \t</div>\n\t\t\t\t    \n\t\t       \t\n\t        \n\t\t</div>  \n\t\n\r\n\n\n              \n          \n              <p>“Today’s disclosure is further evidence that the Kremlin continues to exploit platforms like Facebook to sow division and spread disinformation, and I am glad that Facebook is taking some steps to pinpoint and address this activity,&quot; Warner said in a statement to Fox News.&nbsp;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>&quot;I also expect Facebook, along with other platform companies, will continue to identify Russian troll activity and to work with Congress on updating our laws to better protect our democracy in the future,” he continued.&nbsp; &nbsp;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>&quot;It’s clear that whoever set up these accounts went to much greater lengths to obscure their true identities than the Russian-based Internet Research Agency (IRA) has in the past,&quot; Facebook said in its statement. &quot;We believe this could be partly due to changes we’ve made over the last year to make this kind of abuse much harder.&quot;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p><b><a href="http://www.foxnews.com/tech/2018/07/30/facebook-tech-giants-creating-crisis-in-democracy-and-must-be-held-liable-uk-report-says.html" target="_blank">FACEBOOK, TECH GIANTS CREATING \'CRISIS IN DEMOCRACY,\' UK REPORT SAYS</a></b></p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>&quot;The goal of these operations is to sow discord, distrust, and division in an attempt to undermine public faith in our institutions and our political system,&quot; Sen. Richard Burr, R-N.C., said in a statement. &quot;The Russians want a weak America.&nbsp; There is still much that needs to be done to prevent and counter foreign interference on social media.&quot;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>The president has made it clear that his administration will not tolerate foreign interference into our electoral process from any nation state or other malicious actors,&quot; White House Deputy Press Secretary Hogan Gidley said in a statement late Tuesday.</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>The earliest page was created in March 2017. Facebook says more than 290,000 accounts followed at least one of the fake pages. The most followed Facebook Pages had names such as &quot;Aztlan Warriors,&quot; \'\'Black Elevation,&quot; \'\'Mindful Being,&quot; and &quot;Resisters.&quot;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>Facebook says the pages ran about 150 ads for $11,000 on Facebook and Instagram, paid for in U.S. and Canadian dollars. The first ad was created in April 2017; the last was created in June 2018.</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>The perpetrators used virtual private networks and internet phone services, and paid third parties to run ads on their behalf, according to Facebook.</p>\n              \n             \n               \n              \n              \n              \n\n\n\n\n\n\t\n    \n    \n    \n\t    <div class="inline  image-ct">\n\t    \t\t\n\t\t\t\t<div class="m">\n\t\t\t\t\t \n\t\t                \n\t\t\t\t\t\t\n\t\t\t\t\t\t\t<img src="http://a57.foxnews.com/images.foxnews.com/content/fox-news/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts/_jcr_content/article-text/article-par-16/inline_spotlight_ima/image.img.jpg/612/344/1533060441947.jpg?ve=1&tl=1" alt="A figurine is seen in front of the Facebook logo in this illustration taken, March 20, 2018. REUTERS/Dado Ruvic - RC155C02C7D0">\n\t\t\t\t\t\t\n\t\t\t\t\t \n\t\t\t\t</div>\t\n\t\t\t\n\t\t\t\n\t\t\t \t        \n\t\t        \t\n\t\t       \t\n\t\t\t       \t\n\t\t\t       \t\t<div class="caption">\n\t\t\t\t\t\t\t<p>\n\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t&nbsp;<span class="copyright">(Reuters)</span>\n\t\t\t\t\t\t\t\t\n\t\t\t\t\t        </p>\n\t\t\t\t       \t</div>\n\t\t\t\t    \n\t\t       \t\n\t        \n\t\t</div>  \n\t\n\r\n\n\n              \n          \n              <p>In addition, Facebook revealed that its partnership with the Atlantic Council helped it to identify the bad actors. One of the groups with roughly 4,000 members was located based on leads from U.S. Special Counsel Robert Mueller\'s recent <a href="https://www.justice.gov/opa/pr/grand-jury-indicts-12-russian-intelligence-officers-hacking-offenses-related-2016-election" target="_blank">indictment</a> of 12 Russian nationals for their role in hacking and disinformation efforts during the 2016 U.S. presidential election.&nbsp;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>That group was created by Russian government figures but had been dormant since Facebook disabled its administrators last year. However, the tech company chose to remove the group to protect the privacy of its members in advance of a forthcoming report from the Atlantic Council that will analyze the Pages, profiles and accounts that Facebook disabled today.&nbsp;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>Freedom From Facebook, a group that has been pushing for the tech company to be broken up, said that today\'s announcement was not enough.&nbsp;</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>“While it is good news that this latest attack on democracy via Facebook was discovered early, the fact that it happened highlights the danger posed when so much power is concentrated in a single company,&quot; said Sarah Miller, a campaigner with Freedom From Facebook.</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p>Miller continued: “We will not be safe from foreign interference -- and Facebook’s own business model of profiting off of bad actors -- until Congress and the FTC step in to break up the company and impose strong privacy rules.”</p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n              <p><i>Fox News\' Bree Tracey and The Associated Press contributed to this report.&nbsp;</i></p>\n              \n             \n               \n              \n              \n              \r\n\n\n              \n          \n  \n          \n              \n              \n\n\n \n\n\t\n\t\t<div class="author-bio">\n\t\t\t<p>Christopher Carbone is a reporter and news editor covering science and technology for FoxNews.com. He can be reached at christopher.carbone@foxnews.com. Follow him on Twitter <a href="https://twitter.com/christocarbone" target="_blank">@christocarbone</a>.</p>\n\n\t\t</div>\n\t\n\n          \n      \n \n\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t<div class="article-meta">\n\t\t\t\t\t\t\t\t\n\n\n\n\n \n     \n     \n \n\n\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t<footer class="article-footer">\t\n\t\t\t\t\t\t\t   \t\t\t\t\t\n\t\t\t\t\t\t\t\t\n\n\n\n\n\t\t                    \t\n\n\n\n\n\n\n\n\t\t\t\t\t\t\t\t\r\n \n\t\t\t\t\t            \n\t\t\t\t\t\t            \t\n\n\n\n<div class="vendor-unit vendor-outbrain tablet-desktop"><div class="fox-ob-placement img-strip" data-article-placement="5" data-fbk="1" data-widget-id="AR_16"></div></div><!-- /outbrain -->\n<div class="vendor-unit vendor-outbrain tablet-desktop"><div class="fox-ob-placement" data-article-placement="1" data-fbk="1" data-widget-id="AR_9"></div></div><!-- /outbrain -->\n\n<div class="vendor-unit vendor-outbrain mobile">\n    <div class="OUTBRAIN" data-widget-id="MB_3"></div>\n</div>\n\n<div class="vendor-unit vendor-yieldmo">\n  <div class="ad-container mobile-tablet yieldmo">\t\t\t\t\t\t\n    <div class="ad cu" id="cu_article_4"></div>\n  </div>\n</div>\n\n<div class="vendor-unit vendor-yieldmo">\n\t<div class="ad-container yieldmo" id="cu_article_3"></div>\n</div>\n\n\n\n\n\n\n    <div id="commenting"  class="article-comments"><div id="livefyre_comment_stream"></div></div>\n\n\t\t\t           \t\t\t\n\t\t\t\t\t\t\t</footer>\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t</div>\t\t\t\t\t\t\n\t\t\t\t\t\t<aside class="sidebar sidebar-article sidebar-primary desktop">\n\t\t\t\t\t\t\t<div class="sticky-region">\n\t\t\t\t\t\t\t\t<div class="sidebar-columns">\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t\t\t<div class="ad section">\n\n\t\t\n\t\t\n\n\n \t\n    \n    \n        \n    \t\t\n\t    \t\t\n\t    \t\t\n\t\t    \t\t\n\t\t\t\t    \n\t\t\t\t\t\n\t\t\t\t\t\n\t\t\t\t\t\t<div class="ad-container desktop">\n\t\t\t\t\t\t  <div class="ad gpt ad-h-250" data-ad-pos="desktop" data-ad-size="300x600,300x250" ></div>\n\t\t\t\t\t\t  \n\t\t\t\t\t\t</div>\n\t\t\t\t\t\n\t\t\t\t\t\n\t\t    \t\n\t\t    \n\t\n\n</div>\n\n\n    \n    \n    \n    <div class="embed-media advanced">\n<div class="vendor-unit vendor-outbrain desktop">\n<div class="OUTBRAIN" data-src="" data-widget-id="AR_26" data-ob-template="foxnews" data-article-placement="9"></div>\n</div>\n\n<div class="vendor-unit vendor-outbrain desktop">\n<div class="OUTBRAIN" data-src="" data-widget-id="AR_14" data-ob-template="foxnews" data-article-placement="4"></div>\n</div></div>        \n    \n\n\n\n\n\n\n\n\n    \n    \n    \n\t\t<section class="collection collection-trending">\n       \t \t<header class="heading">\n            \t\t<h4 class="title">Trending in Tech</h4>\n            </header>\n            <div class="content">\n\t         \t<ul>    \n\t         \t\n\t            \t\t \n\t            \t\t \t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t\t \t\t\t \n\t\t\t\t      <li>\n\t\t\t\t\t      \t<article class="article story-">\n\t\t\t\t\t\t\t<div class="m">\n\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html">\n\t\t\t\t\t\t\t\t\t<picture>\n\t\t\t\t\t\t\t\t\t\t<source media="(max-width: 767px)" srcset="http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/160/90/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1, http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/320/180/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 767px) and (max-width: 1023px)" srcset="http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/224/127/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1, http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/448/252/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 1024px)" srcset="http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/270/152/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1">\n\t\t\t\t\t\t\t\t\t\t<img src="http://a57.foxnews.com/media2.foxnews.com/BrightCove/694940094001/2018/07/31/270/152/694940094001_5816244313001_5816244794001-vs.jpg?ve=1&tl=1" alt="Facebook&#x20;said&#x20;it&#x20;has&#x20;uncovered&#x20;&quot;sophisticated&quot;&#x20;efforts,&#x20;possibly&#x20;linked&#x20;to&#x20;Russia,&#x20;to&#x20;influence&#x20;U.S.&#x20;politics&#x20;on&#x20;its&#x20;platforms..">\n\t\t\t\t\t\t\t\t\t</picture>\n\t\t\t\t\t\t\t\t</a>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t<div class="info">\n\t\t\t\t\t\t\t\t<h5 class="title">\n\t\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html">Facebook finds \'sophisticated\' efforts to disrupt US politics, removes 32 accounts</a>\n\t\t\t\t\t\t\t\t</h5>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t</article>\t   \n\t\t\t\t      </li>\n\t\t\t\t\n\t            \t\t \n\t            \t\t \t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t\t \t\t\t \n\t\t\t\t      <li>\n\t\t\t\t\t      \t<article class="article story-">\n\t\t\t\t\t\t\t<div class="m">\n\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/twitter-brings-in-anti-trump-academics-to-combat-intolerance.html">\n\t\t\t\t\t\t\t\t\t<picture>\n\t\t\t\t\t\t\t\t\t\t<source media="(max-width: 767px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2018/07/26/160/90/TrumpTwitter.jpg?ve=1&tl=1, http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2018/07/26/320/180/TrumpTwitter.jpg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 767px) and (max-width: 1023px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2018/07/26/224/127/TrumpTwitter.jpg?ve=1&tl=1, http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2018/07/26/448/252/TrumpTwitter.jpg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 1024px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2018/07/26/270/152/TrumpTwitter.jpg?ve=1&tl=1">\n\t\t\t\t\t\t\t\t\t\t<img src="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2018/07/26/270/152/TrumpTwitter.jpg?ve=1&tl=1" alt="Twitter&rsquo;s&#x20;campaign&#x20;to&#x20;foster&#x20;healthier&#x20;conversations&#x20;on&#x20;its&#x20;platform&#x20;with&#x20;the&#x20;aid&#x20;of&#x20;academics&#x20;is&#x20;itself&#x20;facing&#x20;an&#x20;allegation&#x20;of&#x20;anti-Trump&#x20;bias.">\n\t\t\t\t\t\t\t\t\t</picture>\n\t\t\t\t\t\t\t\t</a>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t<div class="info">\n\t\t\t\t\t\t\t\t<h5 class="title">\n\t\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/twitter-brings-in-anti-trump-academics-to-combat-intolerance.html">Twitter brings in anti-Trump academics to combat intolerance</a>\n\t\t\t\t\t\t\t\t</h5>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t</article>\t   \n\t\t\t\t      </li>\n\t\t\t\t\n\t            \t\t \n\t            \t\t \t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t\t \t\t\t \n\t\t\t\t      <li>\n\t\t\t\t\t      \t<article class="article story-">\n\t\t\t\t\t\t\t<div class="m">\n\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/jeff-bezos-parents-invested-245k-could-be-mindbogglingly-rich.html">\n\t\t\t\t\t\t\t\t\t<picture>\n\t\t\t\t\t\t\t\t\t\t<source media="(max-width: 767px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/feed/newser/2018/07/31/160/90/1191963-0-20180731140858.jpeg.jpeg?ve=1&tl=1, http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/feed/newser/2018/07/31/320/180/1191963-0-20180731140858.jpeg.jpeg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 767px) and (max-width: 1023px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/feed/newser/2018/07/31/224/127/1191963-0-20180731140858.jpeg.jpeg?ve=1&tl=1, http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/feed/newser/2018/07/31/448/252/1191963-0-20180731140858.jpeg.jpeg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 1024px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/feed/newser/2018/07/31/270/152/1191963-0-20180731140858.jpeg.jpeg?ve=1&tl=1">\n\t\t\t\t\t\t\t\t\t\t<img src="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/feed/newser/2018/07/31/270/152/1191963-0-20180731140858.jpeg.jpeg?ve=1&tl=1" alt="Some&#x20;parents&#x20;buy&#x20;extra&#x20;boxes&#x20;of&#x20;Girl&#x20;Scout&#x20;cookies&#x20;to&#x20;support&#x20;their&#x20;kiddos.">\n\t\t\t\t\t\t\t\t\t</picture>\n\t\t\t\t\t\t\t\t</a>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t<div class="info">\n\t\t\t\t\t\t\t\t<h5 class="title">\n\t\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/jeff-bezos-parents-invested-245k-could-be-mindbogglingly-rich.html">Jeff Bezos\' parents invested $245K, could be mindbogglingly rich</a>\n\t\t\t\t\t\t\t\t</h5>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t</article>\t   \n\t\t\t\t      </li>\n\t\t\t\t\n\t            \t\t \n\t            \t\t \t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t            \t\t \n\t\t \t\t\t \n\t\t\t\t      <li>\n\t\t\t\t\t      \t<article class="article story-">\n\t\t\t\t\t\t\t<div class="m">\n\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/hp-offers-10k-reward-for-hacking-its-printers.html">\n\t\t\t\t\t\t\t\t\t<picture>\n\t\t\t\t\t\t\t\t\t\t<source media="(max-width: 767px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2015/11/17/160/90/password-11.jpg?ve=1&tl=1, http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2015/11/17/320/180/password-11.jpg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 767px) and (max-width: 1023px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2015/11/17/224/127/password-11.jpg?ve=1&tl=1, http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2015/11/17/448/252/password-11.jpg?ve=1&tl=1 2x">\n\t\t\t\t\t\t\t\t\t\t<source media="(min-width: 1024px)" srcset="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2015/11/17/270/152/password-11.jpg?ve=1&tl=1">\n\t\t\t\t\t\t\t\t\t\t<img src="http://a57.foxnews.com/www.foxnews.com/content/dam/fox-news/images/2015/11/17/270/152/password-11.jpg?ve=1&tl=1" alt="Any&#x20;connected&#x20;device&#x20;is&#x20;a&#x20;potential&#x20;security&#x20;threat&#x20;for&#x20;businesses&#x20;and&#x20;individuals&#x20;alike,&#x20;and&#x20;that&#x27;s&#x20;definitely&#x20;the&#x20;case&#x20;for&#x20;printers&#x20;which&#x20;are&#x20;both&#x20;connected&#x20;and&#x20;regularly&#x20;used&#x20;to&#x20;produce&#x20;potentially&#x20;sensitive&#x20;information.">\n\t\t\t\t\t\t\t\t\t</picture>\n\t\t\t\t\t\t\t\t</a>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t<div class="info">\n\t\t\t\t\t\t\t\t<h5 class="title">\n\t\t\t\t\t\t\t\t\t<a href="http://www.foxnews.com/tech/2018/07/31/hp-offers-10k-reward-for-hacking-its-printers.html">HP offers $10K reward for hacking its printers</a>\n\t\t\t\t\t\t\t\t</h5>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t\t</article>\t   \n\t\t\t\t      </li>\n\t\t\t\t\n\t\t\t\t</ul>\n\t\t\t</div>\n\t\t\t\n\t\t</section>\n    \n\n\n\t\t\n\t\t\n\n\n \t\n    \n    \n        \n    \t\t\n\t    \t\t\n\t    \t\t\n\t\t    \t\t\n\t\t\t\t\t\t<div class="mobile-desktop">\n\t\t\t\t\t\n\t\t\t\t    \n\t\t\t\t\t\t<div class="ad-container mobile">\n\t\t\t\t\t\t  <div class="ad gpt ad-h-250" data-ad-pos="mobile" data-ad-size="300x251" ></div>\n\t\t\t\t\t\t  \n\t\t\t\t\t\t</div>\n\t\t\t\t\t\n\t\t\t\t\t\n\t\t\t\t\t\n\t\t\t\t\t\t<div class="ad-container desktop">\n\t\t\t\t\t\t  <div class="ad gpt ad-h-250" data-ad-pos="desktop" data-ad-size="300x251" ></div>\n\t\t\t\t\t\t  \n\t\t\t\t\t\t</div>\n\t\t\t\t\t\n\t\t\t\t\t\n\t\t\t\t\t\t</div>\n\t\t\t\t    \n\t\t    \t\n\t\t    \n\t\n\n\n\n\t\t\n\t\t\n\n\n \t\n    \n    \n        \n    \t\t\n\t    \t\t\n\t    \t\t\t<div class="ad-container htu-container">\n\t\t\t\t\t  <div class="ad gpt ad-h-2" data-ad-pos="htu" data-ad-size="1x2" ></div>\n\t\t\t\t\t  \n\t\t\t\t\t</div>\n\t    \t\t\n\t    \t\t\n\t\t    \n\t\n\n\n\n    \n    \n    \n    <div class="embed-media advanced"><div class="vendor-unit vendor-outbrain desktop">\n<div class="OUTBRAIN" data-src="" data-widget-id="AR_22" data-ob-template="foxnews" data-article-placement="9"></div>\n</div></div>        \n    \n\n\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\n\t\t\t\t\t\t\t    </div>\n\t\t\t\t\t\t\t</div>\n\t\t\t\t\t\t</aside>\t\t\t\t\t\t\t\t\t\t\t\t\n\t\t             </div>\t\n\t\t             \t\t\t\t\t\t\t\n\t\t\t\t</article>\n\t\t\t</main>\t\n\t\t</div>\n\t</div>\n\t<div class="post-content">\n\t\t\r\n\n\t</div>\n</div>\n\n\n\n\n\n\t   \t\n\n\n\n    \n       \t\n  \t\t\n\n<footer class="site-footer">\n\t<div class="footer-upper section-nav">\n\t\t<div class="inner">\n\t\t\t\t\t\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/us.html">U.S.</a>\n\t\t</h6>\n\t<ul>\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/crime.html">\t\t\tCrime\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/military.html">\t\t\tMilitary\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/education.html">\t\t\tEducation\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/terror.html">\t\t\tTerror\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/immigration.html">\t\t\tImmigration\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/economy.html">\t\t\tEconomy\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/us/personal-freedoms.html">\t\t\tPersonal Freedoms\t\t\t</a>\t\t</li>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/news-events/fox-news-investigates.html">\t\t\tFox News Investigates\t\t\t</a>\t\t</li>\n   \t\t\t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/world.html">World</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/united-nations.html">\t\t\tU.N.\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/conflicts.html">\t\t\tConflicts\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/terrorism.html">\t\t\tTerrorism\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/disasters.html">\t\t\tDisasters\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/global-economy.html">\t\t\tGlobal Economy\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/environment.html">\t\t\tEnvironment\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/world-religion.html">\t\t\tReligion\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/world/scandals.html">\t\t\tScandals\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\t\t\t\n<nav class="mobile">\n    <h6 class="nav-title">\n        <a href="//www.foxnews.com/opinion.html">Opinion</a>\n    </h6>\n</nav>\t\t\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/politics.html">Politics</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/executive.html">\t\t\tExecutive\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/senate.html">\t\t\tSenate\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/house.html">\t\t\tHouse\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/judiciary.html">\t\t\tJudiciary\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/politics/foreign-policy.html">\t\t\tForeign policy\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/official-polls/index.html">\t\t\tPolls\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/politics/elections.html">\t\t\tElections\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/entertainment.html">Entertainment</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/celebrity-news/index.html">\t\t\tCelebrity News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/movies/index.html">\t\t\tMovies\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/tv/index.html">\t\t\tTV News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/music/index.html">\t\t\tMusic News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/entertainment/style/index.html">\t\t\tStyle News\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/playlist/entertainment-latest-entertainment/">\t\t\tEntertainment Video\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxbusiness.com/">Business</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/markets.html">\t\t\tMarkets\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/politics.html">\t\t\tPolitics\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/category/technology.html">\t\t\tTechnology\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/features.html">\t\t\tFeatures\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxbusiness.com/category/business-leaders.html">\t\t\tBusiness Leaders\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/lifestyle.html">Lifestyle</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/food-drink.html">\t\t\tFood + Drink\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/auto.html">\t\t\tCars + Trucks\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/travel.html">\t\t\tTravel + Outdoors\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/real-estate.html">\t\t\tHouse + Home\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/fitness-and-wellbeing.html">\t\t\tFitness + Well-being\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/style-and-beauty.html">\t\t\tStyle + Beauty\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/science.html">Science</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/archaeology.html">\t\t\tArchaeology\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/air-and-space.html">\t\t\tAir & Space\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/planet-earth.html">\t\t\tPlanet Earth\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/wild-nature.html">\t\t\tWild Nature\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/natural-science.html">\t\t\tNatural Science\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/science/archaeology/dinosaurs.html">\t\t\tDinosaurs\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/tech.html">Tech</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/security.html">\t\t\tSecurity\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/innovation.html">\t\t\tInnovation\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/technologies/drones.html">\t\t\tDrones\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/computers.html">\t\t\tComputers\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/video-games.html">\t\t\tVideo Games\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/tech/topics/military-tech.html">\t\t\tMilitary Tech\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/health.html">Health</a>\n\t\t</h6>\n\t<ul>\n    \n\t    \n\t\n\t    \t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/healthy-living.html">\t\t\tHealthy Living\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/medical-research.html">\t\t\tMedical Research\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/mental-health.html">\t\t\tMental Health\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/cancer.html">\t\t\tCancer\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/heart-health.html">\t\t\tHeart Health\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/category/health/healthy-living/childrens-health.html">\t\t\tChildren\'s Health\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\t<a href="//www.foxnews.com/shows.html">TV</a>\n\t\t</h6>\n\t<ul>\n    \t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/shows.html">\t\t\tShows\t\t\t</a>\t\t</li>\n\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/person/personalities.html">\t\t\tPersonalities\t\t\t</a>\t\t</li>\n\t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/v/5614615980001/?#sp=watch-live">\t\t\tWatch Live\t\t\t</a>\t\t</li>\n\t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/playlist/episodic-most-recent-episodes/">\t\t\tFull Episodes\t\t\t</a>\t\t</li> \n\t    \t\t\n   \t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/#sp=show-clips">\t\t\tShow Clips\t\t\t</a>\t\t</li>\n   \t\t\t<li class="nav-item">\n\t\t\t<a href="//video.foxnews.com/#sp=news-clips">\t\t\tNews Clips\t\t\t</a>\t\t</li>\n\t\t</ul>\n</nav>\n\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\tAbout\t\t</h6>\n\t<ul>\n\t    \t\t\t<li class="nav-item">\n\t\t\t<a href="//careers.foxnews.com/">\t\t\tCareers\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//careers.foxnews.com/students">\t\t\tCollege Students\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/foxaroundtheworld/">\t\t\tFox Around the World\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="mailto:adsales@foxnews.com?subject=Advertising%20Inquiry">\t\t\tAdvertise With Us\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/about/privacy-policy/#ad-choice">\t\t\tAd Choices\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="mailto:newsmanager@foxnews.com">\t\t\tEmail Newsroom\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//press.foxnews.com/">\t\t\tMedia Relations\t\t\t</a>\t\t</li>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/compliance">\t\t\tCompliance\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\n\n<nav>\n\t<h6 class="nav-title">\n\t\t\tOther\t\t</h6>\n\t<ul>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//insider.foxnews.com">\t\t\tFox News Insider\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/radio/">\t\t\tFox News Radio\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//nation.foxnews.com">\t\t\tFox Nation\t\t\t</a>\t\t</li>\n\t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnewsgo.com/">\t\t\tFox News Go\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/alerts/subscribe.html">\t\t\tNewsletters\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/newsletter-signup/alerts">\t\t\tAlerts\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/podcasts/">\t\t\tPodcasts\t\t\t</a>\t\t</li>\n   \t\t\t\t<li class="nav-item">\n\t\t\t<a href="//www.foxnews.com/about/apps-downloads">\t\t\tApps & Products\t\t\t</a>\t\t</li>\n   \t\t\n\t\t</ul>\n</nav>\n\t\t</div>\n\t</div>\n\t\n\t<div class="footer-lower">\n\t   <div class="social-icons">\n\t      <ul>\n\t         <li class="fb"><a href="//www.facebook.com/FoxNews">Facebook</a></li>\n\t         <li class="tw"><a href="//twitter.com/foxnews">Twitter</a></li>\n\t         <li class="flipboard"><a href="https://flipboard.com/@FoxNews">Flipboard</a></li>\n\t         <li class="gp"><a href="//www.google.com/+FoxNews">Google+</a></li>\n\t         <li class="ig"><a href="//www.instagram.com/foxnews">Instagram</a></li>\n\t         <li class="rss"><a href="//www.foxnews.com/rss/index.html">RSS</a></li>\n\t         <li class="email"><a href="//www.foxnews.com/alerts/subscribe.html">Email</a></li>\n\t      </ul>\n\t   </div>\n\t   <div class="branding"><a class="logo" href="//www.foxnews.com">Fox News</a></div>\n\t   <div class="legal">\n\t      <ul>\n\t         <li><a href="//www.foxnews.com/about/terms-of-use">Terms of Use</a></li>\n\t         <li><a href="//www.foxnews.com/about/privacy-policy">Privacy Policy</a></li>\n\t         <li><a href="//www.foxnews.com/closed-captioning">Closed Captioning Policy</a></li>\n\t         <li><a href="//help.foxnews.com/index.html">Help</a></li>\n\t      </ul>\n\t      <p class="copyright">This material may not be published, broadcast, rewritten, or redistributed. &copy;2018 FOX News Network, LLC. All rights reserved. All market data delayed 20 minutes.</p>\n\t   </div>\n\t   \n\t</div>\n</footer>      \n    \n\n\n\n\t    \n\n\n\n<noscript>\n    <img src="http://metrics.foxnews.com/b/ss/foxnews/1/H22.1--NS/1533079067292?pageName=tech:subsection:article&g=http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html" style="display:none;" width="0" height="0" border="0" alt="">\n    <img src="//secure-us.imrworldwide.com/cgi-bin/m?ci=us-801796h&amp;cg=0&amp;cc=1&amp;si=http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html&amp;ts=noscript&amp;rnd=1533079067292" style="display:none;" width="0" height="0" border="0" alt="">\n    <img src="http://b.scorecardresearch.com/p?c1=2&c2=9013181&c3=&c4=http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html&c5=&c6=&c15=&cv=2.2&cj=1" style="display:none;" width="0" height="0" border="0" alt="">\n</noscript>\n\n\t     <script type="text/javascript"> (function(FNC){ if (FNC.Loader) { FNC.Loader.init(); } }(window.FNC || {})); </script>\t    \n    </div>    \n</body>\n\n</html>',
 'publisher': 'Fox News',
 'text': 'Facebook uncovered "sophisticated" efforts, possibly linked to Russia, to influence American politics in advance of the U.S. midterm elections.\n\nThe company said in a blog post that it removed 32 accounts from Facebook and Instagram because they were involved in "coordinated" political behavior and appeared to be fake.\n\nFacebook did not explicitly say that the effort was aimed at influencing the midterm elections in November, but the timing of the suspicious activity would be consistent with such an attempt.\n\nThe company, which said it is in the early stages of its investigation, held briefings in the House and Senate this week.\n\nThe company said it doesn\'t know who is behind the efforts, but said there may be connections to Russia. Facebook said it has found some connections between the accounts it removed and the accounts connected to Russia\'s Internet Research Agency that it removed before and after the 2016 U.S. presidential elections.\n\nTWITTER BRINGS IN ANTI-TRUMP ACADEMICS TO FIGHT BIAS\n\nU.S. Senator Mark Warner, D-Va., who introduced the Honest Ads Act earlier this year to help prevent foreign interference in U.S. elections, praised Facebook\'s elections.\n\n“Today’s disclosure is further evidence that the Kremlin continues to exploit platforms like Facebook to sow division and spread disinformation, and I am glad that Facebook is taking some steps to pinpoint and address this activity," Warner said in a statement to Fox News.\n\n"I also expect Facebook, along with other platform companies, will continue to identify Russian troll activity and to work with Congress on updating our laws to better protect our democracy in the future,” he continued.\n\n"It’s clear that whoever set up these accounts went to much greater lengths to obscure their true identities than the Russian-based Internet Research Agency (IRA) has in the past," Facebook said in its statement. "We believe this could be partly due to changes we’ve made over the last year to make this kind of abuse much harder."\n\nFACEBOOK, TECH GIANTS CREATING \'CRISIS IN DEMOCRACY,\' UK REPORT SAYS\n\n"The goal of these operations is to sow discord, distrust, and division in an attempt to undermine public faith in our institutions and our political system," Sen. Richard Burr, R-N.C., said in a statement. "The Russians want a weak America. There is still much that needs to be done to prevent and counter foreign interference on social media."\n\nThe president has made it clear that his administration will not tolerate foreign interference into our electoral process from any nation state or other malicious actors," White House Deputy Press Secretary Hogan Gidley said in a statement late Tuesday.\n\nThe earliest page was created in March 2017. Facebook says more than 290,000 accounts followed at least one of the fake pages. The most followed Facebook Pages had names such as "Aztlan Warriors," \'\'Black Elevation," \'\'Mindful Being," and "Resisters."\n\nFacebook says the pages ran about 150 ads for $11,000 on Facebook and Instagram, paid for in U.S. and Canadian dollars. The first ad was created in April 2017; the last was created in June 2018.\n\nThe perpetrators used virtual private networks and internet phone services, and paid third parties to run ads on their behalf, according to Facebook.\n\nIn addition, Facebook revealed that its partnership with the Atlantic Council helped it to identify the bad actors. One of the groups with roughly 4,000 members was located based on leads from U.S. Special Counsel Robert Mueller\'s recent indictment of 12 Russian nationals for their role in hacking and disinformation efforts during the 2016 U.S. presidential election.\n\nThat group was created by Russian government figures but had been dormant since Facebook disabled its administrators last year. However, the tech company chose to remove the group to protect the privacy of its members in advance of a forthcoming report from the Atlantic Council that will analyze the Pages, profiles and accounts that Facebook disabled today.\n\nFreedom From Facebook, a group that has been pushing for the tech company to be broken up, said that today\'s announcement was not enough.\n\n“While it is good news that this latest attack on democracy via Facebook was discovered early, the fact that it happened highlights the danger posed when so much power is concentrated in a single company," said Sarah Miller, a campaigner with Freedom From Facebook.\n\nMiller continued: “We will not be safe from foreign interference -- and Facebook’s own business model of profiting off of bad actors -- until Congress and the FTC step in to break up the company and impose strong privacy rules.”\n\nFox News\' Bree Tracey and The Associated Press contributed to this report.',
 'title': "Facebook finds 'sophisticated' efforts to disrupt US politics, removes 32 accounts",
 'url': 'http://www.foxnews.com/tech/2018/07/31/facebook-finds-sophisticated-efforts-to-disrupt-us-politics-removes-32-accounts.html'}
```





{:.input_area}
```python
pd.DataFrame([article_details])
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
      <th>authors</th>
      <th>date</th>
      <th>description</th>
      <th>html</th>
      <th>publisher</th>
      <th>text</th>
      <th>title</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[Christopher Carbone]</td>
      <td>2018-07-31</td>
      <td>Facebook said it has uncovered sophisticated e...</td>
      <td>\n\n\n&lt;!DOCTYPE html&gt;\n&lt;html lang="en"&gt;\n    \...</td>
      <td>Fox News</td>
      <td>Facebook uncovered "sophisticated" efforts, po...</td>
      <td>Facebook finds 'sophisticated' efforts to disr...</td>
      <td>http://www.foxnews.com/tech/2018/07/31/faceboo...</td>
    </tr>
  </tbody>
</table>
</div>
</div>



<div class="alert alert-info">
<h3> Your turn</h3>
<p> Make a list of two article URLs from a newspaper. Make a dataframe that contains the text, and relevant meta data using newspaper.article.

</div>


If that doesn't work, you have to scrape it yourself.

How I scrape a page

1. Look at the source code of a sample page.
1. Download the page.
2. Find the thing that you want, and the stuff around that thing.
3. Write a regular expression that matches what you want.
4. Write regular expression that actually matches what you want.
5. Test that it works on one page.
5. Production!

How I could also scrape a page

1. Look at the source code of a sample page.
1. Download the page.
2. Find the thing that you want, and the stuff around that thing.
3. Parse the HTML. 
5. Test that it works on one page.
5. Production!



{:.input_area}
```python
import requests
```




{:.input_area}
```python
url = 'http://mobilizationjournal.org/toc/maiq/22/2'
```




{:.input_area}
```python
volume = 22
issue  = 2

url = 'http://mobilizationjournal.org/toc/maiq/' + str(volume) + '/' + str(issue)

print(url)
```


{:.output .output_stream}
```
http://mobilizationjournal.org/toc/maiq/22/2

```



{:.input_area}
```python
volume = 22
issue  = 2

url = 'http://mobilizationjournal.org/toc/maiq/%s/%s' % (volume, issue)

print(url)
```


{:.output .output_stream}
```
http://mobilizationjournal.org/toc/maiq/22/2

```



{:.input_area}
```python
page = requests.get(url)
```




{:.input_area}
```python
print(page.headers)
```


{:.output .output_stream}
```
{'Server': 'AtyponWS/7.1', 'X-XSS-Protection': '1; mode=block', 'X-Content-Type-Options': 'nosniff', 'X-Frame-Options': 'SAMEORIGIN', 'Cache-Control': 'no-cache', 'Pragma': 'no-cache', 'X-Webstats-RespID': 'c7b339248d1a5f28e7c8bbd0faa5d670', 'Content-Type': 'text/html; charset=UTF-8', 'Date': 'Wed, 01 Aug 2018 09:43:50 GMT', 'Vary': 'Accept-Encoding', 'Content-Encoding': 'gzip', 'Content-Length': '9577', 'Connection': 'Keep-Alive'}

```



{:.input_area}
```python
page.status_code
```





{:.output .output_data_text}
```
200
```





{:.input_area}
```python
page.text
```





{:.output .output_data_text}
```
'\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n        \n\n        \n                \n                \n                \n                    \n                    \n                        \n                                \n                            \n                        \n                        \n                    \n                \n            \n        \n                \n            \n    \n\n\n\n\n\n\n\n\n\n\n<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">\n\n\n\n<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en-US" lang="en-US">\n<head>\n    \n\n\n\n\n\n\n<title>\n    \n            Mobilization: An International Quarterly\n            -\n            \n                    Journal - Table of Contents\n                \n        \n</title>\n\n    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />\n    <meta name="robots" content="noarchive"/>\n\n\n\n\n\n\n\n\n\n<meta name="MSSmartTagsPreventParsing" content="true"/>\n\n\n\n\n    \n    \n    <script>\n        (function(i,s,o,g,r,a,m){i[\'GoogleAnalyticsObject\']=r;i[r]=i[r]||function(){\n            (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),\n                m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)\n        })(window,document,\'script\',\'//www.google-analytics.com/analytics.js\',\'ga\');\n\n        ga(\'create\', \'UA-60157750-4\', \'auto\');\n        ga(\'send\', \'pageview\');\n    </script>\n\n\n\n\n\n\n\n\n\n\n\n\n    \n\n\n\n\n\n    \n    \n    \n    <!-- multiple js libraries should be removed once the refactorting from prototype to jquery is complete -->\n    \n    <link href="/templates/jsp/style.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/style.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/style.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/_pinnacle/style.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/css/global.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/global.product.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/_pinnacle/css/pinnacle.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/css/widgets.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/widgets.product.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/css/headfoot.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/headfoot.product.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/css/browse.css" rel="stylesheet" type="text/css" />\n<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/browse.product.css" rel="stylesheet" type="text/css" />\n\n    <!--[if lt IE 8]>\n    <link href="/templates/jsp/_style2/_AP/css/ie.css" rel="stylesheet" type="text/css"/>\n    <link href="/templates/jsp/_style2/_AP/_pinnacle/css/ie.product.css" rel="stylesheet" type="text/css"/>\n    <![endif]-->\n    <!--[if IE 6]><link href="/templates/jsp/_style2/_AP/css/ie6.css" rel="stylesheet" type="text/css"/><![endif]-->\n    <script type="text/javascript"  src="/templates/jsp/_style2/_AP/_pinnacle/js/jquery.js"></script>\n<script type="text/javascript"  src="/templates/jsp/script.js"></script>\n<script type="text/javascript"  src="/templates/jsp/_style2/script.js"></script>\n<script type="text/javascript"  src="/templates/jsp/_style2/_AP/script.js"></script>\n<script type="text/javascript"  src="/templates/jsp/js/behaviour.js"></script>\n<script type="text/javascript"  src="/templates/jsp/js/behaviourRules.js"></script>\n\n    \n\n\n<style type="text/css">\ndiv#headerTopContainer {\n    background:url(/userimages/ContentEditor/1425330507088/HJDM_navbar.png);\n    background-color: #CBCCCD;\n    padding: 0 0;\n}\n\n#journalMenu ul ul {\n    background:#a3a3a3;\n}\n\n#journalMenu li:hover,\n  #journalMenu li:hover h2 {\n  background:#c1c1c1 !important;\n}\n\n#journalMenu h2 a {\n    font-size:10pt;\n    font-family:Verdana;\n    color:#434343;\n}\n\n#journalMenu ul ul {\n    background:#a3a3a3;\n}\n\ndiv#journalMenu ul li:hover ul a,\n  div#journalMenu ul ul li:hover ul a,\n  div#journalMenu ul ul ul li:hover ul a {\n    font-size:10pt;\n    font-family:Verdana;\n    color:#ffffff;\n}\n\n.panelHeading, .panelFooter {\n    background-color:#a4a4a4;\n    color:#000000;\n}\n\ndiv.panelBody {\n    background-color:#dddddd;\n    border-color:#a4a4a4;\n    border-width: 0 1px 0 1px;\n    border-style: solid;\n}\n\n.panelHeading .right {\n    background-image: url(\'/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_top2.png\');\n}\n\n.panelHeading .left {\n    background-image: url(\'/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_top.png\');\n}\n\n.panelFooter .right {\n    background-image: url(\'/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_bottom.png\');\n}\n\n.panelFooter .left {\n    background-image: url(\'/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_bottom.png\');\n}\n</style>\n\n\n<style type="text/css">\ndiv.panelTopRight {\nbackground-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_top2.png);\nbackground-repeat:no-repeat;\n}\ndiv.panelBottomLeft {\nbackground-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_bottom.png);\nbackground-repeat:no-repeat;\n}\ndiv.panelTopLeft {\nbackground-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_top.png);\nbackground-repeat:no-repeat;\n}\ndiv.panelBottomRight {\nbackground-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_bottom.png);\nbackground-repeat:no-repeat;\n}\ndiv#articleNavLinks {\n\nmargin-right:0.5em;\n}\n</style>\n    \n\n\n\n\n\n</head>\n<body class="clearfix">\n<div id="container">\n    \n\n\n\n\n\n\n\n\n\n\n\n\n    <div id="header">\n        \n                \n\n\n<a name="pageTop"></a>\n \n\n\n\n\n\n\n\n\n\n\n<div id="identity-bar">\n    <!-- placeholder id=null, description=Header - Mobile Button -->\n\n    \n        \n        <span id="institution-banner-text">\n            \n                Hoegskolen I Hedmark\n            \n        </span>\n        \n    \n\n    <span id="individual-menu">\n        \n\n        \n                \n                \n                <a href="https://pinnacle-secure.allenpress.com/action/showLogin?code=HJDM-site&amp;uri=%2Ftoc%2Fmaiq%2F22%2F2%3Fcode%3DHJDM-site">\n                    Log In\n                </a>\n                &nbsp;|&nbsp;\n                <a href="https://pinnacle-secure.allenpress.com/action/registration?code=HJDM-site">\n                    Register\n                </a>\n            \n        \n        \n            &nbsp;|&nbsp;\n            <a href="/help">\n                Help\n            </a>\n        \n        &nbsp;|&nbsp;\n        \n        <a href="https://pinnacle-secure.allenpress.com/action/showCart?code=HJDM-site">Cart </a>\n    </span>\n</div>\n<div id="headerAds">\n    <!-- placeholder id=null, description=Header Message 1 -->\n    <!-- placeholder id=null, description=Header Message 2 -->\n    \n    \n</div>\n\n\n\n\n\n\n\n    <img src="/userimages/1314156/banner" alt="Hank Johnston DBA Mobilization Journal" width="980" />\n    \n\n<div class="headerContainer" id="headerTopContainer">\n     <div id="journalMenu">         \n            \n\n\n<ul><li class="first"><h2><a href="/?code=hjdm-site">&emsp; HOME &emsp;</a></h2>\n</li>\n</ul><ul><li><h2><a href="/loi/maiq?code=hjdm-site">&emsp; CONTENT &emsp;</a></h2>\n</li>\n</ul><ul><li class="hasSubmenu"><h2><a href="/page/about?code=hjdm-site">&emsp; ABOUT &emsp;</a></h2>\n<ul><li class="first"><a href="/action/ecommerce?code=hjdm-site">Subscribe</a></li>\n<li><a href="https://mobilization.scholasticahq.com/for-authors" target="_blank">Submit</a></li>\n<li class="last"><a href="https://mobilization.scholasticahq.com/" target="_blank">Contact</a></li>\n</ul>\n</li>\n</ul><ul><li class="last"><h2><a href="/help?code=hjdm-site">&emsp; HELP &emsp;</a></h2>\n</li>\n</ul>       \n    </div>\n    <ul id="quickSearchBox">\n        <li>\n            \n\n\n\n\n<form action="/action/doSearch" name="simpleQuickSearchForm1" method="get"><input id="searchQuery" type="text" name="AllField" value="" size="18" onfocus="this.value=\'\';" placeholder="Quick Search" />\n    \n    \n    <input onclick="javascript:checkQuickSearchInput(document.simpleQuickSearchForm1,\'Quick Search\');return false;" id="gosearch" type="submit" value="" alt="Search" /></form>\n        </li>\n    </ul>\n</div>\n\n    <div id="breadcrumbs">\n        <table class="headerBreadcrumbs">\n            <tr>\n                <td>\n                    \n\n\n\n\n\n    <a href="/">Home</a>\n    \n        &gt;\n        \n                <a href="/loi/maiq">\n                    Mobilization: An International Quarterly\n                </a>\n            \n    \n        &gt;\n        \n                June 2017\n            \n    \n\n\n\n                </td>\n                <td class="headerAdvancedSearch">\n                    <a href="/search/advanced">Advanced Search</a>\n                    <!-- placeholder id=null, description=Google Translator Widget -->\n                </td>\n            </tr>\n        </table>\n    </div>\n\n\n            \n    </div>\n    <div id="content">\n        <div class="panelContent">\n            <table cellspacing="0" cellpadding="0" border="0" width="980">\n            <tbody>\n                <tr valign="top">\n                    <!-- Left Nav-->\n                    <!-- Custom Nav links -->\n                    <td width="228">\n                        <div id="leftCol">\n\n\n\n\n\n\n\n<ul id="customLinks">        <li style="border:1px solid #691a1a !important;"><a href="/action/ecommerce">Subscribe</a><br /></li><li style="border:1px solid #691a1a !important;"><a href="https://mobilization.scholasticahq.com/for-authors" target="_blank">Submit</a><br /></li><li style="border:1px solid #691a1a !important;"><a href="https://mobilization.scholasticahq.com/" target="_blank">Contact</a><br /></li>    </ul>\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message 1 -->\n    \n\n\n\n\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message 2 -->\n    \n\n\n\n\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message 3 -->\n    \n\n\n\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message 4 -->\n    \n\n\n\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message 5 -->\n    \n\n\n</div>\n                    </td>\n                    <td class="bc">\n                        <div id="middleCol"><form name="frmAbs" action="">\n            \n\n\n\n\n\n\n\n\n\n\n    \n        <div class="article_tools">\n            \n\n\n\n\n\n<div id="articleNavLinks">\n    <table cellspacing="10" cellpadding="0" border="0" id="Table9" class="bc" align="center" valign="middle">\n        <tbody>\n        <tr>\n            \n            <td>\n                <div id="articleToolsHeading">\n                    \n\n\n\n\n\n    \n\n                    \n\n                    \n                            Volume 22, Issue 2 <br/>(June 2017)\n                                        \n\n\n\n\n\n<div class="issueEditor"></div>\n                        \n                </div>\n            </td>\n            \n        </tr>\n        </tbody>\n    </table>\n</div>\n\n            \n        \n\n\n\n\n    \n\n\n\n\n<ul id="toolsList">\n    \n    \n        <li id="showAbs">\n            <a href="javascript:submitArticles(document.frmAbs, \'/action/showMultipleAbstracts\')">\n                View Abstracts\n            </a>\n        </li>\n        <li class="divider">|</li>\n    \n    <li id="addToFavs">\n        \n        <a href="javascript:submitArticles(document.frmAbs, \'/personalize/addFavoritePublication\')">Add to Favorites</a>\n    </li>\n    <li class="divider">|</li>\n    <li id="emailArt">\n        \n        <a href="javascript:submitArticles(document.frmAbs, \'/action/showMailPage\')">Share Article</a>\n    </li>\n    <li class="divider wbr">|</li>\n    <li id="downloadCit">\n        \n        <a href="javascript:submitArticles(document.frmAbs, \'/action/showCitFormats\')">Export Citations</a>\n    </li>\n    <li class="divider">|</li>\n    <li id="trackCit"  title="Track Citations shows articles citing this article. You may also sign up to receive an alert when this article is cited in the future via RSS feed.">\n        \n        \n        Track Citations (<a href="/action/addCitationRss?doi=10.17813%2F1086671X-22.2">RSS</a> | <a href="/action/addCitationAlert?doi=10.17813%2F1086671X-22.2&referrer=%2Ftoc%2Fmaiq%2F22%2F2%3Fcode%3DHJDM-site">Email</a>)\n    </li>\n    \n</ul>\n        \n        </div>\n    \n\n            \n\n\n\n\n\n<div class="panel_100p" id="tocPanel">\n    <div class="panelHeading">\n        <div class="left"></div>\n        <div class="content">\n            &nbsp;\n        </div>\n        <div class="right"></div>\n    </div>\n    <div class="panelBody">\n        <div id="marker">\n                        <input type="checkbox" name="markall" id="markall" onclick="onClickMarkAll(frmAbs)"/>\n                        <label for="markall">Select All</label>\n                    </div>\n                   <div style="float:right">\n                       \n\n\n\n\n\n    \n    <table summary="">\n        <tr>\n            <td nowrap="nowrap"></td>\n            <td nowrap="nowrap">\n                &nbsp;&nbsp;&nbsp;\n                <img src="/templates/jsp/_style2/_AP/images/openAccess.gif" alt="Open access" align="absbottom" border="0" />\n                <span style="color:#808080;padding-right: 1px;font-weight: normal">Open access</span>\n                &nbsp;&nbsp;&nbsp;\n                <img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_full.gif" alt="Full access" align="absbottom" border="0" />\n                <span style="color:#808080;padding-right: 1px;font-weight: normal">Full access</span>\n                &nbsp;&nbsp;&nbsp;\n                <img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_part.gif" alt="Partial access" align="absbottom" border="0" />\n                <span style="color:#808080;padding-right: 1px;font-weight: normal">Partial access</span>\n                &nbsp;&nbsp;&nbsp;\n                <img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No access" align="absbottom" border="0" />\n                <span style="color:#808080;padding-right: 1px;font-weight: normal">No access</span>\n            </td>\n        </tr>\n        <tr>\n            <td nowrap="nowrap"></td>\n        </tr>\n        <tr>\n            <td nowrap="nowrap"></td>\n            <td nowrap="nowrap"> &nbsp;&nbsp;&nbsp;</td>\n        </tr>\n    </table>\n\n\n                   </div>\n                   <div style="float:left">\n                        \n\n\n\n\n<div id="tocContent"><!--totalCount7--><!--modified:1532650485000--><h2 class="tocHeading"><span class="subj-group">Articles </span></h2><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">131</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-131" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">ROUTING AROUND ORGANIZATIONS: SELF-DIRECTED POLITICAL CONSUMPTION</div></span><span class="hlFld-ContribAuthor">Jennifer Earl</span>, <span class="hlFld-ContribAuthor">Lauren Copeland</span> and <span class="hlFld-ContribAuthor">Bruce Bimber</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-131">Abstract</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-131">PDF (373 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">155</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-155" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">BRINGING THE FACTORY BACK IN: THE CRUMBLING OF CONSENT AND THE MOLDING OF COLLECTIVE CAPACITY AT WORK</div></span><span class="hlFld-ContribAuthor">René Rojas</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-155">Abstract</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-155">PDF (357 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">177</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-177" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">RESPONDING TO THE STREET: GOVERNMENT RESPONSES TO MASS PROTESTS IN DEMOCRACIES</div></span><span class="hlFld-ContribAuthor">Alejandro Milcíades Peña</span> and <span class="hlFld-ContribAuthor">Thomas Richard Davies</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-177">Abstract</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-177">PDF (1662 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">201</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-201" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">VOICING OUTRAGE UNEVENLY: DEMOCRATIC DISSATISFACTION, NONPARTICIPATION, AND PARTICIPATION FREQUENCY IN THE 15-M CAMPAIGN</div></span><span class="hlFld-ContribAuthor">Martín Portos</span> and <span class="hlFld-ContribAuthor">Juan Masullo</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-201">Abstract</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-201">PDF (730 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">223</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-223" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">WHAT IS TO BE DONE? AGENCY AND THE CAUSATION OF TRANSFORMATIVE EVENTS IN IRELAND\'S 1916 RISING AND 1969 LONG MARCH</div></span><span class="hlFld-ContribAuthor">Lorenzo Bosi</span> and <span class="hlFld-ContribAuthor">Donagh Davis</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-223">Abstract</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-223">PDF (307 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">245</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-245" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">WHY ONLY SOME LIFESTYLE ACTIVISTS AVOID STATE-ORIENTED POLITICS: A CASE STUDY IN THE BELGIAN ENVIRONMENTAL MOVEMENT</div></span><span class="hlFld-ContribAuthor">Joost de Moor</span>, <span class="hlFld-ContribAuthor">Sofie Marien</span> and <span class="hlFld-ContribAuthor">Marc Hooghe</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-245">Abstract</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-245">PDF (285 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><h2 class="tocHeading"><span class="subj-group">Book Reviews </span></h2><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">265</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22.2.265" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">BOOK REVIEWS</div></span><span class="hlFld-ContribAuthor">Deana A. Rohlinger</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22.2.265">Citation</a>\r\n\t\t\t| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22.2.265">PDF (167 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table></div>\n<input type="hidden" name="href" value="/toc/maiq/22/2?code=HJDM-site" />\n\n<input type="hidden" name="mailPageTitle" value="Table of Contents for Mobilization: An International Quarterly: Volume 22, Issue 2 &lt;br/&gt;(June 2017)" />\n\n\n                    </div>\n        \n        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />\n    </div>\n    <div class="panelFooter">\n        <div class="left"></div>\n        <div class="content"></div>\n        <div class="right"></div>\n    </div>\n</div>\n        </form>\n        \n        <span id="hide"><a href="/doi/pdf/10.1046/9999-9999.99999"><!-- Spider trap link --></a></span></div>\n                    </td>\n                    <td width="200" class="bc">\n                        <div id="rightCol">\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n<div class="panel_228" id="journalNavPanel">\n    <div class="panelHeading">\n        <div class="left"></div>\n        <div class="content">\n            \n            \n                    <h3>Volume 22, Issue 2 <br/>(June 2017)</h3>\n                \n            \n        </div>\n        <div class="right"></div>\n    </div>\n    <div class="panelBody">\n        \n            <div id="nextprev">\n                \n                    <a href="/toc/maiq/22/1">\n                        &lt; Previous\n                    </a>\n                \n                \n                    <a href="/toc/maiq/22/3">\n                        Next &gt;\n                    </a>\n                \n            </div><br/>\n        \n        <div id="smallIssueCover">\n            \n                    <a href="/loi/maiq">\n                    <img src="/na101/home/literatum/publisher/pinnacle/journals/covergifs/maiq/cover.jpg" /><br/>\n                </a>\n                \n            \n        </div>\n\n        \n\n\n\n\n\n\n<div>\n    <!-- File is left blank intentionally. -->\n</div>\n<br/>\n\n\n\n    \n        \n\n\n\n\n        <a href="/toc/maiq/23/2">\n            Current Issue\n        </a><br/>\n    \n    \n\n\n\n\n<a href="/loi/maiq">\n    Available Issues\n</a><br/>\n\n\n\n    \n\n\n<div>\n\n</div>\n\n\n\n    \n\n\n\n\n\n\n\n\n\n\n\n        \n        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />\n    </div>\n    <div class="panelFooter">\n        <div class="left"></div>\n        <div class="content"></div>\n        <div class="right"></div>\n    </div>\n</div>\n\n\n\n\n\n\n\n\n<div class="panel_228">\n    <div class="panelHeading">\n        <div class="left"></div>\n        <div class="content">\n            <h3>Alerts for the Journal</h3>\n        </div>\n        <div class="right"></div>\n    </div>\n    <div class="panelBody">\n        Click\n        \n        <a href="#" class="subscribe-anchor" >here</a>\n        to get an email alert for every new issue of <br/> <h3>Mobilization: An International Quarterly</h3>\n    </div>\n    <div class="panelFooter">\n        <div class="left"></div>\n        <div class="content"></div>\n        <div class="right"></div>\n    </div>\n</div>\n\n\n<div class="subscribe-wrapper">\n    <span class="close-log">X</span>\n    <div class="subscribe-form">\n        \n\n\n<form action="/action/doSubscribe" name="emailSubscribe" method="post"><label class="subscribe-form-2-label">\n        Email Address:\n    </label>\n    <input type="email" name="email" autoFocus="autofocus"  class="subscribe-form-2" required/>\n    <br><br/>\n    <input type="hidden" value="true" name="fromsubscribe"/>\n    <input type="hidden" value="addJournal" name="action"/>\n    <input type="hidden" value="maiq" name="journalCode"/>\n    <input type="hidden" value="/toc/maiq/22/2?code=HJDM-site" name="referrer"/>\n    <input class="subscribe-form-2" type="submit" value="Subscribe"/></form>\n    </div>\n</div>\n\n<script type="text/javascript">\n    $(\'.subscribe-anchor\').click(function(e){\n        $(\'.subscribe-wrapper\').slideToggle();\n        e.preventDefault();\n    });\n    $(\'.close-log\').click(function() {\n        $(\'.subscribe-wrapper\').slideToggle();\n    });\n    $(document).keyup(function(e) {\n        if(e.which == 27){\n            if($(\'.subscribe-wrapper\').css("display") != \'none\') {\n                $(\'.subscribe-wrapper\').slideToggle();\n            }\n        }\n    });\n</script>\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message Right 1 -->\n    \n\n\n\n\n\n\n\n\n\n\n\n \n\n\n\n\n\n\n\n    \n        \n    \n\n\n\n\n\n\n\n\n<div class="panel_228" id="journalInfoPanel">\n    <div class="panelHeading">\n        <div class="left"></div>\n        <div class="content">\n            \n            \n                    \n                        <h3>Journal Information</h3>\n                    \n                \n            \n        </div>\n        <div class="right"></div>\n    </div>\n    <div class="panelBody">\n        \n            <table class="mceItemTable">\r\n    <tbody>\r\n        <tr>\r\n            <td><b>ISSN:</b></td>\r\n            <td>1086-671X</td>\r\n        </tr>\r\n        <tr>\r\n            <td><b>Frequency:</b></td>\r\n            <td>Quarterly</td>\r\n        </tr>\r\n        <tr>\r\n            <td><b>RSS Feed:</b><br /><a href="/page/rss">(What is this?)</a></td>\r\n            <td><a href="/action/showFeed?type=etoc&amp;feed=rss&amp;jc=maiq"><img title="RSS TOC Feed" alt="rrs icon" src="/userimages/ContentEditor/1422297266654/Rss-icon_24px.png" /></a></td>\r\n        </tr>\r\n    </tbody>\r\n</table>\n        \n        \n        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />\n    </div>\n    <div class="panelFooter">\n        <div class="left"></div>\n        <div class="content"></div>\n        <div class="right"></div>\n    </div>\n</div>\n\n\n\n\n\n\n\n\n\n \n\n\n\n\n\n\n\n    \n        \n    \n\n\n\n\n\n\n\n\n<div class="panel_228" id="registerPanel">\n    <div class="panelHeading">\n        <div class="left"></div>\n        <div class="content">\n            \n            \n                    \n                        <h3>Register for a Profile</h3>\n                    \n                \n            \n        </div>\n        <div class="right"></div>\n    </div>\n    <div class="panelBody">\n        \n        <p><b>Not Yet Registered?</b></p><p style="text-align:center;"><i>Benefits of Registration Include: </i></p><ul>    <li>A Unique User Profile that will allow you to manage your current subscriptions (including online access)</li>    <li>The ability to create favorites lists down to the article level</li>    <li>The ability to customize email alerts to receive specific notifications about the topics you care most about and special offers</li></ul><p><a href="/action/registration"><b>Register Now!</b></a><br /></p>\n        \n        \n        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />\n    </div>\n    <div class="panelFooter">\n        <div class="left"></div>\n        <div class="content"></div>\n        <div class="right"></div>\n    </div>\n</div>\n\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message Right 2 -->\n    \n\n\n\n\n\n\n\n\n\n        <!-- placeholder id=null, description=Journal Message Right 3 -->\n    \n\n\n\n</div>\n                    </td>\n                </tr>\n            </tbody>\n        </table>\n        </div>\n        <div class="clearfix">&nbsp;</div>\n    </div>\n    <div id="footer">\n        \n\n\n\n\n<div id="linkToTop">    <a href="#pageTop">        <img src="/templates/jsp/_style2/_AP/_pinnacle/images/link_top.gif" />    </a><br /></div><div id="footerCopy">    <hr /><p style="text-align: center;">    Published by <a href="mailto:mobilization@unc.edu" target="_blank">Mobilization</a><br />    Copyright &copy; 2018 Mobilization. All rights reserved.</p></div>\n    </div>\n</div>\n\r\n\r\n\r\n    \r\n\n</body>\n</html>\n\n\n\n'
```





{:.input_area}
```python
from IPython.display import HTML
```




{:.input_area}
```python
HTML(page.text)
```





<div markdown="0" class="output output_html">






















        

        
                
                
                
                    
                    
                        
                                
                            
                        
                        
                    
                
            
        
                
            
    










<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">



<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en-US" lang="en-US">
<head>
    






<title>
    
            Mobilization: An International Quarterly
            -
            
                    Journal - Table of Contents
                
        
</title>

    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta name="robots" content="noarchive"/>









<meta name="MSSmartTagsPreventParsing" content="true"/>




    
    
    <script>
        (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
            (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
                m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
        })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

        ga('create', 'UA-60157750-4', 'auto');
        ga('send', 'pageview');
    </script>












    





    
    
    
    <!-- multiple js libraries should be removed once the refactorting from prototype to jquery is complete -->
    
    <link href="/templates/jsp/style.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/style.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/style.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/_pinnacle/style.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/css/global.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/global.product.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/_pinnacle/css/pinnacle.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/css/widgets.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/widgets.product.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/css/headfoot.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/headfoot.product.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/css/browse.css" rel="stylesheet" type="text/css" />
<link href="/templates/jsp/_style2/_AP/_pinnacle/_theme2/css/browse.product.css" rel="stylesheet" type="text/css" />

    <!--[if lt IE 8]>
    <link href="/templates/jsp/_style2/_AP/css/ie.css" rel="stylesheet" type="text/css"/>
    <link href="/templates/jsp/_style2/_AP/_pinnacle/css/ie.product.css" rel="stylesheet" type="text/css"/>
    <![endif]-->
    <!--[if IE 6]><link href="/templates/jsp/_style2/_AP/css/ie6.css" rel="stylesheet" type="text/css"/><![endif]-->
    <script type="text/javascript"  src="/templates/jsp/_style2/_AP/_pinnacle/js/jquery.js"></script>
<script type="text/javascript"  src="/templates/jsp/script.js"></script>
<script type="text/javascript"  src="/templates/jsp/_style2/script.js"></script>
<script type="text/javascript"  src="/templates/jsp/_style2/_AP/script.js"></script>
<script type="text/javascript"  src="/templates/jsp/js/behaviour.js"></script>
<script type="text/javascript"  src="/templates/jsp/js/behaviourRules.js"></script>

    


<style type="text/css">
div#headerTopContainer {
    background:url(/userimages/ContentEditor/1425330507088/HJDM_navbar.png);
    background-color: #CBCCCD;
    padding: 0 0;
}

#journalMenu ul ul {
    background:#a3a3a3;
}

#journalMenu li:hover,
  #journalMenu li:hover h2 {
  background:#c1c1c1 !important;
}

#journalMenu h2 a {
    font-size:10pt;
    font-family:Verdana;
    color:#434343;
}

#journalMenu ul ul {
    background:#a3a3a3;
}

div#journalMenu ul li:hover ul a,
  div#journalMenu ul ul li:hover ul a,
  div#journalMenu ul ul ul li:hover ul a {
    font-size:10pt;
    font-family:Verdana;
    color:#ffffff;
}

.panelHeading, .panelFooter {
    background-color:#a4a4a4;
    color:#000000;
}

div.panelBody {
    background-color:#dddddd;
    border-color:#a4a4a4;
    border-width: 0 1px 0 1px;
    border-style: solid;
}

.panelHeading .right {
    background-image: url('/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_top2.png');
}

.panelHeading .left {
    background-image: url('/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_top.png');
}

.panelFooter .right {
    background-image: url('/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_bottom.png');
}

.panelFooter .left {
    background-image: url('/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_bottom.png');
}
</style>


<style type="text/css">
div.panelTopRight {
background-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_top2.png);
background-repeat:no-repeat;
}
div.panelBottomLeft {
background-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_bottom.png);
background-repeat:no-repeat;
}
div.panelTopLeft {
background-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_left_top.png);
background-repeat:no-repeat;
}
div.panelBottomRight {
background-image:url(/templates/jsp/_style2/_AP/_pinnacle/images/curve_right_bottom.png);
background-repeat:no-repeat;
}
div#articleNavLinks {

margin-right:0.5em;
}
</style>
    





</head>
<body class="clearfix">
<div id="container">
    












    <div id="header">
        
                


<a name="pageTop"></a>
 










<div id="identity-bar">
    <!-- placeholder id=null, description=Header - Mobile Button -->

    
        
        <span id="institution-banner-text">
            
                Hoegskolen I Hedmark
            
        </span>
        
    

    <span id="individual-menu">
        

        
                
                
                <a href="https://pinnacle-secure.allenpress.com/action/showLogin?code=HJDM-site&amp;uri=%2Ftoc%2Fmaiq%2F22%2F2%3Fcode%3DHJDM-site">
                    Log In
                </a>
                &nbsp;|&nbsp;
                <a href="https://pinnacle-secure.allenpress.com/action/registration?code=HJDM-site">
                    Register
                </a>
            
        
        
            &nbsp;|&nbsp;
            <a href="/help">
                Help
            </a>
        
        &nbsp;|&nbsp;
        
        <a href="https://pinnacle-secure.allenpress.com/action/showCart?code=HJDM-site">Cart </a>
    </span>
</div>
<div id="headerAds">
    <!-- placeholder id=null, description=Header Message 1 -->
    <!-- placeholder id=null, description=Header Message 2 -->
    
    
</div>







    <img src="/userimages/1314156/banner" alt="Hank Johnston DBA Mobilization Journal" width="980" />
    

<div class="headerContainer" id="headerTopContainer">
     <div id="journalMenu">         
            


<ul><li class="first"><h2><a href="/?code=hjdm-site">&emsp; HOME &emsp;</a></h2>
</li>
</ul><ul><li><h2><a href="/loi/maiq?code=hjdm-site">&emsp; CONTENT &emsp;</a></h2>
</li>
</ul><ul><li class="hasSubmenu"><h2><a href="/page/about?code=hjdm-site">&emsp; ABOUT &emsp;</a></h2>
<ul><li class="first"><a href="/action/ecommerce?code=hjdm-site">Subscribe</a></li>
<li><a href="https://mobilization.scholasticahq.com/for-authors" target="_blank">Submit</a></li>
<li class="last"><a href="https://mobilization.scholasticahq.com/" target="_blank">Contact</a></li>
</ul>
</li>
</ul><ul><li class="last"><h2><a href="/help?code=hjdm-site">&emsp; HELP &emsp;</a></h2>
</li>
</ul>       
    </div>
    <ul id="quickSearchBox">
        <li>
            




<form action="/action/doSearch" name="simpleQuickSearchForm1" method="get"><input id="searchQuery" type="text" name="AllField" value="" size="18" onfocus="this.value='';" placeholder="Quick Search" />
    
    
    <input onclick="javascript:checkQuickSearchInput(document.simpleQuickSearchForm1,'Quick Search');return false;" id="gosearch" type="submit" value="" alt="Search" /></form>
        </li>
    </ul>
</div>

    <div id="breadcrumbs">
        <table class="headerBreadcrumbs">
            <tr>
                <td>
                    





    <a href="/">Home</a>
    
        &gt;
        
                <a href="/loi/maiq">
                    Mobilization: An International Quarterly
                </a>
            
    
        &gt;
        
                June 2017
            
    



                </td>
                <td class="headerAdvancedSearch">
                    <a href="/search/advanced">Advanced Search</a>
                    <!-- placeholder id=null, description=Google Translator Widget -->
                </td>
            </tr>
        </table>
    </div>


            
    </div>
    <div id="content">
        <div class="panelContent">
            <table cellspacing="0" cellpadding="0" border="0" width="980">
            <tbody>
                <tr valign="top">
                    <!-- Left Nav-->
                    <!-- Custom Nav links -->
                    <td width="228">
                        <div id="leftCol">







<ul id="customLinks">        <li style="border:1px solid #691a1a !important;"><a href="/action/ecommerce">Subscribe</a><br /></li><li style="border:1px solid #691a1a !important;"><a href="https://mobilization.scholasticahq.com/for-authors" target="_blank">Submit</a><br /></li><li style="border:1px solid #691a1a !important;"><a href="https://mobilization.scholasticahq.com/" target="_blank">Contact</a><br /></li>    </ul>





        <!-- placeholder id=null, description=Journal Message 1 -->
    









        <!-- placeholder id=null, description=Journal Message 2 -->
    









        <!-- placeholder id=null, description=Journal Message 3 -->
    








        <!-- placeholder id=null, description=Journal Message 4 -->
    








        <!-- placeholder id=null, description=Journal Message 5 -->
    


</div>
                    </td>
                    <td class="bc">
                        <div id="middleCol"><form name="frmAbs" action="">
            










    
        <div class="article_tools">
            





<div id="articleNavLinks">
    <table cellspacing="10" cellpadding="0" border="0" id="Table9" class="bc" align="center" valign="middle">
        <tbody>
        <tr>
            
            <td>
                <div id="articleToolsHeading">
                    





    

                    

                    
                            Volume 22, Issue 2 <br/>(June 2017)
                                        





<div class="issueEditor"></div>
                        
                </div>
            </td>
            
        </tr>
        </tbody>
    </table>
</div>

            
        




    




<ul id="toolsList">
    
    
        <li id="showAbs">
            <a href="javascript:submitArticles(document.frmAbs, '/action/showMultipleAbstracts')">
                View Abstracts
            </a>
        </li>
        <li class="divider">|</li>
    
    <li id="addToFavs">
        
        <a href="javascript:submitArticles(document.frmAbs, '/personalize/addFavoritePublication')">Add to Favorites</a>
    </li>
    <li class="divider">|</li>
    <li id="emailArt">
        
        <a href="javascript:submitArticles(document.frmAbs, '/action/showMailPage')">Share Article</a>
    </li>
    <li class="divider wbr">|</li>
    <li id="downloadCit">
        
        <a href="javascript:submitArticles(document.frmAbs, '/action/showCitFormats')">Export Citations</a>
    </li>
    <li class="divider">|</li>
    <li id="trackCit"  title="Track Citations shows articles citing this article. You may also sign up to receive an alert when this article is cited in the future via RSS feed.">
        
        
        Track Citations (<a href="/action/addCitationRss?doi=10.17813%2F1086671X-22.2">RSS</a> | <a href="/action/addCitationAlert?doi=10.17813%2F1086671X-22.2&referrer=%2Ftoc%2Fmaiq%2F22%2F2%3Fcode%3DHJDM-site">Email</a>)
    </li>
    
</ul>
        
        </div>
    

            





<div class="panel_100p" id="tocPanel">
    <div class="panelHeading">
        <div class="left"></div>
        <div class="content">
            &nbsp;
        </div>
        <div class="right"></div>
    </div>
    <div class="panelBody">
        <div id="marker">
                        <input type="checkbox" name="markall" id="markall" onclick="onClickMarkAll(frmAbs)"/>
                        <label for="markall">Select All</label>
                    </div>
                   <div style="float:right">
                       





    
    <table summary="">
        <tr>
            <td nowrap="nowrap"></td>
            <td nowrap="nowrap">
                &nbsp;&nbsp;&nbsp;
                <img src="/templates/jsp/_style2/_AP/images/openAccess.gif" alt="Open access" align="absbottom" border="0" />
                <span style="color:#808080;padding-right: 1px;font-weight: normal">Open access</span>
                &nbsp;&nbsp;&nbsp;
                <img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_full.gif" alt="Full access" align="absbottom" border="0" />
                <span style="color:#808080;padding-right: 1px;font-weight: normal">Full access</span>
                &nbsp;&nbsp;&nbsp;
                <img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_part.gif" alt="Partial access" align="absbottom" border="0" />
                <span style="color:#808080;padding-right: 1px;font-weight: normal">Partial access</span>
                &nbsp;&nbsp;&nbsp;
                <img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No access" align="absbottom" border="0" />
                <span style="color:#808080;padding-right: 1px;font-weight: normal">No access</span>
            </td>
        </tr>
        <tr>
            <td nowrap="nowrap"></td>
        </tr>
        <tr>
            <td nowrap="nowrap"></td>
            <td nowrap="nowrap"> &nbsp;&nbsp;&nbsp;</td>
        </tr>
    </table>


                   </div>
                   <div style="float:left">
                        




<div id="tocContent"><!--totalCount7--><!--modified:1532650485000--><h2 class="tocHeading"><span class="subj-group">Articles </span></h2><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">131</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-131" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">ROUTING AROUND ORGANIZATIONS: SELF-DIRECTED POLITICAL CONSUMPTION</div></span><span class="hlFld-ContribAuthor">Jennifer Earl</span>, <span class="hlFld-ContribAuthor">Lauren Copeland</span> and <span class="hlFld-ContribAuthor">Bruce Bimber</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-131">Abstract</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-131">PDF (373 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">155</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-155" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">BRINGING THE FACTORY BACK IN: THE CRUMBLING OF CONSENT AND THE MOLDING OF COLLECTIVE CAPACITY AT WORK</div></span><span class="hlFld-ContribAuthor">René Rojas</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-155">Abstract</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-155">PDF (357 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">177</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-177" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">RESPONDING TO THE STREET: GOVERNMENT RESPONSES TO MASS PROTESTS IN DEMOCRACIES</div></span><span class="hlFld-ContribAuthor">Alejandro Milcíades Peña</span> and <span class="hlFld-ContribAuthor">Thomas Richard Davies</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-177">Abstract</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-177">PDF (1662 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">201</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-201" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">VOICING OUTRAGE UNEVENLY: DEMOCRATIC DISSATISFACTION, NONPARTICIPATION, AND PARTICIPATION FREQUENCY IN THE 15-M CAMPAIGN</div></span><span class="hlFld-ContribAuthor">Martín Portos</span> and <span class="hlFld-ContribAuthor">Juan Masullo</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-201">Abstract</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-201">PDF (730 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">223</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-223" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">WHAT IS TO BE DONE? AGENCY AND THE CAUSATION OF TRANSFORMATIVE EVENTS IN IRELAND'S 1916 RISING AND 1969 LONG MARCH</div></span><span class="hlFld-ContribAuthor">Lorenzo Bosi</span> and <span class="hlFld-ContribAuthor">Donagh Davis</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-223">Abstract</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-223">PDF (307 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">245</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22-2-245" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">WHY ONLY SOME LIFESTYLE ACTIVISTS AVOID STATE-ORIENTED POLITICS: A CASE STUDY IN THE BELGIAN ENVIRONMENTAL MOVEMENT</div></span><span class="hlFld-ContribAuthor">Joost de Moor</span>, <span class="hlFld-ContribAuthor">Sofie Marien</span> and <span class="hlFld-ContribAuthor">Marc Hooghe</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22-2-245">Abstract</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22-2-245">PDF (285 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table><h2 class="tocHeading"><span class="subj-group">Book Reviews </span></h2><table border="0" width="100%" class="articleEntry"><tr><td align="right" valign="top" width="18" class="nowrap">265</td><td align="right" valign="top" width="18"><input type="checkbox" name="doi" value="10.17813/1086-671X-22.2.265" /><br /></td><td valign="top"><span class="hlFld-Title"><div class="art_title">BOOK REVIEWS</div></span><span class="hlFld-ContribAuthor">Deana A. Rohlinger</span><br /><a class="ref nowrap useQueryHash" href="/doi/abs/10.17813/1086-671X-22.2.265">Citation</a>
			| <a class="ref nowrap" target="_blank" title="Opens new window" href="/doi/pdf/10.17813/1086-671X-22.2.265">PDF (167 KB)</a>&nbsp;</td><td align="right" valign="top" width="18"><img src="/templates/jsp/_style2/_AP/_pinnacle/_theme2/images/access_no.gif" alt="No Access" title="No Access" class="accessIcon noAccess" /></td></tr></table></div>
<input type="hidden" name="href" value="/toc/maiq/22/2?code=HJDM-site" />

<input type="hidden" name="mailPageTitle" value="Table of Contents for Mobilization: An International Quarterly: Volume 22, Issue 2 &lt;br/&gt;(June 2017)" />


                    </div>
        
        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />
    </div>
    <div class="panelFooter">
        <div class="left"></div>
        <div class="content"></div>
        <div class="right"></div>
    </div>
</div>
        </form>
        
        <span id="hide"><a href="/doi/pdf/10.1046/9999-9999.99999"><!-- Spider trap link --></a></span></div>
                    </td>
                    <td width="200" class="bc">
                        <div id="rightCol">


























<div class="panel_228" id="journalNavPanel">
    <div class="panelHeading">
        <div class="left"></div>
        <div class="content">
            
            
                    <h3>Volume 22, Issue 2 <br/>(June 2017)</h3>
                
            
        </div>
        <div class="right"></div>
    </div>
    <div class="panelBody">
        
            <div id="nextprev">
                
                    <a href="/toc/maiq/22/1">
                        &lt; Previous
                    </a>
                
                
                    <a href="/toc/maiq/22/3">
                        Next &gt;
                    </a>
                
            </div><br/>
        
        <div id="smallIssueCover">
            
                    <a href="/loi/maiq">
                    <img src="/na101/home/literatum/publisher/pinnacle/journals/covergifs/maiq/cover.jpg" /><br/>
                </a>
                
            
        </div>

        






<div>
    <!-- File is left blank intentionally. -->
</div>
<br/>



    
        




        <a href="/toc/maiq/23/2">
            Current Issue
        </a><br/>
    
    




<a href="/loi/maiq">
    Available Issues
</a><br/>



    


<div>

</div>



    











        
        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />
    </div>
    <div class="panelFooter">
        <div class="left"></div>
        <div class="content"></div>
        <div class="right"></div>
    </div>
</div>








<div class="panel_228">
    <div class="panelHeading">
        <div class="left"></div>
        <div class="content">
            <h3>Alerts for the Journal</h3>
        </div>
        <div class="right"></div>
    </div>
    <div class="panelBody">
        Click
        
        <a href="#" class="subscribe-anchor" >here</a>
        to get an email alert for every new issue of <br/> <h3>Mobilization: An International Quarterly</h3>
    </div>
    <div class="panelFooter">
        <div class="left"></div>
        <div class="content"></div>
        <div class="right"></div>
    </div>
</div>


<div class="subscribe-wrapper">
    <span class="close-log">X</span>
    <div class="subscribe-form">
        


<form action="/action/doSubscribe" name="emailSubscribe" method="post"><label class="subscribe-form-2-label">
        Email Address:
    </label>
    <input type="email" name="email" autoFocus="autofocus"  class="subscribe-form-2" required/>
    <br><br/>
    <input type="hidden" value="true" name="fromsubscribe"/>
    <input type="hidden" value="addJournal" name="action"/>
    <input type="hidden" value="maiq" name="journalCode"/>
    <input type="hidden" value="/toc/maiq/22/2?code=HJDM-site" name="referrer"/>
    <input class="subscribe-form-2" type="submit" value="Subscribe"/></form>
    </div>
</div>

<script type="text/javascript">
    $('.subscribe-anchor').click(function(e){
        $('.subscribe-wrapper').slideToggle();
        e.preventDefault();
    });
    $('.close-log').click(function() {
        $('.subscribe-wrapper').slideToggle();
    });
    $(document).keyup(function(e) {
        if(e.which == 27){
            if($('.subscribe-wrapper').css("display") != 'none') {
                $('.subscribe-wrapper').slideToggle();
            }
        }
    });
</script>





        <!-- placeholder id=null, description=Journal Message Right 1 -->
    











 







    
        
    








<div class="panel_228" id="journalInfoPanel">
    <div class="panelHeading">
        <div class="left"></div>
        <div class="content">
            
            
                    
                        <h3>Journal Information</h3>
                    
                
            
        </div>
        <div class="right"></div>
    </div>
    <div class="panelBody">
        
            <table class="mceItemTable">
    <tbody>
        <tr>
            <td><b>ISSN:</b></td>
            <td>1086-671X</td>
        </tr>
        <tr>
            <td><b>Frequency:</b></td>
            <td>Quarterly</td>
        </tr>
        <tr>
            <td><b>RSS Feed:</b><br /><a href="/page/rss">(What is this?)</a></td>
            <td><a href="/action/showFeed?type=etoc&amp;feed=rss&amp;jc=maiq"><img title="RSS TOC Feed" alt="rrs icon" src="/userimages/ContentEditor/1422297266654/Rss-icon_24px.png" /></a></td>
        </tr>
    </tbody>
</table>
        
        
        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />
    </div>
    <div class="panelFooter">
        <div class="left"></div>
        <div class="content"></div>
        <div class="right"></div>
    </div>
</div>









 







    
        
    








<div class="panel_228" id="registerPanel">
    <div class="panelHeading">
        <div class="left"></div>
        <div class="content">
            
            
                    
                        <h3>Register for a Profile</h3>
                    
                
            
        </div>
        <div class="right"></div>
    </div>
    <div class="panelBody">
        
        <p><b>Not Yet Registered?</b></p><p style="text-align:center;"><i>Benefits of Registration Include: </i></p><ul>    <li>A Unique User Profile that will allow you to manage your current subscriptions (including online access)</li>    <li>The ability to create favorites lists down to the article level</li>    <li>The ability to customize email alerts to receive specific notifications about the topics you care most about and special offers</li></ul><p><a href="/action/registration"><b>Register Now!</b></a><br /></p>
        
        
        <img class="spacer" src="/templates/jsp/images/singlePixel.gif" />
    </div>
    <div class="panelFooter">
        <div class="left"></div>
        <div class="content"></div>
        <div class="right"></div>
    </div>
</div>






        <!-- placeholder id=null, description=Journal Message Right 2 -->
    









        <!-- placeholder id=null, description=Journal Message Right 3 -->
    



</div>
                    </td>
                </tr>
            </tbody>
        </table>
        </div>
        <div class="clearfix">&nbsp;</div>
    </div>
    <div id="footer">
        




<div id="linkToTop">    <a href="#pageTop">        <img src="/templates/jsp/_style2/_AP/_pinnacle/images/link_top.gif" />    </a><br /></div><div id="footerCopy">    <hr /><p style="text-align: center;">    Published by <a href="mailto:mobilization@unc.edu" target="_blank">Mobilization</a><br />    Copyright &copy; 2018 Mobilization. All rights reserved.</p></div>
    </div>
</div>



    

</body>
</html>




</div>





{:.input_area}
```python
page_html = page.text
```


Look at the source code of a sample page.

![](images/source.png)

1. Open Chrome and navigate the web page of your choice.
2. Click on Customize and control Google Chrome Chrome settings icon icon in the upper right-hand side of the browser window.
3. From the drop-down menu that appears, select More tools and then Developer tools.

1. Open Microsoft Edge and navigate to the web page of your choice.
2. Click the More Edge more icon icon in the upper right-hand corner of the screen.
3. Select F12 Developer Tools from the drop-down menu that appears.

Look at the source code of a sample page.

![](images/search_html.png)

So our headline is here:

`div class="art_title">ROUTING AROUND ORGANIZATIONS: SELF-DIRECTED POLITICAL CONSUMPTION<`

which fits the pattern

`div class="art_title">HEADLINE<`

Regular expressions

xkcd - 208

![](https://raw.github.com/nealcaren/workshop_2014/master/notebooks/images/regular_expressions.png)

Regular expressions

xkcd - 1171

![](https://raw.github.com/nealcaren/workshop_2014/master/notebooks/images/perl_problems.png)



{:.input_area}
```python
import re
```


`HEADLINE` becomes `(.*?)`

`.`  match any character  
`*`  and keep going  
`?`  until you find the first ...



{:.input_area}
```python
re.findall('div class="art_title">.*?<', page_html)   
```





{:.output .output_data_text}
```
['div class="art_title">ROUTING AROUND ORGANIZATIONS: SELF-DIRECTED POLITICAL CONSUMPTION<',
 'div class="art_title">BRINGING THE FACTORY BACK IN: THE CRUMBLING OF CONSENT AND THE MOLDING OF COLLECTIVE CAPACITY AT WORK<',
 'div class="art_title">RESPONDING TO THE STREET: GOVERNMENT RESPONSES TO MASS PROTESTS IN DEMOCRACIES<',
 'div class="art_title">VOICING OUTRAGE UNEVENLY: DEMOCRATIC DISSATISFACTION, NONPARTICIPATION, AND PARTICIPATION FREQUENCY IN THE 15-M CAMPAIGN<',
 'div class="art_title">WHAT IS TO BE DONE? AGENCY AND THE CAUSATION OF TRANSFORMATIVE EVENTS IN IRELAND\'S 1916 RISING AND 1969 LONG MARCH<',
 'div class="art_title">WHY ONLY SOME LIFESTYLE ACTIVISTS AVOID STATE-ORIENTED POLITICS: A CASE STUDY IN THE BELGIAN ENVIRONMENTAL MOVEMENT<',
 'div class="art_title">BOOK REVIEWS<']
```



`HEADLINE` becomes `(.*?)`

`.`  match any character  
`*`  and keep going  
`?`  until you find the first ...

and

only return things inbetween the `()`



{:.input_area}
```python
re.findall('div class="art_title">(.*?)<', page_html)   
```





{:.output .output_data_text}
```
['ROUTING AROUND ORGANIZATIONS: SELF-DIRECTED POLITICAL CONSUMPTION',
 'BRINGING THE FACTORY BACK IN: THE CRUMBLING OF CONSENT AND THE MOLDING OF COLLECTIVE CAPACITY AT WORK',
 'RESPONDING TO THE STREET: GOVERNMENT RESPONSES TO MASS PROTESTS IN DEMOCRACIES',
 'VOICING OUTRAGE UNEVENLY: DEMOCRATIC DISSATISFACTION, NONPARTICIPATION, AND PARTICIPATION FREQUENCY IN THE 15-M CAMPAIGN',
 "WHAT IS TO BE DONE? AGENCY AND THE CAUSATION OF TRANSFORMATIVE EVENTS IN IRELAND'S 1916 RISING AND 1969 LONG MARCH",
 'WHY ONLY SOME LIFESTYLE ACTIVISTS AVOID STATE-ORIENTED POLITICS: A CASE STUDY IN THE BELGIAN ENVIRONMENTAL MOVEMENT',
 'BOOK REVIEWS']
```



You might want to include `.*?` or  `()` or any of `^$.|\+[{` as part of your search term.


You can, with `\`

This tell your regular expression interpreter to treat the next character literally. 



{:.input_area}
```python
re.findall('>PDF .*?<', page_html)   
```





{:.output .output_data_text}
```
['>PDF (373 KB)<',
 '>PDF (357 KB)<',
 '>PDF (1662 KB)<',
 '>PDF (730 KB)<',
 '>PDF (307 KB)<',
 '>PDF (285 KB)<',
 '>PDF (167 KB)<']
```




<div class="alert alert-info">
<h3> Your turn</h3>
Your turn. Find the sizes of all the linked PDFs!


</div> 

Store the results somewhere, and try it on a different page.



{:.input_area}
```python
titles = re.findall('div class="art_title">(.*?)<\/div', page_html)   
```




{:.input_area}
```python
titles
```




{:.input_area}
```python
volume = 23
issue  = 2

url = 'http://mobilizationjournal.org/toc/maiq/%s/%s' % (volume, issue)

print(url)

page = requests.get(url)
page_html = page.text
titles =  re.findall('div class="art_title">(.*?)<\/div', page_html)  

print(titles)

```


Production time!!!



{:.input_area}
```python
volume = 20

for issue in range(1,5):
    url = 'http://mobilizationjournal.org/toc/maiq/%s/%s' % (volume, issue)
    print(url)

```




{:.input_area}
```python
from time import sleep
```




{:.input_area}
```python
def get_moby_titles(page_html):
    titles =  re.findall('div class="art_title">(.*?)<\/div', page_html)  
    return titles


#somewhere to store the titles
titles = []
volume = 20

#Loop through 1 volume pages.
for issue in range(1,5):
    #construct the URL
    url = 'http://mobilizationjournal.org/toc/maiq/%s/%s' % (volume, issue)

    #Open the page and grab the HTML
    page = requests.get(url)
    page_html = page.text
    
    #Extract the headlines
    new_titles = get_moby_titles(page_html) 

    #Add them to our headline list
    titles = titles + new_titles
    
    #Rest
    sleep(1)

    

```




{:.input_area}
```python
len(titles)
```




{:.input_area}
```python
titles
```




{:.input_area}
```python
titles[:10]
```




{:.input_area}
```python
titles[-10:]
```


Time to get it out of here.



{:.input_area}
```python
import pandas as pd

pd.DataFrame(titles)
```




{:.input_area}
```python
pd.DataFrame(titles, columns=['Article Title'])
```




{:.input_area}
```python
moby_df = pd.DataFrame(titles, columns=['Article Title'])

moby_df.to_csv('moby_titles.csv', index=False)
```


<div class="alert alert-info"> 
<h1> Your turn</h1>
<p>Find the email addresses of the University of Oslo Sociology & Human Geography faculty. 
<p> <em> Remember to put things in functions as soon as possible. </em>
<p>Does the script work for your department?
</div>

[Directory](https://www.sv.uio.no/iss/english/people/aca/?page=1)


### If you know HTML, you can also parse the page.



{:.input_area}
```python
from bs4 import BeautifulSoup
```




{:.input_area}
```python
soup = BeautifulSoup(page_html, "lxml")
```




{:.input_area}
```python
soup.find_all('div', attrs={'class':'art_title'})
```




{:.input_area}
```python
ts = soup.find_all('div', attrs={'class':'art_title'})

for title in ts:
    print(title.contents[0])

```




{:.input_area}
```python
pd.read_excel('data/groups.xlsx')
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
      <th>Family name:</th>
      <th>First name:</th>
      <th>Group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Berg</td>
      <td>Oddmund</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Blomqvist</td>
      <td>Niklas</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dahl</td>
      <td>Espen Steinung</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Danner</td>
      <td>Hannah</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Donnally</td>
      <td>Sandra</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Ericsson</td>
      <td>Sanna Charlotta</td>
      <td>4</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Gao</td>
      <td>Wenxin</td>
      <td>2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Geissinger</td>
      <td>Andrea</td>
      <td>5</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Getik</td>
      <td>Demid</td>
      <td>6</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Hammerschmidt</td>
      <td>Dennis</td>
      <td>6</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Hovdahl</td>
      <td>Isabel</td>
      <td>3</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Islam</td>
      <td>Marco</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Jensen</td>
      <td>Are</td>
      <td>4</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Jesnes</td>
      <td>Kristin</td>
      <td>5</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Kilman</td>
      <td>Josefin Mari</td>
      <td>2</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Knutsen</td>
      <td>Tora Kjærnes</td>
      <td>5</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Knutsson</td>
      <td>Polina</td>
      <td>3</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Kontareva</td>
      <td>Alina</td>
      <td>1</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Langørgen</td>
      <td>Erlend</td>
      <td>2</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Llave</td>
      <td>Marilex Rea</td>
      <td>6</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Lundstedt</td>
      <td>Karl Jonas Valter</td>
      <td>4</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Molden</td>
      <td>Birgitte Hovdan</td>
      <td>1</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Molden</td>
      <td>Lars Hovdan</td>
      <td>2</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Olme</td>
      <td>Elisabet</td>
      <td>5</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Oraby</td>
      <td>Tarek</td>
      <td>6</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Parmer</td>
      <td>Pernille</td>
      <td>3</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Persarvet</td>
      <td>Viktor</td>
      <td>1</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Pesl</td>
      <td>Jan</td>
      <td>1</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Samuelsen</td>
      <td>Jeanette</td>
      <td>4</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Schultheiss</td>
      <td>Tobias</td>
      <td>2</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Schwabe</td>
      <td>Henrik</td>
      <td>2</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Smidt</td>
      <td>Martin</td>
      <td>5</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Solbakken</td>
      <td>Simen Sørbøe</td>
      <td>3</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Strøm-Andersen</td>
      <td>Nhat</td>
      <td>3</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Svennevik</td>
      <td>Elisabeth M. C.</td>
      <td>4</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Troset</td>
      <td>Tina Løvsletten</td>
      <td>6</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Wu</td>
      <td>You Ola</td>
      <td>6</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Yaldiz</td>
      <td>Nur</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>
</div>



<div class="alert alert-info">
<h3> Big Group Project</h3>
<p> There is a file called groups.xlsx in the data folder. That assigns everyone in the class to a group, numbered 1-6. Find your group!
<p> As a group, pick a major newspaper, such as the Washington Post or the Guardian. Your goal is get all their articles on Brexit. Go!
<p> Work together!!! Be prepared to present your results to the class!

</div>




{:.input_area}
```python
pd.concat?
```




{:.input_area}
```python
def load_or_get(volume, issue):
    '''
    Tries to open a Moby issue. If not found, gets it from the internet.
    '''
    
    file_name = 'moby_%s_%s.html' % (volume, issue)
    url       = 'http://mobilizationjournal.org/toc/maiq/%s/%s' % (volume, issue)
    
    # First, try to find the file stored locally.
    try:
        with codecs.open(file_name, 'r') as infile:
            page_html = infile.read()
    # If that didn't work, try getting it from the interent      
    except Exception, e:
        print 'Going to the internet to get %s-%s' % (issue, volume)
        page = requests.get(url)
        page_html = page.text
            
        # Save the file so you only go to the page once. It is polite. 
        with codecs.open(file_name, 'wb') as outfile:
            outfile.write(page_html)
    
    #don't forget to send the stuff back
    return page_html
```




{:.input_area}
```python
page_html = load_or_get(8,1)
```




{:.input_area}
```python
page_html = load_or_get(10,1)
```




{:.input_area}
```python
def scrape_headlines(page_html):
    titles =  re.findall('div class="art_title">(.*?)<\/div', page_html)
    return titles
```




{:.input_area}
```python
volume = 13

for issue in [1,2]:
    page_html = load_or_get(volume, issue)
    print  scrape_headlines(page_html)
```




{:.input_area}
```python
pd.read_html('http://www.espn.com/soccer/commentary?gameId=514949')[2]
```


### Still stuck?


Spoofing a browser

`headers = {'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36'}`

`r = requests.get(url, headers=headers)`

Keep cookies

`s = requests.Session()`

`s.get('http://httpbin.org/get')`


Authentication in requests

`requests.get('https://api.github.com/user', auth=('user', 'pass'))`

### Still still stuck



2. [scrapy](https://scrapy.org/)
3. Selenium - control the browser. 

---
redirect_from:
  - "collecting/file-reading"
interact_link: content/collecting/file_reading.ipynb
title: 'From files'
prev_page:
  url: /placeholder
  title: 'Collecting texts'
next_page:
  url: /collecting/apis
  title: 'Web APIs'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---



{:.input_area}
```python
import docx2txt
```




{:.input_area}
```python
text = docx2txt.process('data/pandas_wiki.docx')
```




{:.input_area}
```python
text
```





{:.output .output_data_text}
```
'In\xa0computer programming,\xa0pandas\xa0is a\xa0software library\xa0written for the\xa0Python programming language\xa0for data manipulation and analysis. In particular, it offers data structures and operations for manipulating numerical tables and\xa0time series. It is\xa0free software\xa0released under the\xa0three-clause BSD license.\xa0The name is derived from the term "panel data", an\xa0econometrics\xa0term for data sets that include observations over multiple time periods for the same individuals.\n\n\n\nLibrary features\n\nDataFrame object for data manipulation with integrated indexing.\n\nTools for reading and writing data between in-memory data structures and different file formats.\n\nData alignment and integrated handling of missing data.\n\nReshaping and pivoting of data sets.\n\nLabel-based slicing, fancy indexing, and subsetting of large data sets.\n\nData structure column insertion and deletion.\n\nGroup by engine allowing split-apply-combine operations on data sets.\n\nData set merging and joining.\n\nHierarchical axis indexing to work with high-dimensional data in a lower-dimensional data structure.\n\nTime series-functionality: Date range generation[3]\xa0and frequency conversion, moving window statistics, moving window linear regressions, date shifting and lagging.\n\nThe library is highly optimized for performance, with critical code paths written in\xa0Cython\xa0or\xa0C.'
```





{:.input_area}
```python
print(text)
```


{:.output .output_stream}
```
In computer programming, pandas is a software library written for the Python programming language for data manipulation and analysis. In particular, it offers data structures and operations for manipulating numerical tables and time series. It is free software released under the three-clause BSD license. The name is derived from the term "panel data", an econometrics term for data sets that include observations over multiple time periods for the same individuals.



Library features

DataFrame object for data manipulation with integrated indexing.

Tools for reading and writing data between in-memory data structures and different file formats.

Data alignment and integrated handling of missing data.

Reshaping and pivoting of data sets.

Label-based slicing, fancy indexing, and subsetting of large data sets.

Data structure column insertion and deletion.

Group by engine allowing split-apply-combine operations on data sets.

Data set merging and joining.

Hierarchical axis indexing to work with high-dimensional data in a lower-dimensional data structure.

Time series-functionality: Date range generation[3] and frequency conversion, moving window statistics, moving window linear regressions, date shifting and lagging.

The library is highly optimized for performance, with critical code paths written in Cython or C.

```



{:.input_area}
```python
import PyPDF2
```




{:.input_area}
```python
pdfFileObj = open('data/l09r01.pdf', 'rb')
```




{:.input_area}
```python
pdfReader = PyPDF2.PdfFileReader(pdfFileObj)
```




{:.input_area}
```python
print(pdfReader.numPages)
```


{:.output .output_stream}
```
32

```



{:.input_area}
```python
print(pdfReader.getPage(0).extractText())
```


{:.output .output_stream}
```
 
GE.15
-
21932(E)
 
*1521932*
 
 
 
Conference of the Parties
 
Twenty
-
first session
 
Paris, 30 November 
to 
11
 
December 201
5
 
Agenda item 
4
(
b
)
 
Durban Platform for Enhanced Action (decision 1/CP.17)
 
Adoption of a protocol, another legal instrument, or an 
 
agreed outcome with legal force under the Convention 
 
applicable to all Parties
 
 
 
ADOPTION OF THE PARIS AGREEMENT
 
Proposal by 
the President
 
Draft decision 
-
/CP.21
 
The
 
Conference of the Parties
,
 
Recalling 
decision 1/CP.17 on the establishment of the Ad Hoc Working Group on 
the Durban Platform for Enhanced Action,
 
Also
 
recalling
 
Articles 2, 3 and 4 of the Convention,
 
Further
 
recalling 
relevant
 
decisions of the Conference of the Parties, including 
decisions 
1/CP.16, 
2/CP.18, 1/CP.19 and 1/CP.20,
 
Welcoming
 
the 
adoption 
of
 
United Nations General Assembly resolution 
A/RES/70/1,
 

our world: the 2030 Age
nda for Sustaina

, in 
particular its goal 13, and the 
adoption
 
of the Addis Ababa Action Agenda of the third 
International Conference on Financing for Development
 
and the adoption of the Sendai 
Framework for Disaster Risk Reduction
,
 
Recognizing
 
that climate
 
change represents an urgent and potentially irreversible 
threat to human societies and the planet and thus requires the widest possible cooperation 
by all countries, and their participation in an effective and appropriate international 
response, with a vi
ew to accelerating the reduction of global greenhouse gas 
emissions, 
 
Also
 
r
ecognizing 
that 
deep reductions 
in global emissions will be required in order 
to achieve the ultimate objective of the Convention and emphasizing the need for urgency 
in 
address
ing
 
climate change, 
 
Acknowledging
 
that climate change is a common concern of humankind, 
Parties 
should, when taking action to address climate change, respect
, 
promote
 
and consider
 
their 
respective obligations on human rights,
 
the right to health, the rights of indigenous peoples, 
 
+
 
 
United Nations
 
FCCC
/CP/201
5
/L.
9
/Rev.1
 
 
 
 
Distr.: Limited
 
12
 
December 2015
 
 
Original: English
 


```



{:.input_area}
```python
text = ''

for page_number in range(0,pdfReader.numPages):
    text = text +  pdfReader.getPage(page_number).extractText()
```




{:.input_area}
```python
len(text)
```





{:.output .output_data_text}
```
116358
```





{:.input_area}
```python
print(text[5135:6135])
```


{:.output .output_stream}
```
uests 
Parties to provide 
notification of any such provisional 
application to the Depositary;
 
FCCC/CP/2015/L.9
/Rev.1
 
 
3
 
6.
 
Notes
 
that the work of the Ad Hoc Working Group on the Durban Platform for 
Enhanced Action, in accordance with decision 1/CP.17, paragraph 4, has been completed;
 
7.
 
Decides
 
to establish the Ad Hoc Working Group on the Paris Agreement under the 
same arrangement, mutatis mutandis, as those concerning the election of officers to the 
Bureau of the 
Ad Hoc Working Group on the Durban Platform for Enhanced Action
;
1
 
8.
 
Also
 
decides
 
that the Ad Hoc Working Group on the Paris Agreement shall prepare 
for the entry into force of the Agreement and for the convening of the first session of the 
Conference of the Parties serving as the meeting of the Parties to the Paris Agreement;
 
9.
 
Furthe
r
 
decides
 
to oversee the implementation of the work programme resulting 
from the relevant requests contained in this decision;
 
10.
 
Requests
 
t

```



{:.input_area}
```python
def extract_page(page_number):
    text = pdfReader.getPage(page_number).extractText()
    return {'page' : page_number + 1,
            'text' : text}
    

pages = []

for page_number in range(0,pdfReader.numPages):
    pages.append(extract_page(page_number))
```




{:.input_area}
```python
len(pages)
```





{:.output .output_data_text}
```
32
```





{:.input_area}
```python
pages[5]
```





{:.output .output_data_text}
```
{'page': 6,
 'text': 'FCCC/CP/2015/L.9\n/Rev.1\n \n6\n \n \nand the excha\nnge of information, experiences, and best practices amongst Parties to raise \ntheir resilience to these impacts\n;\n*\n \n36.\n \nInvites\n \nParties to communicate, by 2020, to the secretariat mid\n-\ncentury, long\n-\nterm \nlow greenhouse gas emission development strategies in ac\ncordance with Article 4, \nparagraph 19, of the Agreement, and \nrequests\n \nthe secretariat to publish on the UNFCCC \n\n \n37.\n \nRequests\n \nthe Subsidiary Body for Scientific and Technolo\ngical Advice to develop \nand recommend the guidance referred to under Article 6, paragraph 2, of the Agreement for \nadoption by the Conference of the Parties serving as the meeting of the Parties to the Paris \nAgreement at its first session, including guidanc\ne to ensure that double counting is avoided \non the basis of a corresponding adjustment by Parties for \nboth \nanthropogenic emissions by \nsources and removals by sinks covered by their nationally determined contributions under \nthe Agreement;\n \n38.\n \nRecommends \ntha\nt the \nConference of the Parties serving as the meeting of the Parties \nto the Paris Agreement\n \nadopt rules, modalities and procedures for the mechanism \nestablished by Article 6, paragraph 4, of the Agreement on the basis of: \n \n(a)\n \nVoluntary participation auth\norized by each Party involved;\n \n(b)\n \nReal, measurable, and long\n-\nterm benefits related to the mitigation of climate \nchange;\n \n(c)\n \nSpecific scopes of activities; \n \n(d)\n \nReductions in emissions that are additional to any that would otherwise \noccur;\n \n(e)\n \nVerification\n \nand certification of emission reductions resulting from \nmitigation activities by designated operational entities;\n \n(f)\n \nExperience gained with and lessons learned from existing mechanisms and \napproaches adopted under the Convention and its related legal ins\ntruments;\n \n39.\n \nRequests \nthe Subsidiary Body for Scientific and Technological Advice to develop \nand recommend rules, modalities and procedures for the mechanism referred to in \nparagraph 38 above for consideration and adoption by the \nConference of the Parties\n \nserving \nas the meeting of the Parties to the Paris Agreement\n \nat its first session;\n \n40.\n \nAlso\n \nr\nequests\n \nthe Subsidiary Body for Scientific and Technological Advice to \nundertake a work programme under the framework for non\n-\nmarket approaches to \nsustainable development referred to in Article 6, paragraph 8, of the Agreement, with the \nobjective of considering h\now to enhance linkages and create synergy between, inter alia, \nmitigation, adaptation, finance, t\nechnology transfer and capacity\n-\nbuilding, and how to \nfacilitate the implementation and coordination of non\n-\nmarket approaches;\n \n41.\n \nFurther requests\n \nthe Subsidia\nry Body for Scientific and Technological Advice to \nrecommend a draft decision on the work programme referred to in paragraph 40 above, \ntaking into account the views of Parties, for consideration and adoption by the \nConference \nof the Parties serving as the \nmeeting of the Parties to the Paris Agreement\n \nat its first \nsession;\n \nA\nDAPTATION\n \n                                        \n                  \n \n \n*\n \n \nParagraph 35 has been deleted, and subsequent paragraph numbering and cross references to other \nparagraphs within the document will be amended at a later stage.\n \n'}
```





{:.input_area}
```python
import pandas as pd
```




{:.input_area}
```python
df = pd.DataFrame(pages)
```




{:.input_area}
```python
df.head()
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
      <th>page</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>\nGE.15\n-\n21932(E)\n \n*1521932*\n \n \n \n...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>FCCC/CP/2015/L.9\n/Rev.1\n \n2\n \n \nlocal co...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>FCCC/CP/2015/L.9\n/Rev.1\n \n \n3\n \n6.\n \nN...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>FCCC/CP/2015/L.9\n/Rev.1\n \n4\n \n \n\n-\nind...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>FCCC/CP/2015/L.9\n/Rev.1\n \n \n5\n \nemission...</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
df.to_csv('data/paris_accord.csv', index=False)
```


Paris accord looks like this

![paris_accord.png](attachment:paris_accord.png)

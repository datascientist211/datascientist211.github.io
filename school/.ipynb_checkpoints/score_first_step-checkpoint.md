---

title: Dutch schools dataset construction
description: " "

---


Since I need to choose the school most fitted for my child, I really interested in having more knowledge about primary education system in The Netherlands and to know more about school differences. I found that Dutch Ministry of education, culture and science opened some data about primary schools. It's very scattered information placed in different files in not very useful for analysis form, so before go to analysis I want to join info I need together, clean data, deal with missing values and maybe tramsform data to the view which would be easier to read and analyse. 

All the data for this part of dataset construction I got here: https://duo.nl/open_onderwijsdata/databestanden/po/leerlingen-po/bo-sbo/bo-sbo-eindscores.jsp

## 1. Exams scores

First I will join a few .cvs files about exam score from differetn years together:


```python
import pandas as pd 
data_score_2014 = pd.read_csv("input_data/Test_score_2014_2015.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_score_2015 = pd.read_csv("input_data/Test_score_2015_2016.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_score_2016 = pd.read_csv("input_data/Test_score_2016_2017.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_score_2017 = pd.read_csv("input_data/Test_score_2017_2018.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_score_2018 = pd.read_csv("input_data/Test_score_2018_2019.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 

print(data_score_2014.columns)
data_score_2018.head()
```

    Index(['PEILDATUM_LEERLINGEN', 'PRIKDATUM_SCORES', 'BRIN_NUMMER',
           'VESTIGINGSNUMMER', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'Leerjaar_8',
           'ONTHEFFING_REDEN_ND', 'CET_AANTAL', 'cet_gem', 'IEP_AANTAL', 'iep_gem',
           'ROUTE8_AANTAL', 'route8_gem', 'DREMPEL_AANTAL', 'drempel_gem'],
          dtype='object')





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
      <th>PEILDATUM_LEERLINGEN</th>
      <th>PRIKDATUM_SCORES</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>...</th>
      <th>CET_AANTAL</th>
      <th>CET_GEM</th>
      <th>IEP_AANTAL</th>
      <th>IEP_GEM</th>
      <th>ROUTE8_AANTAL</th>
      <th>ROUTE8_GEM</th>
      <th>DIA_AANTAL</th>
      <th>DIA_GEM</th>
      <th>AMN_AANTAL</th>
      <th>AMN_GEM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20190417</td>
      <td>20190725</td>
      <td>00AP</td>
      <td>0</td>
      <td>Wereldwijs</td>
      <td>2716PH</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>11</td>
      <td>538,636363636364</td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>20190417</td>
      <td>20190725</td>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6107AW</td>
      <td>STEVENSWEERT</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>...</td>
      <td>17</td>
      <td>537,588235294118</td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>20190417</td>
      <td>20190725</td>
      <td>00AV</td>
      <td>0</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>1930</td>
      <td>Nissewaard</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>12</td>
      <td>532,5</td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>20190417</td>
      <td>20190725</td>
      <td>00AZ</td>
      <td>0</td>
      <td>De Stapsteen</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0</td>
      <td></td>
      <td>14</td>
      <td>84,3571428571429</td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>20190417</td>
      <td>20190725</td>
      <td>00BA</td>
      <td>0</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>...</td>
      <td>0</td>
      <td></td>
      <td>16</td>
      <td>83,625</td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
      <td>0</td>
      <td></td>
    </tr>
  </tbody>
</table>
<p>5 rows × 25 columns</p>
</div>



I noticed that the ***data_score_2014*** dataframe have some columns named using small letters, but in other dataframes only capital letters are using, let's change it:


```python
data_score_2014.rename(columns = {'cet_gem': 'CET_GEM', 'iep_gem': 'IEP_GEM', 'route8_gem':'ROUTE8_GEM', 'Leerjaar_8':'LEERJAAR_8'}, inplace = True)
```

Now let's merge everything in one dataframe:


```python
data_frames = [data_score_2014, data_score_2015, data_score_2016, data_score_2017, data_score_2018]
data_score = pd.concat(data_frames).fillna(' ')
data_score.reset_index(drop=True, inplace=True)
print(data_score_2014.shape)
print(data_score_2015.shape)
print(data_score_2016.shape)
print(data_score_2017.shape)
print(data_score_2018.shape)
print(data_score.shape)
```

    (6920, 23)
    (6851, 21)
    (6751, 27)
    (6658, 25)
    (6579, 25)
    (33759, 29)



```python
data_score.head()
```




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
      <th>PEILDATUM_LEERLINGEN</th>
      <th>PRIKDATUM_SCORES</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>...</th>
      <th>ROUTE8_AANTAL</th>
      <th>ROUTE8_GEM</th>
      <th>DREMPEL_AANTAL</th>
      <th>drempel_gem</th>
      <th>DIA_AANTAL</th>
      <th>DIA_GEM</th>
      <th>CESAN_AANTAL</th>
      <th>CESAN_GEM</th>
      <th>AMN_AANTAL</th>
      <th>AMN_GEM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20141001</td>
      <td>20150918</td>
      <td>00AP</td>
      <td>0</td>
      <td>De Schanskorf</td>
      <td>2715BT</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>20141001</td>
      <td>20150918</td>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6109AM</td>
      <td>OHE EN LAAK</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>...</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>20141001</td>
      <td>20150918</td>
      <td>00AV</td>
      <td>0</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>612</td>
      <td>Spijkenisse</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>20141001</td>
      <td>20150918</td>
      <td>00AZ</td>
      <td>0</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>20141001</td>
      <td>20150918</td>
      <td>00BA</td>
      <td>0</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>...</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>



Now I want to see which type of columns we have in dataset:


```python
print(data_score.dtypes)
```

    PEILDATUM_LEERLINGEN          int64
    PRIKDATUM_SCORES              int64
    BRIN_NUMMER                  object
    VESTIGINGSNUMMER              int64
    INSTELLINGSNAAM_VESTIGING    object
    POSTCODE_VESTIGING           object
    PLAATSNAAM                   object
    GEMEENTENUMMER                int64
    GEMEENTENAAM                 object
    PROVINCIE                    object
    SOORT_PO                     object
    DENOMINATIE_VESTIGING        object
    BEVOEGD_GEZAG_NUMMER          int64
    LEERJAAR_8                    int64
    ONTHEFFING_REDEN_ND          object
    CET_AANTAL                   object
    CET_GEM                      object
    IEP_AANTAL                   object
    IEP_GEM                      object
    ROUTE8_AANTAL                object
    ROUTE8_GEM                   object
    DREMPEL_AANTAL               object
    drempel_gem                  object
    DIA_AANTAL                   object
    DIA_GEM                      object
    CESAN_AANTAL                 object
    CESAN_GEM                    object
    AMN_AANTAL                   object
    AMN_GEM                      object
    dtype: object


There are few columns has type 'object', but they should has 'int' or 'float' type, so I think there are some values which cannot be converted to numeric ones. Let's check it. I think that such values can be considered as missing values, so let's change it to 0:


```python
def fill_missing_zero_value(dataframe, column):
    dataframe.loc[dataframe[column] == ' ', column] = 0

def convert_to_numeric(dataframe, column):
    dataframe[column] = dataframe[column].astype(str).str.replace(',','.')
    dataframe[column] = pd.to_numeric(dataframe[column])

def get_numeric_column(dataframe, column):
    fill_missing_zero_value(dataframe, column)
    convert_to_numeric(dataframe, column)

get_numeric_column(data_score, 'ONTHEFFING_REDEN_ND')
data_score['ONTHEFFING_REDEN_ND'].astype(int)

get_numeric_column(data_score, 'CET_AANTAL')
data_score['CET_AANTAL'] = data_score['CET_AANTAL'].astype(int)
get_numeric_column(data_score, 'CET_GEM')

get_numeric_column(data_score, 'IEP_AANTAL')
data_score['IEP_AANTAL'] = data_score['IEP_AANTAL'].astype(int)
get_numeric_column(data_score, 'IEP_GEM')

get_numeric_column(data_score, 'ROUTE8_AANTAL')
data_score['ROUTE8_AANTAL'] = data_score['ROUTE8_AANTAL'].astype(int)
get_numeric_column(data_score, 'ROUTE8_GEM')

get_numeric_column(data_score, 'DREMPEL_AANTAL')
data_score['DREMPEL_AANTAL'] = data_score['DREMPEL_AANTAL'].astype(int)
get_numeric_column(data_score, 'drempel_gem')

get_numeric_column(data_score, 'DIA_AANTAL')
data_score['DIA_AANTAL'] = data_score['DIA_AANTAL'].astype(int)
get_numeric_column(data_score, 'DIA_GEM')

get_numeric_column(data_score, 'CESAN_AANTAL')
data_score['CESAN_AANTAL'] = data_score['CESAN_AANTAL'].astype(int)
get_numeric_column(data_score, 'CESAN_GEM')

get_numeric_column(data_score, 'AMN_AANTAL')
data_score['AMN_AANTAL'] = data_score['AMN_AANTAL'].astype(int)
get_numeric_column(data_score, 'AMN_GEM')

print(data_score.dtypes)
```

    PEILDATUM_LEERLINGEN           int64
    PRIKDATUM_SCORES               int64
    BRIN_NUMMER                   object
    VESTIGINGSNUMMER               int64
    INSTELLINGSNAAM_VESTIGING     object
    POSTCODE_VESTIGING            object
    PLAATSNAAM                    object
    GEMEENTENUMMER                 int64
    GEMEENTENAAM                  object
    PROVINCIE                     object
    SOORT_PO                      object
    DENOMINATIE_VESTIGING         object
    BEVOEGD_GEZAG_NUMMER           int64
    LEERJAAR_8                     int64
    ONTHEFFING_REDEN_ND          float64
    CET_AANTAL                     int64
    CET_GEM                      float64
    IEP_AANTAL                     int64
    IEP_GEM                      float64
    ROUTE8_AANTAL                  int64
    ROUTE8_GEM                   float64
    DREMPEL_AANTAL                 int64
    drempel_gem                  float64
    DIA_AANTAL                     int64
    DIA_GEM                      float64
    CESAN_AANTAL                   int64
    CESAN_GEM                    float64
    AMN_AANTAL                     int64
    AMN_GEM                      float64
    dtype: object


Let's check do we have null or NaN values:


```python
print(data_score.isnull().sum())
print(data_score.isna().sum())
```

    PEILDATUM_LEERLINGEN         0
    PRIKDATUM_SCORES             0
    BRIN_NUMMER                  0
    VESTIGINGSNUMMER             0
    INSTELLINGSNAAM_VESTIGING    0
    POSTCODE_VESTIGING           0
    PLAATSNAAM                   0
    GEMEENTENUMMER               0
    GEMEENTENAAM                 0
    PROVINCIE                    0
    SOORT_PO                     0
    DENOMINATIE_VESTIGING        0
    BEVOEGD_GEZAG_NUMMER         0
    LEERJAAR_8                   0
    ONTHEFFING_REDEN_ND          0
    CET_AANTAL                   0
    CET_GEM                      0
    IEP_AANTAL                   0
    IEP_GEM                      0
    ROUTE8_AANTAL                0
    ROUTE8_GEM                   0
    DREMPEL_AANTAL               0
    drempel_gem                  0
    DIA_AANTAL                   0
    DIA_GEM                      0
    CESAN_AANTAL                 0
    CESAN_GEM                    0
    AMN_AANTAL                   0
    AMN_GEM                      0
    dtype: int64
    PEILDATUM_LEERLINGEN         0
    PRIKDATUM_SCORES             0
    BRIN_NUMMER                  0
    VESTIGINGSNUMMER             0
    INSTELLINGSNAAM_VESTIGING    0
    POSTCODE_VESTIGING           0
    PLAATSNAAM                   0
    GEMEENTENUMMER               0
    GEMEENTENAAM                 0
    PROVINCIE                    0
    SOORT_PO                     0
    DENOMINATIE_VESTIGING        0
    BEVOEGD_GEZAG_NUMMER         0
    LEERJAAR_8                   0
    ONTHEFFING_REDEN_ND          0
    CET_AANTAL                   0
    CET_GEM                      0
    IEP_AANTAL                   0
    IEP_GEM                      0
    ROUTE8_AANTAL                0
    ROUTE8_GEM                   0
    DREMPEL_AANTAL               0
    drempel_gem                  0
    DIA_AANTAL                   0
    DIA_GEM                      0
    CESAN_AANTAL                 0
    CESAN_GEM                    0
    AMN_AANTAL                   0
    AMN_GEM                      0
    dtype: int64


It looks like we don't have NaN or null values.

Now I would like to change columns with exam's date info to columns with just a year of the exam:


```python
data_score['PRIKDATUM_SCORES'] = data_score['PRIKDATUM_SCORES'].map({
    20150918 : 2015,
    20160722 : 2016,
    20170722 : 2017,
    20180725 : 2018,
    20190725 : 2019,
})
data_score.rename(columns = {'PRIKDATUM_SCORES': 'DATUM'}, inplace = True)
```


```python
print(data_score['DATUM'].value_counts())
```

    2015    6920
    2016    6851
    2017    6751
    2018    6658
    2019    6579
    Name: DATUM, dtype: int64


I know that most common final test in the primary schools in The Netherlands is CET and suppose it shoul be most common schools choise in terms of types of final exam. I want to explore what information we have about exams students are taking, but first let's explore do we have the rows without any info about exams:


```python
schools_without_exams = data_score[(data_score['CET_AANTAL'] == 0) & 
                               (data_score['DREMPEL_AANTAL'] == 0) & (data_score['AMN_AANTAL'] == 0) 
                                & (data_score['CESAN_AANTAL'] == 0) & (data_score['DIA_AANTAL'] == 0) 
                                & (data_score['ROUTE8_AANTAL'] == 0) & (data_score['IEP_AANTAL'] == 0)]
schools_at_least_1_exam = data_score[(data_score['CET_AANTAL'] > 0) | 
                               (data_score['DREMPEL_AANTAL'] > 0) | (data_score['AMN_AANTAL'] > 0) 
                                | (data_score['CESAN_AANTAL'] > 0) | (data_score['DIA_AANTAL'] > 0) 
                                | (data_score['ROUTE8_AANTAL'] > 0) | (data_score['IEP_AANTAL'] > 0)]
print(data_score.shape)
print(schools_at_least_1_exam.shape)
print(schools_without_exams.shape)
```

    (33759, 29)
    (32253, 29)
    (1506, 29)


And remove this rows (without any exams):


```python
data_score.drop(schools_without_exams.index, 0,  inplace = True)
print(data_score.shape)
```

    (32253, 29)


Next let's take a look do we have wrong exams data (for example, we have amount of pupils who took the exam, but score is 0 or vice versa). And if we have it, we need to remove it.


```python
def remove_wrong_data(subset):
    if subset.shape[0] > 0:
        data_score.drop(subset.index, 0,  inplace = True)

wrong_cet = data_score[((data_score['CET_AANTAL'] == 0) & (data_score['CET_GEM'] > 0)) |
                      ((data_score['CET_AANTAL'] > 0) & (data_score['CET_GEM'] == 0))]
print(wrong_cet.shape)
remove_wrong_data(wrong_cet)

wrong_drempel = data_score[((data_score['DREMPEL_AANTAL'] == 0) & (data_score['drempel_gem'] > 0)) |
                      ((data_score['DREMPEL_AANTAL'] > 0) & (data_score['drempel_gem'] == 0))]
print(wrong_drempel.shape)
remove_wrong_data(wrong_drempel)

wrong_AMN = data_score[((data_score['AMN_AANTAL'] == 0) & (data_score['AMN_GEM'] > 0)) |
                      ((data_score['AMN_AANTAL'] > 0) & (data_score['AMN_GEM'] == 0))]
print(wrong_AMN.shape)
remove_wrong_data(wrong_AMN)

wrong_CESAN = data_score[((data_score['CESAN_AANTAL'] == 0) & (data_score['CESAN_GEM'] > 0)) |
                      ((data_score['CESAN_AANTAL'] > 0) & (data_score['CESAN_GEM'] == 0))]
print(wrong_CESAN.shape)
remove_wrong_data(wrong_CESAN)

wrong_DIA = data_score[((data_score['DIA_AANTAL'] == 0) & (data_score['DIA_GEM'] > 0)) |
                      ((data_score['DIA_AANTAL'] > 0) & (data_score['DIA_GEM'] == 0))]
print(wrong_DIA.shape)
remove_wrong_data(wrong_DIA)

wrong_ROUTE8 = data_score[((data_score['ROUTE8_AANTAL'] == 0) & (data_score['ROUTE8_GEM'] > 0)) |
                      ((data_score['ROUTE8_AANTAL'] > 0) & (data_score['ROUTE8_GEM'] == 0))]
print(wrong_ROUTE8.shape)
remove_wrong_data(wrong_ROUTE8)

wrong_IEP = data_score[((data_score['IEP_AANTAL'] == 0) & (data_score['IEP_GEM'] > 0)) |
                      ((data_score['IEP_AANTAL'] > 0) & (data_score['IEP_GEM'] == 0))]
print(wrong_IEP.shape)
remove_wrong_data(wrong_IEP)


print(data_score.shape)
```

    (537, 29)
    (68, 29)
    (10, 29)
    (0, 29)
    (12, 29)
    (115, 29)
    (208, 29)
    (31303, 29)


Now I want to see how many schools choose different type of exams and is there a lot of schools which choose more then two kind of test: 


```python
schools_more_then_2_exam = data_score.copy()

def count_exams(row):
    count = 1 if row.CET_AANTAL > 0 else 0
    count = count + 1 if row.DREMPEL_AANTAL > 0 else count
    count = count + 1 if row.AMN_AANTAL > 0 else count
    count = count + 1 if row.CESAN_AANTAL > 0 else count
    count = count + 1 if row.DIA_AANTAL > 0 else count
    count = count + 1 if row.ROUTE8_AANTAL > 0 else count
    count = count + 1 if row.IEP_AANTAL > 0 else count
    return count

    
schools_more_then_2_exam['EXAMEN_AANTAL'] = schools_more_then_2_exam.apply(lambda row: count_exams(row), axis = 1) 

schools_more_then_2_exam = schools_more_then_2_exam[schools_more_then_2_exam['EXAMEN_AANTAL'] > 2]
print(schools_more_then_2_exam.shape)
schools_more_then_2_exam.head()
```

    (3, 30)





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
      <th>PEILDATUM_LEERLINGEN</th>
      <th>DATUM</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>...</th>
      <th>ROUTE8_GEM</th>
      <th>DREMPEL_AANTAL</th>
      <th>drempel_gem</th>
      <th>DIA_AANTAL</th>
      <th>DIA_GEM</th>
      <th>CESAN_AANTAL</th>
      <th>CESAN_GEM</th>
      <th>AMN_AANTAL</th>
      <th>AMN_GEM</th>
      <th>EXAMEN_AANTAL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18551</th>
      <td>20170415</td>
      <td>2017</td>
      <td>15YU</td>
      <td>0</td>
      <td>OBS Holy</td>
      <td>3137WD</td>
      <td>VLAARDINGEN</td>
      <td>622</td>
      <td>Vlaardingen</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>184.600000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>25227</th>
      <td>20180417</td>
      <td>2018</td>
      <td>15YU</td>
      <td>0</td>
      <td>OBS Holy</td>
      <td>3137WD</td>
      <td>VLAARDINGEN</td>
      <td>622</td>
      <td>Vlaardingen</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>208.166667</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>31810</th>
      <td>20190417</td>
      <td>2019</td>
      <td>15YU</td>
      <td>0</td>
      <td>OBS Holy</td>
      <td>3137WD</td>
      <td>VLAARDINGEN</td>
      <td>622</td>
      <td>Vlaardingen</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>191.200000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
<p>3 rows × 30 columns</p>
</div>



There are few rows with more then 2 type of test chosen. Let's just remove it, then it will be possible to make a column of chosen exams in each school and remove all unnecessary columns:


```python
#remove rows with more then 2 choosen type of tests
print(data_score.shape)
data_score.drop(schools_more_then_2_exam.index, 0,  inplace = True)
print(data_score.shape)
```

    (31303, 29)
    (31300, 29)



```python
# make columns for the test with biggest amount of pupils who took it
def most_common_exam(row):
    exam_type, amount, score = '', 0, 0
    if row.CET_AANTAL > 0:
        exam_type,amount,score ='CET', row.CET_AANTAL, row.CET_GEM
    if row.DREMPEL_AANTAL > amount:
        exam_type,amount,score = 'DREMPEL', row.DREMPEL_AANTAL, row.drempel_gem
    if row.AMN_AANTAL > amount:
        exam_type,amount,score = 'AMN', row.AMN_AANTAL, row.AMN_GEM
    if row.CESAN_AANTAL > amount:
        exam_type,amount,score = 'CESAN', row.CESAN_AANTAL, row.CESAN_GEM
    if row.DIA_AANTAL > amount:
        exam_type,amount,score = 'DIA', row.DIA_AANTAL, row.DIA_GEM
    if row.ROUTE8_AANTAL > amount:
        exam_type,amount,score = 'ROUTE8', row.ROUTE8_AANTAL, row.ROUTE8_GEM
    if row.IEP_AANTAL > amount:
        exam_type,amount,score = 'IEP', row.IEP_AANTAL, row.IEP_GEM
    return exam_type,amount,score

data_score['EXAMEN_1'], data_score['EXAMEN_1_AANTAL'], data_score['EXAMEN_1_GEM'] = zip(*data_score.apply(lambda row: most_common_exam(row), axis = 1))
data_score.head()
```




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
      <th>PEILDATUM_LEERLINGEN</th>
      <th>DATUM</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>...</th>
      <th>drempel_gem</th>
      <th>DIA_AANTAL</th>
      <th>DIA_GEM</th>
      <th>CESAN_AANTAL</th>
      <th>CESAN_GEM</th>
      <th>AMN_AANTAL</th>
      <th>AMN_GEM</th>
      <th>EXAMEN_1</th>
      <th>EXAMEN_1_AANTAL</th>
      <th>EXAMEN_1_GEM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20141001</td>
      <td>2015</td>
      <td>00AP</td>
      <td>0</td>
      <td>De Schanskorf</td>
      <td>2715BT</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>CET</td>
      <td>11</td>
      <td>534.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20141001</td>
      <td>2015</td>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6109AM</td>
      <td>OHE EN LAAK</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>CET</td>
      <td>18</td>
      <td>539.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20141001</td>
      <td>2015</td>
      <td>00AZ</td>
      <td>0</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>CET</td>
      <td>22</td>
      <td>532.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20141001</td>
      <td>2015</td>
      <td>00BA</td>
      <td>0</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>CET</td>
      <td>11</td>
      <td>536.2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>20141001</td>
      <td>2015</td>
      <td>00BB</td>
      <td>0</td>
      <td>Obs Letterwies</td>
      <td>9944AR</td>
      <td>NIEUWOLDA</td>
      <td>1895</td>
      <td>Oldambt</td>
      <td>Groningen</td>
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>CET</td>
      <td>16</td>
      <td>531.3</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
# make columns for the second chosen test
def get_second_exam(row):
    if (row.CET_AANTAL > 0) & (row.EXAMEN_1 != 'CET'):
        return 'CET', row.CET_AANTAL, row.CET_GEM
    if (row.DREMPEL_AANTAL > 0) & (row.EXAMEN_1 != 'DREMPEL'):
        return 'DREMPEL', row.DREMPEL_AANTAL, row.drempel_gem
    if (row.AMN_AANTAL > 0) & (row.EXAMEN_1 != 'AMN'):
        return 'AMN', row.AMN_AANTAL, row.AMN_GEM
    if (row.CESAN_AANTAL > 0) & (row.EXAMEN_1 != 'CESAN'):
        return 'CESAN', row.CESAN_AANTAL, row.CESAN_GEM
    if (row.DIA_AANTAL > 0) & (row.EXAMEN_1 != 'DIA'):
        return 'DIA', row.DIA_AANTAL, row.DIA_GEM
    if (row.ROUTE8_AANTAL > 0) & (row.EXAMEN_1 != 'ROUTE8'):
        return 'ROUTE8', row.ROUTE8_AANTAL, row.ROUTE8_GEM
    if (row.IEP_AANTAL > 0) & (row.EXAMEN_1 != 'IEP'):
        return 'IEP', row.IEP_AANTAL, row.IEP_GEM
    return '', 0, 0

data_score['EXAMEN_2'], data_score['EXAMEN_2_AANTAL'], data_score['EXAMEN_2_GEM'] = zip(*data_score.apply(lambda row: get_second_exam(row), axis = 1))
data_score['EXAMEN_2'].value_counts()
```




               31151
    CET           55
    IEP           55
    ROUTE8        18
    DREMPEL       11
    DIA            6
    AMN            4
    Name: EXAMEN_2, dtype: int64



We can see that most of the schools in dataset (31151) has one chosen type of exam, so we can remove new columns **EXAMEN_2_...** because it has relatively small information.

Next we also need to make a new field which values would be unique for each school (and which we would use later to merge different dataframes). I think if we pool **VESTIGINGSNUMMER**, **BRIN_NUMMER** and **DATUM** columns, we would have unique value for each row:


```python
new_column = data_score["VESTIGINGSNUMMER"].astype(str) + data_score["BRIN_NUMMER"] + "_" + data_score["DATUM"].astype(str) 
data_score.insert (0, "SCHOOL_ID", new_column)
```


```python
print(data_score.shape)
print(data_score.columns)
```

    (31300, 36)
    Index(['SCHOOL_ID', 'PEILDATUM_LEERLINGEN', 'DATUM', 'BRIN_NUMMER',
           'VESTIGINGSNUMMER', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'LEERJAAR_8',
           'ONTHEFFING_REDEN_ND', 'CET_AANTAL', 'CET_GEM', 'IEP_AANTAL', 'IEP_GEM',
           'ROUTE8_AANTAL', 'ROUTE8_GEM', 'DREMPEL_AANTAL', 'drempel_gem',
           'DIA_AANTAL', 'DIA_GEM', 'CESAN_AANTAL', 'CESAN_GEM', 'AMN_AANTAL',
           'AMN_GEM', 'EXAMEN_1', 'EXAMEN_1_AANTAL', 'EXAMEN_1_GEM', 'EXAMEN_2',
           'EXAMEN_2_AANTAL', 'EXAMEN_2_GEM'],
          dtype='object')


And last (but not least) what we should do - remove all unnecessary columns:


```python
data_score.drop('PEILDATUM_LEERLINGEN', 1, inplace = True)
data_score.drop('BRIN_NUMMER', 1, inplace = True)
data_score.drop('VESTIGINGSNUMMER', 1, inplace = True)
data_score.drop('LEERJAAR_8', 1, inplace = True)
data_score.drop('ONTHEFFING_REDEN_ND', 1, inplace = True)
data_score.drop('CET_AANTAL', 1, inplace = True)
data_score.drop('CET_GEM', 1, inplace = True)
data_score.drop('IEP_AANTAL', 1, inplace = True)
data_score.drop('IEP_GEM', 1, inplace = True)
data_score.drop('ROUTE8_AANTAL', 1, inplace = True)
data_score.drop('ROUTE8_GEM', 1, inplace = True)
data_score.drop('DREMPEL_AANTAL', 1, inplace = True)
data_score.drop('drempel_gem', 1, inplace = True)
data_score.drop('DIA_AANTAL', 1, inplace = True)
data_score.drop('DIA_GEM', 1, inplace = True)
data_score.drop('CESAN_AANTAL', 1, inplace = True)
data_score.drop('CESAN_GEM', 1, inplace = True)
data_score.drop('AMN_AANTAL', 1, inplace = True)
data_score.drop('AMN_GEM', 1, inplace = True)
data_score.drop('EXAMEN_2', 1, inplace = True)
data_score.drop('EXAMEN_2_AANTAL', 1, inplace = True)
data_score.drop('EXAMEN_2_GEM', 1, inplace = True)
data_score.rename(columns = {'EXAMEN_1': 'EXAMEN', 'EXAMEN_1_AANTAL': 'EXAMEN_AANTAL', 'EXAMEN_1_GEM':'EXAMEN_GEM', 'Leerjaar_8':'LEERJAAR_8'}, inplace = True)
```


```python
print(data_score.shape)
data_score.head()
```

    (31300, 14)





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
      <th>SCHOOL_ID</th>
      <th>DATUM</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>SOORT_PO</th>
      <th>DENOMINATIE_VESTIGING</th>
      <th>BEVOEGD_GEZAG_NUMMER</th>
      <th>EXAMEN</th>
      <th>EXAMEN_AANTAL</th>
      <th>EXAMEN_GEM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>000AP_2015</td>
      <td>2015</td>
      <td>De Schanskorf</td>
      <td>2715BT</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>41646</td>
      <td>CET</td>
      <td>11</td>
      <td>534.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>000AR_2015</td>
      <td>2015</td>
      <td>BS "De Maasparel"</td>
      <td>6109AM</td>
      <td>OHE EN LAAK</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>Bo</td>
      <td>Rooms-Katholiek</td>
      <td>41797</td>
      <td>CET</td>
      <td>18</td>
      <td>539.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>000AZ_2015</td>
      <td>2015</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>41638</td>
      <td>CET</td>
      <td>22</td>
      <td>532.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>000BA_2015</td>
      <td>2015</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>41530</td>
      <td>CET</td>
      <td>11</td>
      <td>536.2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>000BB_2015</td>
      <td>2015</td>
      <td>Obs Letterwies</td>
      <td>9944AR</td>
      <td>NIEUWOLDA</td>
      <td>1895</td>
      <td>Oldambt</td>
      <td>Groningen</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>41613</td>
      <td>CET</td>
      <td>16</td>
      <td>531.3</td>
    </tr>
  </tbody>
</table>
</div>



First part of our dataset constraction has done, so le't write results down to temporary .csv file:


```python
data_score.to_csv(r'score_first_result.csv', index = False)
```

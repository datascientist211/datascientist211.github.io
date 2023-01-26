---

title: Dutch schools dataset construction
description: " "

---


This article is the second part of dataset construction. I'm going to add here some new data to the dataset. It will be info about amount of students on different levels of knowledge in language and arithmetics and school advices for secondary education.

All the data for this part of dataset construction I got here: 
1. Student's reference level of language and arithmetic: https://duo.nl/open_onderwijsdata/databestanden/po/leerlingen-po/bo-sbo/refniveau.jsp
2. School advices: https://duo.nl/open_onderwijsdata/databestanden/po/leerlingen-po/bo-sbo/schooladviezen.jsp

## 2. Students level in language and arithmetic

First we need to get data from temporary .cvs file which I created on previous step:


```python
import pandas as pd 
data_score = pd.read_csv("score_first_result.csv", error_bad_lines=False, sep=',', encoding = "ISO-8859-1") 

print(data_score.shape)
print(data_score.columns)
data_score.head()
```

    (31300, 14)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'EXAMEN',
           'EXAMEN_AANTAL', 'EXAMEN_GEM'],
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
      <th>2</th>
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
      <th>3</th>
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
      <th>4</th>
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



Now I will join together a few .cvs files which we will add to dataset after some data transformation and cleaning:


```python
data_subject_score_2015 = pd.read_csv("input_data/Arithmetic_language_score_2015_2016.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_subject_score_2016 = pd.read_csv("input_data/Arithmetic_language_score_2016_2017.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_subject_score_2017 = pd.read_csv("input_data/Arithmetic_language_score_2017_2018.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_subject_score_2018 = pd.read_csv("input_data/Arithmetic_language_score_2018_2019.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 

print(data_subject_score_2018.shape)
data_subject_score_2018.head()
```

    (6304, 22)





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
      <th>PEILDATUM</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>SOORT_PO</th>
      <th>...</th>
      <th>REKENEN_LAGER1F</th>
      <th>REKENEN_1F</th>
      <th>REKENEN_1S</th>
      <th>REKENEN_2F</th>
      <th>LV_LAGER1F</th>
      <th>LV_1F</th>
      <th>LV_2F</th>
      <th>TV_LAGER1F</th>
      <th>TV_1F</th>
      <th>TV_2F</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20190417</td>
      <td>00AP</td>
      <td>0</td>
      <td>Wereldwijs</td>
      <td>2716PH</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>...</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>9</td>
      <td>0</td>
      <td>2</td>
      <td>9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20190417</td>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6107AW</td>
      <td>STEVENSWEERT</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>Bo</td>
      <td>...</td>
      <td>2</td>
      <td>4</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>16</td>
      <td>0</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20190417</td>
      <td>00AV</td>
      <td>0</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>1930</td>
      <td>Nissewaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>...</td>
      <td>1</td>
      <td>7</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>10</td>
      <td>2</td>
      <td>4</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20190417</td>
      <td>00AZ</td>
      <td>0</td>
      <td>De Stapsteen</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>...</td>
      <td>2</td>
      <td>3</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>11</td>
      <td>0</td>
      <td>3</td>
      <td>11</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20190417</td>
      <td>00BA</td>
      <td>0</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>Bo</td>
      <td>...</td>
      <td>2</td>
      <td>5</td>
      <td>9</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>14</td>
      <td>2</td>
      <td>4</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>



Need to merge everything in one dataframe:


```python
data_frames = [data_subject_score_2015, data_subject_score_2016, data_subject_score_2017, data_subject_score_2018]
data_subject_score = pd.concat(data_frames).fillna(' ')
data_subject_score.reset_index(drop=True, inplace=True)
print(data_subject_score_2015.shape)
print(data_subject_score_2016.shape)
print(data_subject_score_2017.shape)
print(data_subject_score_2018.shape)
print(data_subject_score.shape)
data_subject_score.head()
```

    (6558, 22)
    (6463, 22)
    (6381, 22)
    (6304, 22)
    (25706, 22)





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
      <th>PEILDATUM</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>SOORT_PO</th>
      <th>...</th>
      <th>REKENEN_LAGER1F</th>
      <th>REKENEN_1F</th>
      <th>REKENEN_1S</th>
      <th>REKENEN_2F</th>
      <th>LV_LAGER1F</th>
      <th>LV_1F</th>
      <th>LV_2F</th>
      <th>TV_LAGER1F</th>
      <th>TV_1F</th>
      <th>TV_2F</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20160419</td>
      <td>00AP</td>
      <td>0</td>
      <td>De Schanskorf</td>
      <td>2715BT</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>...</td>
      <td>2</td>
      <td>4</td>
      <td>6</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>10</td>
      <td>0</td>
      <td>4</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20160419</td>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6107AW</td>
      <td>STEVENSWEERT</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>Bo</td>
      <td>...</td>
      <td>0</td>
      <td>7</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>16</td>
      <td>1</td>
      <td>3</td>
      <td>13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20160419</td>
      <td>00AV</td>
      <td>0</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>1930</td>
      <td>Nissewaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>...</td>
      <td>3</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>4</td>
      <td>2</td>
      <td>3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20160419</td>
      <td>00AZ</td>
      <td>0</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>...</td>
      <td>2</td>
      <td>8</td>
      <td>1</td>
      <td>8</td>
      <td>0</td>
      <td>5</td>
      <td>14</td>
      <td>0</td>
      <td>4</td>
      <td>15</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20160419</td>
      <td>00BA</td>
      <td>0</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>Bo</td>
      <td>...</td>
      <td>1</td>
      <td>10</td>
      <td>2</td>
      <td>11</td>
      <td>0</td>
      <td>8</td>
      <td>16</td>
      <td>2</td>
      <td>4</td>
      <td>18</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>



And take a look at columns types to understand do we (probably) have some empty/missing/unappropriate values in numeric columns:


```python
data_subject_score.dtypes
```




    PEILDATUM                     int64
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
    REKENEN_LAGER1F               int64
    REKENEN_1F                    int64
    REKENEN_1S                    int64
    REKENEN_2F                    int64
    LV_LAGER1F                    int64
    LV_1F                         int64
    LV_2F                         int64
    TV_LAGER1F                    int64
    TV_1F                         int64
    TV_2F                         int64
    dtype: object



You can see that dataset has all the "levels" columns as int type, I think it means we don't have unexpected values in this columns (like string or empty column), it couldn't be parsed to int otherwise. But still let's check null and NaN values in dataset:


```python
print(data_subject_score.isnull().sum())
print(data_subject_score.isna().sum())
```

    PEILDATUM                    0
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
    REKENEN_LAGER1F              0
    REKENEN_1F                   0
    REKENEN_1S                   0
    REKENEN_2F                   0
    LV_LAGER1F                   0
    LV_1F                        0
    LV_2F                        0
    TV_LAGER1F                   0
    TV_1F                        0
    TV_2F                        0
    dtype: int64
    PEILDATUM                    0
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
    REKENEN_LAGER1F              0
    REKENEN_1F                   0
    REKENEN_1S                   0
    REKENEN_2F                   0
    LV_LAGER1F                   0
    LV_1F                        0
    LV_2F                        0
    TV_LAGER1F                   0
    TV_1F                        0
    TV_2F                        0
    dtype: int64


Let's make unique columns **SCHOOL_ID**:


```python
data_subject_score['PEILDATUM'].value_counts()
```




    20160419    6558
    20170415    6463
    20180417    6381
    20190417    6304
    Name: PEILDATUM, dtype: int64




```python
data_subject_score['PEILDATUM'] = data_subject_score['PEILDATUM'].map({
    20160419 : 2016,
    20170415 : 2017,
    20180417 : 2018,
    20190417 : 2019,
})
data_subject_score.rename(columns = {'PEILDATUM': 'DATUM'}, inplace = True)
data_subject_score['DATUM'].value_counts()
```




    2016    6558
    2017    6463
    2018    6381
    2019    6304
    Name: DATUM, dtype: int64




```python
new_column = data_subject_score["VESTIGINGSNUMMER"].astype(str) + data_subject_score["BRIN_NUMMER"] + "_" + data_subject_score["DATUM"].astype(str) 
data_subject_score.insert (0, "SCHOOL_ID", new_column)
data_subject_score.drop('VESTIGINGSNUMMER', 1, inplace = True)
data_subject_score.drop('BRIN_NUMMER', 1, inplace = True)
```


```python
print(data_subject_score.shape)
print(data_subject_score.columns)
data_subject_score.head()
```

    (25706, 21)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'REKENEN_LAGER1F',
           'REKENEN_1F', 'REKENEN_1S', 'REKENEN_2F', 'LV_LAGER1F', 'LV_1F',
           'LV_2F', 'TV_LAGER1F', 'TV_1F', 'TV_2F'],
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
      <th>...</th>
      <th>REKENEN_LAGER1F</th>
      <th>REKENEN_1F</th>
      <th>REKENEN_1S</th>
      <th>REKENEN_2F</th>
      <th>LV_LAGER1F</th>
      <th>LV_1F</th>
      <th>LV_2F</th>
      <th>TV_LAGER1F</th>
      <th>TV_1F</th>
      <th>TV_2F</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>000AP_2016</td>
      <td>2016</td>
      <td>De Schanskorf</td>
      <td>2715BT</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>...</td>
      <td>2</td>
      <td>4</td>
      <td>6</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>10</td>
      <td>0</td>
      <td>4</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>000AR_2016</td>
      <td>2016</td>
      <td>BS "De Maasparel"</td>
      <td>6107AW</td>
      <td>STEVENSWEERT</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>Bo</td>
      <td>Rooms-Katholiek</td>
      <td>...</td>
      <td>0</td>
      <td>7</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>16</td>
      <td>1</td>
      <td>3</td>
      <td>13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>000AV_2016</td>
      <td>2016</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>1930</td>
      <td>Nissewaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>...</td>
      <td>3</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>4</td>
      <td>2</td>
      <td>3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>000AZ_2016</td>
      <td>2016</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>...</td>
      <td>2</td>
      <td>8</td>
      <td>1</td>
      <td>8</td>
      <td>0</td>
      <td>5</td>
      <td>14</td>
      <td>0</td>
      <td>4</td>
      <td>15</td>
    </tr>
    <tr>
      <th>4</th>
      <td>000BA_2016</td>
      <td>2016</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>1734</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>...</td>
      <td>1</td>
      <td>10</td>
      <td>2</td>
      <td>11</td>
      <td>0</td>
      <td>8</td>
      <td>16</td>
      <td>2</td>
      <td>4</td>
      <td>18</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 21 columns</p>
</div>



And remove all unnesessary columns which we had already have in data_score:


```python
columns = []
for column in data_subject_score.columns:
    if((column in data_score.columns) & (column != 'SCHOOL_ID')):
        columns.append(column)

print(columns)
```

    ['DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING', 'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO', 'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER']



```python
print(data_subject_score.shape)
data_subject_score.drop(columns, 1, inplace = True)
print(data_subject_score.shape)
print(data_subject_score.columns)
```

    (25706, 21)
    (25706, 11)
    Index(['SCHOOL_ID', 'REKENEN_LAGER1F', 'REKENEN_1F', 'REKENEN_1S',
           'REKENEN_2F', 'LV_LAGER1F', 'LV_1F', 'LV_2F', 'TV_LAGER1F', 'TV_1F',
           'TV_2F'],
          dtype='object')


And finally merge data_score and data_subject_score:


```python
data = pd.merge(data_score, data_subject_score, on='SCHOOL_ID',  how='left').fillna(0)
print(data.shape)
print(data.columns)
data.head()
```

    (31300, 24)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'EXAMEN',
           'EXAMEN_AANTAL', 'EXAMEN_GEM', 'REKENEN_LAGER1F', 'REKENEN_1F',
           'REKENEN_1S', 'REKENEN_2F', 'LV_LAGER1F', 'LV_1F', 'LV_2F',
           'TV_LAGER1F', 'TV_1F', 'TV_2F'],
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
      <th>...</th>
      <th>REKENEN_LAGER1F</th>
      <th>REKENEN_1F</th>
      <th>REKENEN_1S</th>
      <th>REKENEN_2F</th>
      <th>LV_LAGER1F</th>
      <th>LV_1F</th>
      <th>LV_2F</th>
      <th>TV_LAGER1F</th>
      <th>TV_1F</th>
      <th>TV_2F</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
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
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 24 columns</p>
</div>



### School advices about secondary education

First let's read all the school advices datasets, merge their together and check missing values:


```python
data_school_advice_2014 = pd.read_csv("input_data/School_advice_2014_2015.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_school_advice_2015 = pd.read_csv("input_data/School_advice_2015_2016.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_school_advice_2016 = pd.read_csv("input_data/School_advice_2016_2017.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_school_advice_2017 = pd.read_csv("input_data/School_advice_2017_2018.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 
data_school_advice_2018 = pd.read_csv("input_data/School_advice_2018_2019.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 

print(data_school_advice_2018.shape)
data_school_advice_2018.head()
```

    (6585, 26)





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
      <th>PRIKDATUM_ADVIEZEN</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>...</th>
      <th>VMBO_B_K</th>
      <th>VMBO_K</th>
      <th>VMBO_K_GT</th>
      <th>VMBO_GT</th>
      <th>VMBO_GT_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
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
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
      <td>3</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>11</td>
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
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>8</td>
      <td>0</td>
      <td>17</td>
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
      <td>2</td>
      <td>0</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>12</td>
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
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>3</td>
      <td>0</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>14</td>
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
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>16</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>




```python
data_frames = [data_school_advice_2014, data_school_advice_2015, data_school_advice_2016, data_school_advice_2017,data_school_advice_2018]
data_school_advice = pd.concat(data_frames).fillna(' ')
data_school_advice.reset_index(drop=True, inplace=True)
print(data_school_advice_2014.shape)
print(data_school_advice_2015.shape)
print(data_school_advice_2016.shape)
print(data_school_advice_2017.shape)
print(data_school_advice_2018.shape)
print(data_school_advice.shape)
data_school_advice.head()
```

    (6920, 26)
    (6853, 26)
    (6754, 26)
    (6662, 26)
    (6585, 26)
    (33774, 26)





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
      <th>PRIKDATUM_ADVIEZEN</th>
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>...</th>
      <th>VMBO_B_K</th>
      <th>VMBO_K</th>
      <th>VMBO_K_GT</th>
      <th>VMBO_GT</th>
      <th>VMBO_GT_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20141001</td>
      <td>20150722</td>
      <td>00AP</td>
      <td>0</td>
      <td>De Schanskorf</td>
      <td>2715BT</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20141001</td>
      <td>20150722</td>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6109AM</td>
      <td>OHE EN LAAK</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20141001</td>
      <td>20150722</td>
      <td>00AV</td>
      <td>0</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>612</td>
      <td>Spijkenisse</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20141001</td>
      <td>20150722</td>
      <td>00AZ</td>
      <td>0</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1927</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>...</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20141001</td>
      <td>20150722</td>
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
      <td>1</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>




```python
print(data_school_advice.dtypes)
print(data_school_advice.isnull().sum())
print(data_school_advice.isna().sum())
```

    PEILDATUM_LEERLINGEN          int64
    PRIKDATUM_ADVIEZEN            int64
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
    VSO                           int64
    PRO                           int64
    VMBO_B                        int64
    VMBO_B_K                      int64
    VMBO_K                        int64
    VMBO_K_GT                     int64
    VMBO_GT                       int64
    VMBO_GT_HAVO                  int64
    HAVO                          int64
    HAVO_VWO                      int64
    VWO                           int64
    ADVIES_NIET_MOGELIJK          int64
    TOTAAL_ADVIES                 int64
    dtype: object
    PEILDATUM_LEERLINGEN         0
    PRIKDATUM_ADVIEZEN           0
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
    VSO                          0
    PRO                          0
    VMBO_B                       0
    VMBO_B_K                     0
    VMBO_K                       0
    VMBO_K_GT                    0
    VMBO_GT                      0
    VMBO_GT_HAVO                 0
    HAVO                         0
    HAVO_VWO                     0
    VWO                          0
    ADVIES_NIET_MOGELIJK         0
    TOTAAL_ADVIES                0
    dtype: int64
    PEILDATUM_LEERLINGEN         0
    PRIKDATUM_ADVIEZEN           0
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
    VSO                          0
    PRO                          0
    VMBO_B                       0
    VMBO_B_K                     0
    VMBO_K                       0
    VMBO_K_GT                    0
    VMBO_GT                      0
    VMBO_GT_HAVO                 0
    HAVO                         0
    HAVO_VWO                     0
    VWO                          0
    ADVIES_NIET_MOGELIJK         0
    TOTAAL_ADVIES                0
    dtype: int64


Everything looks fine.
Next we need to make unique columns **SCHOOL_ID**:


```python
data_school_advice['PRIKDATUM_ADVIEZEN'].value_counts()
```




    20150722    6920
    20160722    6853
    20170722    6754
    20180725    6662
    20190725    6585
    Name: PRIKDATUM_ADVIEZEN, dtype: int64




```python
data_school_advice['PRIKDATUM_ADVIEZEN'] = data_school_advice['PRIKDATUM_ADVIEZEN'].map({
    20150722 : 2015,
    20160722 : 2016,
    20170722 : 2017,
    20180725 : 2018,
    20190725 : 2019,
})
data_school_advice.rename(columns = {'PRIKDATUM_ADVIEZEN': 'DATUM'}, inplace = True)
data_school_advice['DATUM'].value_counts()
```




    2015    6920
    2016    6853
    2017    6754
    2018    6662
    2019    6585
    Name: DATUM, dtype: int64




```python
new_column = data_school_advice["VESTIGINGSNUMMER"].astype(str) + data_school_advice["BRIN_NUMMER"] + "_" + data_school_advice["DATUM"].astype(str) 
data_school_advice.insert (0, "SCHOOL_ID", new_column)
data_school_advice.drop('VESTIGINGSNUMMER', 1, inplace = True)
data_school_advice.drop('BRIN_NUMMER', 1, inplace = True)
data_school_advice.drop('PEILDATUM_LEERLINGEN', 1, inplace = True)
```


```python
print(data_school_advice.shape)
print(data_school_advice.columns)
data_school_advice.head()
```

    (33774, 24)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'VSO', 'PRO', 'VMBO_B',
           'VMBO_B_K', 'VMBO_K', 'VMBO_K_GT', 'VMBO_GT', 'VMBO_GT_HAVO', 'HAVO',
           'HAVO_VWO', 'VWO', 'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES'],
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
      <th>...</th>
      <th>VMBO_B_K</th>
      <th>VMBO_K</th>
      <th>VMBO_K_GT</th>
      <th>VMBO_GT</th>
      <th>VMBO_GT_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
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
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
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
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2</th>
      <td>000AV_2015</td>
      <td>2015</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>612</td>
      <td>Spijkenisse</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
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
      <td>...</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
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
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 24 columns</p>
</div>



Deleting common (with data_score) columns:


```python
columns = []
for column in data_school_advice.columns:
    if((column in data_score.columns) & (column != 'SCHOOL_ID')):
        columns.append(column)
print(columns)
```

    ['DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING', 'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO', 'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER']



```python
print(data_school_advice.shape)
data_school_advice.drop(columns, 1, inplace = True)
print(data_school_advice.shape)
print(data_school_advice.columns)
```

    (33774, 24)
    (33774, 14)
    Index(['SCHOOL_ID', 'VSO', 'PRO', 'VMBO_B', 'VMBO_B_K', 'VMBO_K', 'VMBO_K_GT',
           'VMBO_GT', 'VMBO_GT_HAVO', 'HAVO', 'HAVO_VWO', 'VWO',
           'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES'],
          dtype='object')


We have a lot of different type of VMBO advices, they are not that important to us, we would like just to know 'big' level of education student was adviced for. Let's pool all the columns of different VMBO-types into one:


```python
VMBO_column = data_school_advice["VMBO_B"] + data_school_advice["VMBO_B_K"] + data_school_advice["VMBO_K"]
+ data_school_advice["VMBO_K_GT"] + data_school_advice["VMBO_GT"]
data_school_advice.insert (3, "VMBO", VMBO_column)
data_school_advice.drop('VMBO_B', 1, inplace = True)
data_school_advice.drop('VMBO_B_K', 1, inplace = True)
data_school_advice.drop('VMBO_K', 1, inplace = True)
data_school_advice.drop('VMBO_K_GT', 1, inplace = True)
data_school_advice.drop('VMBO_GT', 1, inplace = True)

data_school_advice.rename(columns = {'VMBO_GT_HAVO': 'VMBO_HAVO'}, inplace = True)
```


```python
print(data_school_advice.shape)
print(data_school_advice.columns)
data_school_advice.head()
```

    (33774, 10)
    Index(['SCHOOL_ID', 'VSO', 'PRO', 'VMBO', 'VMBO_HAVO', 'HAVO', 'HAVO_VWO',
           'VWO', 'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES'],
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
      <th>SCHOOL_ID</th>
      <th>VSO</th>
      <th>PRO</th>
      <th>VMBO</th>
      <th>VMBO_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>000AP_2015</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>000AR_2015</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2</th>
      <td>000AV_2015</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>000AZ_2015</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>000BA_2015</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
</div>



Merge data and data_school_advice:


```python
data = pd.merge(data, data_school_advice, on='SCHOOL_ID',  how='left').fillna(0)
print(data.shape)
print(data.columns)
data.head()
```

    (31300, 33)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'EXAMEN',
           'EXAMEN_AANTAL', 'EXAMEN_GEM', 'REKENEN_LAGER1F', 'REKENEN_1F',
           'REKENEN_1S', 'REKENEN_2F', 'LV_LAGER1F', 'LV_1F', 'LV_2F',
           'TV_LAGER1F', 'TV_1F', 'TV_2F', 'VSO', 'PRO', 'VMBO', 'VMBO_HAVO',
           'HAVO', 'HAVO_VWO', 'VWO', 'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES'],
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
      <th>...</th>
      <th>TV_2F</th>
      <th>VSO</th>
      <th>PRO</th>
      <th>VMBO</th>
      <th>VMBO_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
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
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
    </tr>
    <tr>
      <th>3</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>4</th>
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
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>16</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 33 columns</p>
</div>



Do all the columns has the type they should?


```python
data.dtypes
```




    SCHOOL_ID                     object
    DATUM                          int64
    INSTELLINGSNAAM_VESTIGING     object
    POSTCODE_VESTIGING            object
    PLAATSNAAM                    object
    GEMEENTENUMMER                 int64
    GEMEENTENAAM                  object
    PROVINCIE                     object
    SOORT_PO                      object
    DENOMINATIE_VESTIGING         object
    BEVOEGD_GEZAG_NUMMER           int64
    EXAMEN                        object
    EXAMEN_AANTAL                  int64
    EXAMEN_GEM                   float64
    REKENEN_LAGER1F              float64
    REKENEN_1F                   float64
    REKENEN_1S                   float64
    REKENEN_2F                   float64
    LV_LAGER1F                   float64
    LV_1F                        float64
    LV_2F                        float64
    TV_LAGER1F                   float64
    TV_1F                        float64
    TV_2F                        float64
    VSO                            int64
    PRO                            int64
    VMBO                           int64
    VMBO_HAVO                      int64
    HAVO                           int64
    HAVO_VWO                       int64
    VWO                            int64
    ADVIES_NIET_MOGELIJK           int64
    TOTAAL_ADVIES                  int64
    dtype: object



I see that  after merging columns **REKENEN_LAGER1F**, **REKENEN_1F**, **REKENEN_1S**, **REKENEN_2F**, **LV_LAGER1F**, **LV_1F**, **LV_2F**, **TV_LAGER1F**, **TV_1F**, **TV_2F** has type float64, but they should be int, so let's convert them:


```python
data["REKENEN_LAGER1F"] = data["REKENEN_LAGER1F"].astype(int)
data["REKENEN_1F"] = data["REKENEN_1F"].astype(int)
data["REKENEN_1S"] = data["REKENEN_1S"].astype(int)
data["REKENEN_2F"] = data["REKENEN_2F"].astype(int)
data["LV_LAGER1F"] = data["LV_LAGER1F"].astype(int)
data["LV_1F"] = data["LV_1F"].astype(int)
data["LV_2F"] = data["LV_2F"].astype(int)
data["TV_LAGER1F"] = data["TV_LAGER1F"].astype(int)
data["TV_1F"] = data["TV_1F"].astype(int)
data["TV_2F"] = data["TV_2F"].astype(int)
```

Finally, let's write our result dataset down to .csv file:


```python
data.to_csv(r'score_second_result.csv', index = False)
```

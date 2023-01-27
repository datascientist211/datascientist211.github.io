---

title: Dutch schools dataset construction

---

This article is the third part of dataset construction. Here I want to add new data into dataset I got on previous steps. This time it will be info students who wasn't able to make it on time and stayed in primary school longer then others

All the data for this part of dataset construction I got here: https://duo.nl/open_onderwijsdata/databestanden/po/leerlingen-po/bo-sbo/zittenblijven.jsp


## 3. Students who stayed in primary school longer.

Read .csv file of new data:


```python
import pandas as pd 
data_raw = pd.read_csv("input_data/Students_stayed_longer_2013_2019.csv", error_bad_lines=False, sep=';', encoding = "ISO-8859-1") 

print(data_raw.shape)
print(data_raw.columns)
data_raw.head() 
```

    (6910, 39)
    Index(['BRIN_NUMMER', 'VESTIGINGSNUMMER', 'INSTELLINGSNAAM_VESTIGING',
           'POSTCODE_VESTIGING', 'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM',
           'PROVINCIE', 'SOORT_PO', 'DENOMINATIE_VESTIGING',
           'BEVOEGD_GEZAG_NUMMER', 'LJ8_13', 'ZIT_13', 'ZITPERC_13',
           'ZITPERC_LJ_13', 'LJ8_14', 'ZIT_14', 'ZITPERC_14', 'ZITPERC_LJ_14',
           'LJ8_15', 'ZIT_15', 'ZITPERC_15', 'ZITPERC_LJ_15', 'LJ8_16', 'ZIT_16',
           'ZITPERC_16', 'ZITPERC_LJ_16', 'LJ8_17', 'ZIT_17', 'ZITPERC_17',
           'ZITPERC_LJ_17', 'LJ8_18', 'ZIT_18', 'ZITPERC_18', 'ZITPERC_LJ_18',
           'LJ8_19', 'ZIT_19', 'ZITPERC_19', 'ZITPERC_LJ_19'],
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
      <th>BRIN_NUMMER</th>
      <th>VESTIGINGSNUMMER</th>
      <th>INSTELLINGSNAAM_VESTIGING</th>
      <th>POSTCODE_VESTIGING</th>
      <th>PLAATSNAAM</th>
      <th>GEMEENTENUMMER</th>
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>SOORT_PO</th>
      <th>DENOMINATIE_VESTIGING</th>
      <th>...</th>
      <th>ZITPERC_17</th>
      <th>ZITPERC_LJ_17</th>
      <th>LJ8_18</th>
      <th>ZIT_18</th>
      <th>ZITPERC_18</th>
      <th>ZITPERC_LJ_18</th>
      <th>LJ8_19</th>
      <th>ZIT_19</th>
      <th>ZITPERC_19</th>
      <th>ZITPERC_LJ_19</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>00AP</td>
      <td>0</td>
      <td>Wereldwijs</td>
      <td>2716PH</td>
      <td>ZOETERMEER</td>
      <td>637</td>
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>...</td>
      <td>,181818181818182</td>
      <td>,0227272727272727</td>
      <td>11</td>
      <td>1</td>
      <td>,0909090909090909</td>
      <td>,0113636363636364</td>
      <td>8</td>
      <td>2</td>
      <td>,25</td>
      <td>,03125</td>
    </tr>
    <tr>
      <th>1</th>
      <td>00AR</td>
      <td>0</td>
      <td>BS "De Maasparel"</td>
      <td>6107AW</td>
      <td>STEVENSWEERT</td>
      <td>1641</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>Bo</td>
      <td>Rooms-Katholiek</td>
      <td>...</td>
      <td>,0588235294117647</td>
      <td>,00735294117647059</td>
      <td>17</td>
      <td>1</td>
      <td>,0588235294117647</td>
      <td>,00735294117647059</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>00AV</td>
      <td>0</td>
      <td>De Morgenster</td>
      <td>3201CN</td>
      <td>SPIJKENISSE</td>
      <td>1930</td>
      <td>Nissewaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
      <td>1</td>
      <td>,0833333333333333</td>
      <td>,0104166666666667</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>00AZ</td>
      <td>0</td>
      <td>De Stapsteen</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>1978</td>
      <td>Molenlanden</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>...</td>
      <td>,25</td>
      <td>,03125</td>
      <td>14</td>
      <td>2</td>
      <td>,142857142857143</td>
      <td>,0178571428571429</td>
      <td>11</td>
      <td>1</td>
      <td>,0909090909090909</td>
      <td>,0113636363636364</td>
    </tr>
    <tr>
      <th>4</th>
      <td>00BA</td>
      <td>0</td>
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
      <td>0</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 39 columns</p>
</div>



In this dataset I see that there are a lot of columns like **ZIT_YEAR**, **ZITPERC_YEAR**, **ZITPERC_LJ_YEAR**, **LJ8_YEAR**, so I would like to get this **YEAR** part and transform this columns to rows with specific year value and only 3 columns: **ZIT**, **ZITPERC**,  **ZITPERC_LJ** and **LJ8**. It will need a lot of transformations, so let's start with creating few separate dataset for each year:


```python
def convert_to_numeric(dataframe, column):
    dataframe[column] = dataframe[column].astype(str).str.replace(',','.')
    dataframe[column] = dataframe[column].astype(str).str.replace(r'^.','0.')
    dataframe[column] = pd.to_numeric(dataframe[column])
    
def get_year_dataset(year):
    data = (data_raw.copy())[['BRIN_NUMMER', 'VESTIGINGSNUMMER', 
                              'LJ8_' + year, 'ZIT_' + year,
                              'ZITPERC_' + year]]
    data['SCHOOL_ID'] = data["VESTIGINGSNUMMER"].astype(str) + data["BRIN_NUMMER"] + "_20" + year
    data.rename(columns = {'LJ8_' + year: 'LJ8',
                           'ZIT_' + year: 'ZIT',
                           'ZITPERC_' + year: 'ZITPERC'},
                inplace = True)
    data.drop('VESTIGINGSNUMMER', 1, inplace = True)
    data.drop('BRIN_NUMMER', 1, inplace = True)
    convert_to_numeric(data, 'ZITPERC')
    data['ZITPERC'] = data['ZITPERC'] * 100
    return data


data_2014 = get_year_dataset('14')
data_2015 = get_year_dataset('15')
data_2016 = get_year_dataset('16')
data_2017 = get_year_dataset('17')
data_2018 = get_year_dataset('18')
```


```python
data_2014.head()
data_2014.dtypes
```




    LJ8            int64
    ZIT            int64
    ZITPERC      float64
    SCHOOL_ID     object
    dtype: object



And merge it together:


```python
data_frames = [data_2014, data_2015, data_2016, data_2017, data_2018]
data_second_year = pd.concat(data_frames).fillna(0)
data_second_year.reset_index(drop=True, inplace=True)
print(data_2014.shape)
print(data_2015.shape)
print(data_2016.shape)
print(data_2017.shape)
print(data_2017.shape)
print(data_2018.shape)
print(data_second_year.shape)
data_second_year
```

    (6910, 4)
    (6910, 4)
    (6910, 4)
    (6910, 4)
    (6910, 4)
    (6910, 4)
    (34550, 4)





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
      <th>LJ8</th>
      <th>ZIT</th>
      <th>ZITPERC</th>
      <th>SCHOOL_ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>11</td>
      <td>0</td>
      <td>0.000000</td>
      <td>000AP_2014</td>
    </tr>
    <tr>
      <th>1</th>
      <td>18</td>
      <td>1</td>
      <td>5.555556</td>
      <td>000AR_2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>33.333333</td>
      <td>000AV_2014</td>
    </tr>
    <tr>
      <th>3</th>
      <td>24</td>
      <td>3</td>
      <td>12.500000</td>
      <td>000AZ_2014</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12</td>
      <td>4</td>
      <td>33.333333</td>
      <td>000BA_2014</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>34545</th>
      <td>0</td>
      <td>0</td>
      <td>0.000000</td>
      <td>031ML_2018</td>
    </tr>
    <tr>
      <th>34546</th>
      <td>0</td>
      <td>0</td>
      <td>0.000000</td>
      <td>031MM_2018</td>
    </tr>
    <tr>
      <th>34547</th>
      <td>0</td>
      <td>0</td>
      <td>0.000000</td>
      <td>031MN_2018</td>
    </tr>
    <tr>
      <th>34548</th>
      <td>0</td>
      <td>0</td>
      <td>0.000000</td>
      <td>031MV_2018</td>
    </tr>
    <tr>
      <th>34549</th>
      <td>0</td>
      <td>0</td>
      <td>0.000000</td>
      <td>031MX_2018</td>
    </tr>
  </tbody>
</table>
<p>34550 rows × 4 columns</p>
</div>



Let's check missing and unexpected values:


```python
print(data_second_year.dtypes)
```

    LJ8            int64
    ZIT            int64
    ZITPERC      float64
    SCHOOL_ID     object
    dtype: object



```python
lj8_zero = data_second_year[data_second_year['LJ8'] == 0]
print(lj8_zero.shape)
lj8_zero.head()
```

    (2282, 4)





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
      <th>LJ8</th>
      <th>ZIT</th>
      <th>ZITPERC</th>
      <th>SCHOOL_ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>61</th>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>003AX_2014</td>
    </tr>
    <tr>
      <th>69</th>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>003BW_2014</td>
    </tr>
    <tr>
      <th>75</th>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>003CS_2014</td>
    </tr>
    <tr>
      <th>124</th>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>003HM_2014</td>
    </tr>
    <tr>
      <th>172</th>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>003LC_2014</td>
    </tr>
  </tbody>
</table>
</div>



It looks like we have 2282 values with total amount of students in class 8 is 0. It could be wrong data or mistake, but anyway this rows says us nothing, so we can remove it:


```python
data_second_year.drop(lj8_zero.index, 0, inplace = True)
print(data_second_year.shape)
data_second_year.head()
```

    (32268, 4)





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
      <th>LJ8</th>
      <th>ZIT</th>
      <th>ZITPERC</th>
      <th>SCHOOL_ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>11</td>
      <td>0</td>
      <td>0.000000</td>
      <td>000AP_2014</td>
    </tr>
    <tr>
      <th>1</th>
      <td>18</td>
      <td>1</td>
      <td>5.555556</td>
      <td>000AR_2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>33.333333</td>
      <td>000AV_2014</td>
    </tr>
    <tr>
      <th>3</th>
      <td>24</td>
      <td>3</td>
      <td>12.500000</td>
      <td>000AZ_2014</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12</td>
      <td>4</td>
      <td>33.333333</td>
      <td>000BA_2014</td>
    </tr>
  </tbody>
</table>
</div>



So now let's read dataset from previous step, add our dataset there and write it to down to final file: 


```python
data_score = pd.read_csv("score_second_result.csv", error_bad_lines=False, sep=',', encoding = "ISO-8859-1") 

print(data_score.shape)
print(data_score.columns)
data_score.head()
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
      <td>0</td>
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
      <td>0</td>
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
      <td>0</td>
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
      <td>0</td>
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
      <td>0</td>
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




```python
data = pd.merge(data_score, data_second_year,  on='SCHOOL_ID',  how='left').fillna(0)
data.LJ8 = data.LJ8.astype(int)
data.ZIT = data.ZIT.astype(int)
print(data.shape)
print(data.columns)
data.head()
```

    (31300, 36)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENUMMER', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'BEVOEGD_GEZAG_NUMMER', 'EXAMEN',
           'EXAMEN_AANTAL', 'EXAMEN_GEM', 'REKENEN_LAGER1F', 'REKENEN_1F',
           'REKENEN_1S', 'REKENEN_2F', 'LV_LAGER1F', 'LV_1F', 'LV_2F',
           'TV_LAGER1F', 'TV_1F', 'TV_2F', 'VSO', 'PRO', 'VMBO', 'VMBO_HAVO',
           'HAVO', 'HAVO_VWO', 'VWO', 'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES',
           'LJ8', 'ZIT', 'ZITPERC'],
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
      <th>VMBO</th>
      <th>VMBO_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
      <th>LJ8</th>
      <th>ZIT</th>
      <th>ZITPERC</th>
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
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>13</td>
      <td>0</td>
      <td>0.000000</td>
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
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
      <td>17</td>
      <td>0</td>
      <td>0.000000</td>
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
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
      <td>19</td>
      <td>3</td>
      <td>15.789474</td>
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
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
      <td>24</td>
      <td>5</td>
      <td>20.833333</td>
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
      <td>7</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>16</td>
      <td>15</td>
      <td>2</td>
      <td>13.333333</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 36 columns</p>
</div>



Looking at the list of columns I can see that we probably won't need **BEVOEGD_GEZAG_NUMMER** and **ZITPERC** columns because we can get the info in this columns from other places in dataset. I also think that we should remove **GEMEENTENUMMER** columns because we've already have **GEMEENTENAAM** which has the same info. Let's remove them and then write the result dataset to the output folder:


```python
data.drop('GEMEENTENUMMER', 1, inplace = True)
data.drop('BEVOEGD_GEZAG_NUMMER', 1, inplace = True)
data.drop('ZITPERC', 1, inplace = True)
data.columns
```




    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'EXAMEN', 'EXAMEN_AANTAL', 'EXAMEN_GEM',
           'REKENEN_LAGER1F', 'REKENEN_1F', 'REKENEN_1S', 'REKENEN_2F',
           'LV_LAGER1F', 'LV_1F', 'LV_2F', 'TV_LAGER1F', 'TV_1F', 'TV_2F', 'VSO',
           'PRO', 'VMBO', 'VMBO_HAVO', 'HAVO', 'HAVO_VWO', 'VWO',
           'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES', 'LJ8', 'ZIT'],
          dtype='object')




```python
data.to_csv(r'output/Score.csv', index = False)
```

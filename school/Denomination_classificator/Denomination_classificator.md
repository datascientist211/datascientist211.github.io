---

title:  Dutch primary school denomination classificator

---
 
## Overview

I need to choose a school for child and I have a problem there -  I don't know what should I look at in this process. For example there are a lot of school types: 'Openbaar', 'Rooms-Katholiek', 'Protestants-Christelijk', etc. As for a person who never met this system before it's not very clear for me which one should I choose and which one would fit my child better. On the other hand I want to make a desicion based on objective data such as
- Scores in final tests.
- A number of students from the school who got recommended to continue their education on a particular 'level' in future.
- A 'quality' of students knowledge.
- Etc. 

As a result of all this thoughts I realized that I have a following issue: I don't know if I need to choose a type of a school together with other characteristics, or the type can be explained by those characteristics. If the second thought is true, then I can just choose the characteristics that I want for my childs school to have and they will simply define the best type of school. In other words, is it possible to make a classifier which would identify whether a school is Openbaar or Rooms-Katholiek or has other denomination based on other characterrictics we have? 

I found and pooled in a dataset with the most important information I want to know about my childs future school. Now I want to know if I need to choose a denomination of a school (and spend time to investigate what each school denomination means in Dutch culture, their comparison and so on)? Or I can just choose other characteristic (which look more clear to me) and they will define a denomination of a school. I will build several classifiers based on different ML alghorithms. Then I will try to choose the best of them and see if the best classifier works good enough to predict a denomination of a primary school. If there is no such classifier, we can conclude the denomination is a purely non-derivative feature and can be ignored while choosing a school.

## Data preparation

Let's start with reading the dataset from the file called 'Score.csv':


```python
import pandas as pd 
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np

data_score = pd.read_csv("./make_school_data/main/output/Score.csv", error_bad_lines=False, sep=',', encoding = "ISO-8859-1") 
print(data_score.shape)
print(data_score.columns)
data_score.head() 
```

    (31300, 33)
    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'EXAMEN', 'EXAMEN_AANTAL', 'EXAMEN_GEM',
           'REKENEN_LAGER1F', 'REKENEN_1F', 'REKENEN_1S', 'REKENEN_2F',
           'LV_LAGER1F', 'LV_1F', 'LV_2F', 'TV_LAGER1F', 'TV_1F', 'TV_2F', 'VSO',
           'PRO', 'VMBO', 'VMBO_HAVO', 'HAVO', 'HAVO_VWO', 'VWO',
           'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES', 'LJ8', 'ZIT'],
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
      <th>GEMEENTENAAM</th>
      <th>PROVINCIE</th>
      <th>SOORT_PO</th>
      <th>DENOMINATIE_VESTIGING</th>
      <th>EXAMEN</th>
      <th>...</th>
      <th>PRO</th>
      <th>VMBO</th>
      <th>VMBO_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
      <th>LJ8</th>
      <th>ZIT</th>
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
      <td>Zoetermeer</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>CET</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>13</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>000AR_2015</td>
      <td>2015</td>
      <td>BS "De Maasparel"</td>
      <td>6109AM</td>
      <td>OHE EN LAAK</td>
      <td>Maasgouw</td>
      <td>Limburg</td>
      <td>Bo</td>
      <td>Rooms-Katholiek</td>
      <td>CET</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
      <td>17</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>000AZ_2015</td>
      <td>2015</td>
      <td>De Kiezel en de Kei</td>
      <td>2971AR</td>
      <td>BLESKENSGRAAF CA</td>
      <td>Molenwaard</td>
      <td>Zuid-Holland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>CET</td>
      <td>...</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
      <td>19</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>000BA_2015</td>
      <td>2015</td>
      <td>OBS De Klimboom</td>
      <td>6666EB</td>
      <td>HETEREN</td>
      <td>Overbetuwe</td>
      <td>Gelderland</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>CET</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
      <td>24</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>000BB_2015</td>
      <td>2015</td>
      <td>Obs Letterwies</td>
      <td>9944AR</td>
      <td>NIEUWOLDA</td>
      <td>Oldambt</td>
      <td>Groningen</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>CET</td>
      <td>...</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>16</td>
      <td>15</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 33 columns</p>
</div>



There are a lot of variables which define an address of a school in different ways here. We need to choose one of them. I think we can leave **GEMEENTENAAM** column as a variable which explains location and remove the rest of the the address columns:


```python
columns = ['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING', 'PLAATSNAAM', 
           'PROVINCIE']
print(data_score.shape)
data_score.drop(columns, 1, inplace = True)
print(data_score.shape)
print(data_score.columns)
data_score.head()
```

    (31300, 33)
    (31300, 27)
    Index(['GEMEENTENAAM', 'SOORT_PO', 'DENOMINATIE_VESTIGING', 'EXAMEN',
           'EXAMEN_AANTAL', 'EXAMEN_GEM', 'REKENEN_LAGER1F', 'REKENEN_1F',
           'REKENEN_1S', 'REKENEN_2F', 'LV_LAGER1F', 'LV_1F', 'LV_2F',
           'TV_LAGER1F', 'TV_1F', 'TV_2F', 'VSO', 'PRO', 'VMBO', 'VMBO_HAVO',
           'HAVO', 'HAVO_VWO', 'VWO', 'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES',
           'LJ8', 'ZIT'],
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
      <th>GEMEENTENAAM</th>
      <th>SOORT_PO</th>
      <th>DENOMINATIE_VESTIGING</th>
      <th>EXAMEN</th>
      <th>EXAMEN_AANTAL</th>
      <th>EXAMEN_GEM</th>
      <th>REKENEN_LAGER1F</th>
      <th>REKENEN_1F</th>
      <th>REKENEN_1S</th>
      <th>REKENEN_2F</th>
      <th>...</th>
      <th>PRO</th>
      <th>VMBO</th>
      <th>VMBO_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
      <th>LJ8</th>
      <th>ZIT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Zoetermeer</td>
      <td>Bo</td>
      <td>Gereformeerd vrijgemaakt</td>
      <td>CET</td>
      <td>11</td>
      <td>534.3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>13</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Maasgouw</td>
      <td>Bo</td>
      <td>Rooms-Katholiek</td>
      <td>CET</td>
      <td>18</td>
      <td>539.8</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
      <td>17</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Molenwaard</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>CET</td>
      <td>22</td>
      <td>532.9</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
      <td>19</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Overbetuwe</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>CET</td>
      <td>11</td>
      <td>536.2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
      <td>24</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Oldambt</td>
      <td>Bo</td>
      <td>Openbaar</td>
      <td>CET</td>
      <td>16</td>
      <td>531.3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>16</td>
      <td>15</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 27 columns</p>
</div>



Next I would like to explore the outcome:


```python
print(data_score['DENOMINATIE_VESTIGING'].value_counts())
plt.figure(figsize=(15,8))
print(sns.countplot(y='DENOMINATIE_VESTIGING',data=data_score, 
                    order = data_score['DENOMINATIE_VESTIGING'].value_counts().index))
```

    Openbaar                          9951
    Rooms-Katholiek                   9588
    Protestants-Christelijk           7899
    Algemeen bijzonder                1513
    Reformatorisch                     806
    Gereformeerd vrijgemaakt           534
    Antroposofisch                     328
    Samenwerking PC, RK                265
    Islamitisch                        235
    Interconfessioneel                  60
    Evangelisch                         41
    Hindoeistisch                       32
    Samenwerking Opb., RK               15
    Samenwerking Opb., PC               15
    Joods                               10
    Evangelische broedergemeenscha       4
    Samenwerking PC, RK, Alg. Bijz       4
    Name: DENOMINATIE_VESTIGING, dtype: int64
    AxesSubplot(0.125,0.125;0.775x0.755)



    
![png](output_6_1.png)
    


From the plot and data above I see that there are several values of **DENOMINATIE_VESTIGING** column with relatively small amoumt of data. Let's remove rows with a number of the denomination values smaller than 0.5% of the total observations amount.


```python
values_to_remove = []
value_counts = data_score['DENOMINATIE_VESTIGING'].value_counts()
threshold = 0.005 * len(data_score['DENOMINATIE_VESTIGING'])
for column in data_score['DENOMINATIE_VESTIGING'].value_counts().index.tolist():
    if value_counts[column] < threshold:
        values_to_remove.append(column)
values_to_remove
```




    ['Interconfessioneel',
     'Evangelisch',
     'Hindoeistisch',
     'Samenwerking Opb., RK',
     'Samenwerking Opb., PC',
     'Joods',
     'Evangelische broedergemeenscha',
     'Samenwerking PC, RK, Alg. Bijz']




```python
print(data_score.shape)
data_score = data_score.drop(data_score[data_score['DENOMINATIE_VESTIGING'].isin(values_to_remove)].index, 0)
data_score.shape
```

    (31300, 27)





    (31119, 27)



In the dataset there are 3 categorical columns. Before starting the modelling we need to convert all the categorical variables to numerical ones. Let's use ***LabelEncoder*** this time: 


```python
from sklearn.preprocessing import LabelEncoder

data_score['SOORT_PO'] = LabelEncoder().fit_transform(data_score['SOORT_PO'])

data_score['GEMEENTENAAM'] = LabelEncoder().fit_transform(data_score['GEMEENTENAAM'])

data_score['DENOMINATIE_VESTIGING'] = LabelEncoder().fit_transform(data_score['DENOMINATIE_VESTIGING'])

data_score['EXAMEN'] = LabelEncoder().fit_transform(data_score['EXAMEN'])
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
      <th>GEMEENTENAAM</th>
      <th>SOORT_PO</th>
      <th>DENOMINATIE_VESTIGING</th>
      <th>EXAMEN</th>
      <th>EXAMEN_AANTAL</th>
      <th>EXAMEN_GEM</th>
      <th>REKENEN_LAGER1F</th>
      <th>REKENEN_1F</th>
      <th>REKENEN_1S</th>
      <th>REKENEN_2F</th>
      <th>...</th>
      <th>PRO</th>
      <th>VMBO</th>
      <th>VMBO_HAVO</th>
      <th>HAVO</th>
      <th>HAVO_VWO</th>
      <th>VWO</th>
      <th>ADVIES_NIET_MOGELIJK</th>
      <th>TOTAAL_ADVIES</th>
      <th>LJ8</th>
      <th>ZIT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>402</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>11</td>
      <td>534.3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>13</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>210</td>
      <td>0</td>
      <td>7</td>
      <td>2</td>
      <td>18</td>
      <td>539.8</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>18</td>
      <td>17</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>227</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>22</td>
      <td>532.9</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>22</td>
      <td>19</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>271</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>11</td>
      <td>536.2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
      <td>24</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>253</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>16</td>
      <td>531.3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>16</td>
      <td>15</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 27 columns</p>
</div>



## Building classification models

Ley's try to build classification models using several different ML alghorithms: Random Forest, SVM, KNN, XGBoosting. 

First of all, we need to standardize the data and split it into test and train subsets:


```python
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.svm import SVC
from sklearn.ensemble import RandomForestClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.naive_bayes import GaussianNB
from xgboost import XGBClassifier
from sklearn.preprocessing import LabelEncoder, MinMaxScaler
from sklearn.metrics import roc_curve

pred = data_score['DENOMINATIE_VESTIGING']

data_score.drop('DENOMINATIE_VESTIGING', 1, inplace = True)

random_state = 4004

scaler = MinMaxScaler()
data = scaler.fit_transform(data_score)
    
X_train, X_test, y_train, y_test = train_test_split(data, pred, train_size=0.7, test_size=0.3, random_state=random_state)
```

### Random forest


```python
rfc = RandomForestClassifier(n_estimators=100, random_state = random_state)
rfc.fit(X_train, y_train)
pred_test = rfc.predict(X_test)

```


```python
rfc_score = rfc.score(X_test, y_test)
print("Random Forest's accuracy is: {:.2%}".format(rfc_score))
```

    Random Forest's accuracy is: 40.47%


### K Nearest Neighbors 


```python
knn = KNeighborsClassifier()
knn.fit(X_train, y_train)
preds = knn.predict(X_test)
pred_test = knn.predict(X_test)
```


```python
knn_score = knn.score(X_test, y_test)
print("KNN's accuracy is: {:.2%}".format(knn_score))
```

    KNN's accuracy is: 34.89%


### XGBoost


```python
xgb = XGBClassifier(n_estimators=100)
xgb.fit(X_train, y_train)
pred_test = xgb.predict(X_test)
```


```python
xgb_score = (pred_test == y_test).sum() / len(pred_test)
print("XGBoost's prediction accuracy is: {:.2%}".format(xgb_score))
```

    XGBoost's prediction accuracy is: 51.86%



```python
results = pd.DataFrame({
    'Model': ['Random Forest', 'KNN', 'XGBoost'],
    'Score': ["{:.2%}".format(rfc_score), "{:.2%}".format(knn_score), "{:.2%}".format(xgb_score)]})
result_df = results.sort_values(by='Score', ascending=False)
result_df = result_df.set_index('Model')
result_df
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
      <th>Score</th>
    </tr>
    <tr>
      <th>Model</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>XGBoost</th>
      <td>51.86%</td>
    </tr>
    <tr>
      <th>Random Forest</th>
      <td>40.47%</td>
    </tr>
    <tr>
      <th>KNN</th>
      <td>34.89%</td>
    </tr>
  </tbody>
</table>
</div>





We can see that none of the ML algorithm was able to build an apropriate model. The best one shows accuracy of a mere 52% which is far from perfect.

That means classes of different denominations do not differ that much from each other based on the objective criteria we have in the dataset. In other words: denominations are more or less the same if compared with objective numbers.

Therefore I can conclude that while choosing a school for my child I may ignore schools denominations and concentrate on scores, knowledge quality and other metrics which are important and familiar to me.


```python

```

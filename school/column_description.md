---

title: Description for Dutch schools dataset

---


There are a lot of columns in resulting dataset and almost all of them are in dutch :) so I think it would be clears to explain what info each column shows us. First let's read the dataset and take a loor at it and at its columns:


```python
import pandas as pd 
data_score = pd.read_csv("output/Score.csv", error_bad_lines=False, sep=',', encoding = "ISO-8859-1") 
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




```python
data_score.columns
```




    Index(['SCHOOL_ID', 'DATUM', 'INSTELLINGSNAAM_VESTIGING', 'POSTCODE_VESTIGING',
           'PLAATSNAAM', 'GEMEENTENAAM', 'PROVINCIE', 'SOORT_PO',
           'DENOMINATIE_VESTIGING', 'EXAMEN', 'EXAMEN_AANTAL', 'EXAMEN_GEM',
           'REKENEN_LAGER1F', 'REKENEN_1F', 'REKENEN_1S', 'REKENEN_2F',
           'LV_LAGER1F', 'LV_1F', 'LV_2F', 'TV_LAGER1F', 'TV_1F', 'TV_2F', 'VSO',
           'PRO', 'VMBO', 'VMBO_HAVO', 'HAVO', 'HAVO_VWO', 'VWO',
           'ADVIES_NIET_MOGELIJK', 'TOTAAL_ADVIES', 'LJ8', 'ZIT'],
          dtype='object')



We can see 37 columns with different values, lets's explain what means what:

| **Columns** | **Meaning**   |
| :-: | :- |
|  **SCHOOL_ID**  | School ID, was made out of 3 columns, contains info about school registration number and year for which data is shown|
|  **DATUM**  | Year for which data is shown|
|  **INSTELLINGSNAAM_VESTIGING**  | Name of the school|
|**POSTCODE_VESTIGING**| School postcode|
|**PLAATSNAAM** | Place where the school is located (usually town or village)|
|**GEMEENTENAAM** | Name of the municipality where the school is located|
|**PROVINCIE** | Province where the school is located|
|**SOORT_PO** | The type of primary education: bo (primary education) or sbo (special primary education)|
|**DENOMINATIE_VESTIGING**| The philosophy or religion propagated by the school establishment|
|**EXAMEN**| Type of final exam which shool was chosen for most of the students|
|**EXAMEN_AANTAL** | Amount of student who took the exam ***EXAMEN***|
|**EXAMEN_GEM** | Average score for the exam ***EXAMEN*** which got ***EXAMEN_AANTAL*** amount of students in the school in ***DATUM*** year|
|**REKENEN_LAGER1F**| The number of students with has a level for arithmetic lower than the basic level 1F|
|**REKENEN_1F** | The number of students with the basic 1F math level for arithmetic|
|**REKENEN_1S** | The number of students with the target level for arithmetic|
|**REKENEN_2F** | The number of students with the 2F basic math level|
|**LV_LAGER1F** | The number of students with a level below basic level 1F for reading skills|
|**LV_1F** | The number of students with the basic level 1F for reading skills| 
|**LV_2F** |The number of students with the basic level 2F for reading skills|
|**TV_LAGER1F** | The number of students with a level below the basic language proficiency level| 
|**TV_1F** | The number of students with the basic level 1F for language proficiency skills|
|**TV_2F** | The number of students with the basic level 2F for language proficiency skills|
|**PRO**| The number of students who got school advice for practical education 
|**VSO**|  The number of students who got school advice for secondary special education
|**VMBO**| The number of students who got school advice for practical and some more theoretical education|
|**VMBO_HAVO**| The number of students who got school advice for mixed or theoretical learning path and **HAVO** |
|**HAVO**| The number of students who got school advice for high education (after secondary one)
|**HAVO_VWO**| The number of students who got school advice for high education or pre-university education |
|**VWO**| The number of students who got school advice for pre-university education|  
|**ADVIES_NIET_MOGELIJK**|The number of students for whom the school has indicated that a specific advice is not possible (for example due to transition classes)|
|**TOTAAL_ADVIES**| The total number of students with a school advice + the number of students for which the school has indicated that a specific advice is not possible|
|**LJ8** | Number of students in grade 8|
|**ZIT** | Number of students aged 12 or older, in grade 8, with more than 8 years of residence in education |

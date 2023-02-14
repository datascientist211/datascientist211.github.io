---

title:  Amsterdam EDA dataset construction

---

# Data collection
I would like to show you where I got data, how I collected, investigated and clean dataset for explotatory data analysis of the Amsterdam residents.

There is a [Wijk- en buurtkaart 2019 dataset](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2019) which everybody could download. It's quite big, so I filtered it to have only rows about Amsterdam neighbourhoods. The result dataset was saved in `ams_buurten_2019.csv` file.


Let's take a look at what we have there:


```python
import pandas as pd 
import geopandas as gpd

data = pd.read_csv('ams_buurten_2019.csv', sep = ";")
data = data[data["water"] == "NEE"]
print(data.shape)
data.head()
```

    (470, 206)





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
      <th>fid</th>
      <th>geom</th>
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>gemeentecode</th>
      <th>gemeentenaam</th>
      <th>indelingswijziging_wijken_en_buurten</th>
      <th>water</th>
      <th>meest_voorkomende_postcode</th>
      <th>...</th>
      <th>theater_gemiddelde_afstand_in_km</th>
      <th>theater_gemiddeld_aantal_binnen_5_km</th>
      <th>theater_gemiddeld_aantal_binnen_10_km</th>
      <th>theater_gemiddeld_aantal_binnen_20_km</th>
      <th>gemiddelde_afstand_tot_museum</th>
      <th>gemiddeld_aantal_musea_binnen_5_km</th>
      <th>gemiddeld_aantal_musea_binnen_10_km</th>
      <th>gemiddeld_aantal_musea_binnen_20_km</th>
      <th>jaarstatcode</th>
      <th>jaar</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>4159</td>
      <td>GP</td>
      <td>BU03630000</td>
      <td>Kop Zeedijk</td>
      <td>WK036300</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>0.6</td>
      <td>37.2</td>
      <td>49.3</td>
      <td>63.0</td>
      <td>0.2</td>
      <td>42.7</td>
      <td>46.0</td>
      <td>65.0</td>
      <td>2019BU03630000</td>
      <td>2019.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4160</td>
      <td>GP</td>
      <td>BU03630001</td>
      <td>Oude Kerk e.o.</td>
      <td>WK036300</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>0.5</td>
      <td>37.0</td>
      <td>50.4</td>
      <td>62.9</td>
      <td>0.2</td>
      <td>42.6</td>
      <td>46.1</td>
      <td>65.0</td>
      <td>2019BU03630001</td>
      <td>2019.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4161</td>
      <td>GP</td>
      <td>BU03630002</td>
      <td>Burgwallen Oost</td>
      <td>WK036300</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>0.3</td>
      <td>37.0</td>
      <td>51.0</td>
      <td>62.4</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>46.1</td>
      <td>65.0</td>
      <td>2019BU03630002</td>
      <td>2019.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>4162</td>
      <td>GP</td>
      <td>BU03630003</td>
      <td>Nes e.o.</td>
      <td>WK036300</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>0.2</td>
      <td>37.0</td>
      <td>51.0</td>
      <td>62.6</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>46.7</td>
      <td>65.0</td>
      <td>2019BU03630003</td>
      <td>2019.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4163</td>
      <td>GP</td>
      <td>BU03630004</td>
      <td>BG-terrein e.o.</td>
      <td>WK036300</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>0.2</td>
      <td>37.0</td>
      <td>51.0</td>
      <td>62.0</td>
      <td>0.3</td>
      <td>43.0</td>
      <td>47.3</td>
      <td>65.0</td>
      <td>2019BU03630004</td>
      <td>2019.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 206 columns</p>
</div>



Now I want to add bigger neighbourhoods called `wijk` in dutch, so I would be able to add districts (called `stadsdelen`). 

From the Wijk- en buurtkaart 2019 dataset mentioned above I also got a dataset `wijken.csv`, but I only need `wijknaam` and `wijkcode` from it.
Let's get it and then add `wijknaam` to the main dataset:


```python
new_data = pd.read_csv('wijken.csv', sep = ";")
data_wijk = new_data[new_data['gemeentenaam'] == "Amsterdam"]
data_wijk = data_wijk[data_wijk['wijknaam'] != ' ']
data_wijk = data_wijk[['wijkcode', 'wijknaam']]
print(data_wijk.shape)
data_wijk.head()
```

    (99, 2)





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
      <th>wijkcode</th>
      <th>wijknaam</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>905</th>
      <td>WK036300</td>
      <td>Burgwallen-Oude Zijde</td>
    </tr>
    <tr>
      <th>906</th>
      <td>WK036301</td>
      <td>Burgwallen-Nieuwe Zijde</td>
    </tr>
    <tr>
      <th>907</th>
      <td>WK036302</td>
      <td>Grachtengordel-West</td>
    </tr>
    <tr>
      <th>908</th>
      <td>WK036303</td>
      <td>Grachtengordel-Zuid</td>
    </tr>
    <tr>
      <th>909</th>
      <td>WK036304</td>
      <td>Nieuwmarkt/Lastage</td>
    </tr>
  </tbody>
</table>
</div>




```python
#data = pd.merge(data, data_wijk, on="wijkcode")
def get_wijk(row):
    data_row = data_wijk[data_wijk['wijkcode'] == row['wijkcode']]
    value = "" if data_row.empty else data_row['wijknaam'].iloc[0]
    return value
    

data = data.copy()

data['wijknaam'] = data.apply(lambda row: get_wijk(row), axis=1)
data['wijkcode'] = data['wijkcode'].astype(str).str.replace('WK0363', '')
print(data.shape)
data.head()
```

    (470, 207)





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
      <th>fid</th>
      <th>geom</th>
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>gemeentecode</th>
      <th>gemeentenaam</th>
      <th>indelingswijziging_wijken_en_buurten</th>
      <th>water</th>
      <th>meest_voorkomende_postcode</th>
      <th>...</th>
      <th>theater_gemiddeld_aantal_binnen_5_km</th>
      <th>theater_gemiddeld_aantal_binnen_10_km</th>
      <th>theater_gemiddeld_aantal_binnen_20_km</th>
      <th>gemiddelde_afstand_tot_museum</th>
      <th>gemiddeld_aantal_musea_binnen_5_km</th>
      <th>gemiddeld_aantal_musea_binnen_10_km</th>
      <th>gemiddeld_aantal_musea_binnen_20_km</th>
      <th>jaarstatcode</th>
      <th>jaar</th>
      <th>wijknaam</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>4159</td>
      <td>GP</td>
      <td>BU03630000</td>
      <td>Kop Zeedijk</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>37.2</td>
      <td>49.3</td>
      <td>63.0</td>
      <td>0.2</td>
      <td>42.7</td>
      <td>46.0</td>
      <td>65.0</td>
      <td>2019BU03630000</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4160</td>
      <td>GP</td>
      <td>BU03630001</td>
      <td>Oude Kerk e.o.</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>37.0</td>
      <td>50.4</td>
      <td>62.9</td>
      <td>0.2</td>
      <td>42.6</td>
      <td>46.1</td>
      <td>65.0</td>
      <td>2019BU03630001</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4161</td>
      <td>GP</td>
      <td>BU03630002</td>
      <td>Burgwallen Oost</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>37.0</td>
      <td>51.0</td>
      <td>62.4</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>46.1</td>
      <td>65.0</td>
      <td>2019BU03630002</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
    </tr>
    <tr>
      <th>5</th>
      <td>4162</td>
      <td>GP</td>
      <td>BU03630003</td>
      <td>Nes e.o.</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>37.0</td>
      <td>51.0</td>
      <td>62.6</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>46.7</td>
      <td>65.0</td>
      <td>2019BU03630003</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4163</td>
      <td>GP</td>
      <td>BU03630004</td>
      <td>BG-terrein e.o.</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>37.0</td>
      <td>51.0</td>
      <td>62.0</td>
      <td>0.3</td>
      <td>43.0</td>
      <td>47.3</td>
      <td>65.0</td>
      <td>2019BU03630004</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 207 columns</p>
</div>



From [this](https://maps.amsterdam.nl/open_geodata/) website (from the parts called 'Buurten OUD' and 'Stadsdelen OUD') I got datasets which describe how Amsterdam divided by `buurten` and `stadsdelen`. 

Let's open it, prepare and add info about city districts (`stadsdelen`) from it to the main dataset:


```python
import re
data_wijk_stadsdeelcode = pd.read_csv('GEBIED_BUURTEN.csv', sep = ";")
data_wijk_stadsdeelcode = data_wijk_stadsdeelcode[['Wijkcode', 'Stadsdeelcode']]
data_wijk_stadsdeelcode = data_wijk_stadsdeelcode.rename(columns={'Wijkcode': 'wijkcode'})
data_wijk_stadsdeelcode = data_wijk_stadsdeelcode.drop_duplicates(subset=["wijkcode"], keep='first')

print(data_wijk_stadsdeelcode.shape)
data_wijk_stadsdeelcode.head()
```

    (107, 2)





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
      <th>wijkcode</th>
      <th>Stadsdeelcode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A00</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A01</td>
      <td>A</td>
    </tr>
    <tr>
      <th>11</th>
      <td>A02</td>
      <td>A</td>
    </tr>
    <tr>
      <th>14</th>
      <td>A03</td>
      <td>A</td>
    </tr>
    <tr>
      <th>21</th>
      <td>A04</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>




```python
data_stadsdelen = pd.read_csv('GEBIED_STADSDELEN.csv', sep = ";")
data_stadsdelen = data_stadsdelen[['Stadsdeelcode', 'Stadsdeelnaam']]
data_stadsdelen = data_stadsdelen.rename(columns={'Stadsdeelnaam': 'stadsdeel'})
#print(data_stadsdelen.shape)
data_stadsdelen.head()

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
      <th>Stadsdeelcode</th>
      <th>stadsdeel</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>Centrum</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>Westpoort</td>
    </tr>
    <tr>
      <th>2</th>
      <td>E</td>
      <td>West</td>
    </tr>
    <tr>
      <th>3</th>
      <td>F</td>
      <td>Nieuw-West</td>
    </tr>
    <tr>
      <th>4</th>
      <td>K</td>
      <td>Zuid</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(data_stadsdelen.shape)
data_stadsdelen = pd.merge(data_stadsdelen, data_wijk_stadsdeelcode, on="Stadsdeelcode")
print(data_stadsdelen.shape)
data_stadsdelen.head()

```

    (9, 2)
    (107, 3)





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
      <th>Stadsdeelcode</th>
      <th>stadsdeel</th>
      <th>wijkcode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>Centrum</td>
      <td>A00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A</td>
      <td>Centrum</td>
      <td>A01</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A</td>
      <td>Centrum</td>
      <td>A02</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>Centrum</td>
      <td>A03</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A</td>
      <td>Centrum</td>
      <td>A04</td>
    </tr>
  </tbody>
</table>
</div>




```python
data_stadsdelen['wijkcode'] = data_stadsdelen['wijkcode'].astype(str).str.replace('\D', 'WK0363', regex=True)
data_stadsdelen.to_excel('amsterdam_stadsdelen.xlsx', index = None, header=True)
data_stadsdelen['wijkcode'] = data_stadsdelen['wijkcode'].astype(str).str.replace('WK0363', '', regex=True)
print(data_stadsdelen.shape)
data_stadsdelen.head()
```

    (107, 3)





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
      <th>Stadsdeelcode</th>
      <th>stadsdeel</th>
      <th>wijkcode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>Centrum</td>
      <td>00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A</td>
      <td>Centrum</td>
      <td>01</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A</td>
      <td>Centrum</td>
      <td>02</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>Centrum</td>
      <td>03</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A</td>
      <td>Centrum</td>
      <td>04</td>
    </tr>
  </tbody>
</table>
</div>




```python
#data = pd.merge(data, data_wijk, on="wijkcode")
def get_stadsdeel(row):
    #print("wijkcode: " +  row['wijkcode'] + " stadsdeel: " + )
    data_row = data_stadsdelen[data_stadsdelen['wijkcode'] == row['wijkcode']]
    value = "" if data_row.empty else data_row['stadsdeel'].iloc[0]
    return value
    

data = data.copy()

data['stadsdeel'] = data.apply(lambda row: get_stadsdeel(row), axis=1)
print(data.shape)
data.head()
```

    (470, 208)





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
      <th>fid</th>
      <th>geom</th>
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>gemeentecode</th>
      <th>gemeentenaam</th>
      <th>indelingswijziging_wijken_en_buurten</th>
      <th>water</th>
      <th>meest_voorkomende_postcode</th>
      <th>...</th>
      <th>theater_gemiddeld_aantal_binnen_10_km</th>
      <th>theater_gemiddeld_aantal_binnen_20_km</th>
      <th>gemiddelde_afstand_tot_museum</th>
      <th>gemiddeld_aantal_musea_binnen_5_km</th>
      <th>gemiddeld_aantal_musea_binnen_10_km</th>
      <th>gemiddeld_aantal_musea_binnen_20_km</th>
      <th>jaarstatcode</th>
      <th>jaar</th>
      <th>wijknaam</th>
      <th>stadsdeel</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>4159</td>
      <td>GP</td>
      <td>BU03630000</td>
      <td>Kop Zeedijk</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>49.3</td>
      <td>63.0</td>
      <td>0.2</td>
      <td>42.7</td>
      <td>46.0</td>
      <td>65.0</td>
      <td>2019BU03630000</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
      <td>Centrum</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4160</td>
      <td>GP</td>
      <td>BU03630001</td>
      <td>Oude Kerk e.o.</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>50.4</td>
      <td>62.9</td>
      <td>0.2</td>
      <td>42.6</td>
      <td>46.1</td>
      <td>65.0</td>
      <td>2019BU03630001</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
      <td>Centrum</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4161</td>
      <td>GP</td>
      <td>BU03630002</td>
      <td>Burgwallen Oost</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>51.0</td>
      <td>62.4</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>46.1</td>
      <td>65.0</td>
      <td>2019BU03630002</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
      <td>Centrum</td>
    </tr>
    <tr>
      <th>5</th>
      <td>4162</td>
      <td>GP</td>
      <td>BU03630003</td>
      <td>Nes e.o.</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>51.0</td>
      <td>62.6</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>46.7</td>
      <td>65.0</td>
      <td>2019BU03630003</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
      <td>Centrum</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4163</td>
      <td>GP</td>
      <td>BU03630004</td>
      <td>BG-terrein e.o.</td>
      <td>00</td>
      <td>GM0363</td>
      <td>Amsterdam</td>
      <td>1</td>
      <td>NEE</td>
      <td>1012</td>
      <td>...</td>
      <td>51.0</td>
      <td>62.0</td>
      <td>0.3</td>
      <td>43.0</td>
      <td>47.3</td>
      <td>65.0</td>
      <td>2019BU03630004</td>
      <td>2019.0</td>
      <td>Burgwallen-Oude Zijde</td>
      <td>Centrum</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 208 columns</p>
</div>



# Data cleaning

Now we need to clean data. I went through the dataset and see that some of the columns contains elements like 'X' or '-', which prevent them from being numerical. Let's fix it, I need this columns as a numbers. Also let's replace '-99999999' value with '0':


```python
data = data.copy()
data.replace(to_replace="X", value="0", inplace=True)
data.replace(to_replace="-", value="0", inplace=True)
data.replace(to_replace=-99999999, value=0, inplace=True)

for col_name in data.select_dtypes(include=[object]):
    data[col_name] = data[col_name].str.replace(',','.')
    
data.fillna(0, inplace=True)
```

Now I want to convert everything I need to numbers:


```python
cols=[i for i in data.columns if i not in ['geom','buurtcode', 'buurtnaam', 'wijkcode', 'gemeentecode', 'gemeentenaam', 
                                           'water', 'jaarstatcode', 'jrstatcode', 'wijknaam', 'stadsdeel']]
for col in cols:
    data[col]=pd.to_numeric(data[col])

print(data.dtypes.to_list())
```

    [dtype('int64'), dtype('O'), dtype('O'), dtype('O'), dtype('O'), dtype('O'), dtype('O'), dtype('int64'), dtype('O'), dtype('int64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('float64'), dtype('O'), dtype('float64'), dtype('O'), dtype('O')]


# Data preparation

Let's add some new columns which I will need later to draw charts:


```python
def calculate_totaal_from_perc(persentage, aantal):
    return int(persentage * aantal / 100)

def calculate_totaal_from_av(avg, aantal):
    return avg * aantal

data['huishoudens_met_kinderen'] = data.apply(lambda row: calculate_totaal_from_perc(row['percentage_huishoudens_met_kinderen'],
                                                                            row['aantal_huishoudens']), axis=1)
data['percentage_migratieachtergrond'] = data.apply(lambda row: row['percentage_westerse_migratieachtergrond'] + row['percentage_niet_westerse_migratieachtergrond'], axis=1)

data['migratieachtergrond'] = data.apply(lambda row: calculate_totaal_from_perc(row['percentage_migratieachtergrond'],
                                                                            row['aantal_inwoners']), axis=1)

data['mensen_van_nederland'] = data.apply(lambda row: calculate_totaal_from_perc(100 - row['percentage_migratieachtergrond'],
                                                                            row['aantal_inwoners']), axis=1)
data['perc_mensen_van_nederland'] = data.apply(lambda row: 100 - row['percentage_migratieachtergrond'], axis=1)

data['westerse_migratieachtergrond'] = data.apply(lambda row: calculate_totaal_from_perc(row['percentage_westerse_migratieachtergrond'],
                                                                            row['aantal_inwoners']), axis=1)
data['niet_westerse_migratieachtergrond'] = data.apply(lambda row: calculate_totaal_from_perc(row['percentage_niet_westerse_migratieachtergrond'],
                                                                            row['aantal_inwoners']), axis=1)

```

And drop all the columns I won't need for EDA:


```python
cols_to_drop = [x for x in data.columns if "_binnen_" in x]
data.drop(cols_to_drop, axis=1, inplace = True)
```

As I noticed, dataset almost doesn't have any info about `Westpoort` district, so let's remove it from the data we're gonna use for explotatory data analysis. Also we don't won't rows where `aantal_inwoners` is 0


```python
print(data.shape)
data_eda = data[data['stadsdeel'] != "Westpoort"]
data_eda = data_eda[data_eda['aantal_inwoners'] != 0]
data_eda = data_eda[data_eda['bevolkingsdichtheid_inwoners_per_km2'] != 0]
print(data_eda.shape)
```

    (470, 155)
    (436, 155)


Also for EDA we doesn't need columns which have descriptions of the neighbourhoods, so I will remove this data from the dataset too:


```python
columns_to_drop = ['buurtcode',
                   'buurtnaam',
                   'wijkcode',
                   'gemeentecode',
                   'gemeentenaam',
                   'indelingswijziging_wijken_en_buurten',
                   'water',
                   'meest_voorkomende_postcode',
                   'dekkingspercentage',
                   'omgevingsadressendichtheid',
                   'stedelijkheid_adressen_per_km2',
                   'oppervlakte_totaal_in_ha',
                   'oppervlakte_land_in_ha',
                   'oppervlakte_water_in_ha',
                   'jaarstatcode',
                   'jaar',
                   'wijknaam'
                  ]
data_eda = data_eda.drop(columns_to_drop, axis=1)
data_eda = data_eda.drop([ column for column in data_eda.columns if "_afstand_" in column ], axis=1)
```


```python
data_eda.to_csv(r'wijkbuurtkaart_for_eda.csv', index = False)
```

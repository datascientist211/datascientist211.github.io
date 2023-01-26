---

title:  Dogs owners dataset construction
description: " "

---


On this page I will show you where I got data, how I collected, investigated and clean it.

There is a website called [Gronometer](https://groningen.buurtmonitor.nl/) where you can find a lot of open data about Groningen. This data is also available to download as datasets. I choosed some data from 2018 year available for different neighbourhoods and downloaded it to `Groningen_population_data_2018.csv` file




To collect the data I need, first let's read the data about dog's owner that we have. 
Then rename some columns and unify column `buurtcode` to has the same value format as other datasets (which we'll be use below)


```python
import pandas as pd 

data_dogs=pd.read_csv('honden_data.csv', sep = ";")
data_dogs = data_dogs.rename(columns={'buurtnr': 'buurtcode'})
data_dogs = data_dogs.rename(columns={'2018': 'dogs_amount'})
data_dogs['buurtcode'] = data_dogs['buurtcode'].astype(str).str.replace('14', '')
data_dogs['buurtcode'] = data_dogs.apply(lambda row: "BU00" + str(row['buurtcode']), axis=1)
data_dogs = data_dogs.drop('2016', axis=1)

print(data_dogs.shape)
data_dogs.head()
```

    (96, 2)





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
      <th>buurtcode</th>
      <th>dogs_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BU000000</td>
      <td>37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BU000001</td>
      <td>51</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000002</td>
      <td>48</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU000003</td>
      <td>21</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000005</td>
      <td>108</td>
    </tr>
  </tbody>
</table>
</div>



Now I would like to read explore data from [Wijk- en buurtkaart 2022 dataset](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2022). I saved this data (without geometry) to `.csv` file.
Let's take a look at what data (and columns) we have here:


```python
data_buurtkaart=pd.read_csv('data_from_wijkbuurtkaart.csv')
data_buurtkaart['buurtcode'] = data_buurtkaart['buurtcode'].astype(str).str.replace('BU0014', 'BU00')

print(data_buurtkaart.shape)
print(data_buurtkaart.columns)
data_buurtkaart.head()
```

    (150, 41)
    Index(['buurtcode', 'buurtnaam', 'wijkcode', 'gemeentecode', 'gemeentenaam',
           'indelingswijziging_wijken_en_buurten', 'water',
           'meest_voorkomende_postcode', 'dekkingspercentage',
           'omgevingsadressendichtheid', 'stedelijkheid_adressen_per_km2',
           'bevolkingsdichtheid_inwoners_per_km2', 'aantal_inwoners', 'mannen',
           'vrouwen', 'percentage_personen_0_tot_15_jaar',
           'percentage_personen_15_tot_25_jaar',
           'percentage_personen_25_tot_45_jaar',
           'percentage_personen_45_tot_65_jaar',
           'percentage_personen_65_jaar_en_ouder', 'percentage_ongehuwd',
           'percentage_gehuwd', 'percentage_gescheid', 'percentage_verweduwd',
           'aantal_huishoudens', 'percentage_eenpersoonshuishoudens',
           'percentage_huishoudens_zonder_kinderen',
           'percentage_huishoudens_met_kinderen', 'gemiddelde_huishoudsgrootte',
           'percentage_westerse_migratieachtergrond',
           'percentage_niet_westerse_migratieachtergrond',
           'percentage_uit_marokko',
           'percentage_uit_nederlandse_antillen_en_aruba',
           'percentage_uit_suriname', 'percentage_uit_turkije',
           'percentage_overige_nietwestersemigratieachtergrond',
           'oppervlakte_totaal_in_ha', 'oppervlakte_land_in_ha',
           'oppervlakte_water_in_ha', 'jrstatcode', 'jaar'],
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
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>gemeentecode</th>
      <th>gemeentenaam</th>
      <th>indelingswijziging_wijken_en_buurten</th>
      <th>water</th>
      <th>meest_voorkomende_postcode</th>
      <th>dekkingspercentage</th>
      <th>omgevingsadressendichtheid</th>
      <th>...</th>
      <th>percentage_uit_marokko</th>
      <th>percentage_uit_nederlandse_antillen_en_aruba</th>
      <th>percentage_uit_suriname</th>
      <th>percentage_uit_turkije</th>
      <th>percentage_overige_nietwestersemigratieachtergrond</th>
      <th>oppervlakte_totaal_in_ha</th>
      <th>oppervlakte_land_in_ha</th>
      <th>oppervlakte_water_in_ha</th>
      <th>jrstatcode</th>
      <th>jaar</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BU000000</td>
      <td>Binnenstad-Noord</td>
      <td>WK001400</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9712</td>
      <td>1</td>
      <td>6712</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>8</td>
      <td>39</td>
      <td>37</td>
      <td>2</td>
      <td>2022BU00140000</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BU001406</td>
      <td>De Wierden</td>
      <td>WK001414</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9613</td>
      <td>1</td>
      <td>170</td>
      <td>...</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>59</td>
      <td>52</td>
      <td>7</td>
      <td>2022BU00141406</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000103</td>
      <td>Rivierenbuurt</td>
      <td>WK001401</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9725</td>
      <td>1</td>
      <td>4220</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>7</td>
      <td>56</td>
      <td>55</td>
      <td>1</td>
      <td>2022BU00140103</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU001003</td>
      <td>Zernike Campus</td>
      <td>WK001410</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9747</td>
      <td>1</td>
      <td>972</td>
      <td>...</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>-99999999</td>
      <td>202</td>
      <td>187</td>
      <td>15</td>
      <td>2022BU00141003</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU001701</td>
      <td>Haren-Zuidwest</td>
      <td>WK001417</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9752</td>
      <td>1</td>
      <td>951</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>141</td>
      <td>140</td>
      <td>1</td>
      <td>2022BU00141701</td>
      <td>2022</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 41 columns</p>
</div>



We don't need some of the columns here:


```python
data_buurtkaart.drop('gemeentecode', axis=1, inplace = True)
data_buurtkaart.drop('gemeentenaam', axis=1, inplace = True)
data_buurtkaart.drop('indelingswijziging_wijken_en_buurten', axis=1, inplace = True)
data_buurtkaart.drop('water', axis=1, inplace = True)
data_buurtkaart.drop('meest_voorkomende_postcode', axis=1, inplace = True)
data_buurtkaart.drop('dekkingspercentage', axis=1, inplace = True)
data_buurtkaart.drop('omgevingsadressendichtheid', axis=1, inplace = True)
data_buurtkaart.drop('stedelijkheid_adressen_per_km2', axis=1, inplace = True)
data_buurtkaart.drop('oppervlakte_totaal_in_ha', axis=1, inplace = True)
data_buurtkaart.drop('oppervlakte_land_in_ha', axis=1, inplace = True)
data_buurtkaart.drop('oppervlakte_water_in_ha', axis=1, inplace = True)
data_buurtkaart.drop('jrstatcode', axis=1, inplace = True)
data_buurtkaart.drop('jaar', axis=1, inplace = True)

print(data_buurtkaart.columns)
```

    Index(['buurtcode', 'buurtnaam', 'wijkcode',
           'bevolkingsdichtheid_inwoners_per_km2', 'aantal_inwoners', 'mannen',
           'vrouwen', 'percentage_personen_0_tot_15_jaar',
           'percentage_personen_15_tot_25_jaar',
           'percentage_personen_25_tot_45_jaar',
           'percentage_personen_45_tot_65_jaar',
           'percentage_personen_65_jaar_en_ouder', 'percentage_ongehuwd',
           'percentage_gehuwd', 'percentage_gescheid', 'percentage_verweduwd',
           'aantal_huishoudens', 'percentage_eenpersoonshuishoudens',
           'percentage_huishoudens_zonder_kinderen',
           'percentage_huishoudens_met_kinderen', 'gemiddelde_huishoudsgrootte',
           'percentage_westerse_migratieachtergrond',
           'percentage_niet_westerse_migratieachtergrond',
           'percentage_uit_marokko',
           'percentage_uit_nederlandse_antillen_en_aruba',
           'percentage_uit_suriname', 'percentage_uit_turkije',
           'percentage_overige_nietwestersemigratieachtergrond'],
          dtype='object')


And now let's read the data I choosed and downloaded from [Gronometer](https://groningen.buurtmonitor.nl/):


```python
data_overig = pd.read_csv("Groningen_population_data_2018.csv", sep=';') 
data_overig[['buurt_number', 'buurt_name']] = data_overig['Buurten'].str.extract('(?P<buurt_number>[\d]+) (?P<buurt_name>[\s\S]+)', expand=True)
data_overig['buurtcode'] = data_overig.apply(lambda row: "BU00" + str(row['buurt_number']), axis=1)
data_overig.drop('buurt_number', axis=1, inplace = True)
data_overig.drop('buurt_name', axis=1, inplace = True)
data_overig.drop('Buurten', axis=1, inplace = True)
data_overig.rename(columns={'aantal mensen met een uitkering Participatiewet (Pw)|2018': 'aantal mensen met een uitkering Participatiewet'}, inplace = True)

print(data_overig.shape)
print(data_overig.columns)
data_overig.head()
```

    (151, 15)
    Index(['aantal mensen met een uitkering Participatiewet',
           'percentage mensen met een uitkering krachtens de Participatiewet|2018',
           'bevolking totaal|2018', 'percentage minimahuishoudens|2018',
           'aantal minimahuishoudens|2018',
           'gemiddeld inkomen per persoon met inkomen|2018',
           'gemiddeld huishoudensinkomen|2018',
           'aangiften diefstal af/uit woning|2018', 'aangiften diefstal auto|2018',
           'aangiften diefstal fiets|2018',
           'aangiften diefstal af/uit bedrijf|2018',
           'aangiften diefstal af/uit auto|2018', 'aangiften van vernieling|2018',
           'aangiften van mishandeling|2018', 'buurtcode'],
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
      <th>aantal mensen met een uitkering Participatiewet</th>
      <th>percentage mensen met een uitkering krachtens de Participatiewet|2018</th>
      <th>bevolking totaal|2018</th>
      <th>percentage minimahuishoudens|2018</th>
      <th>aantal minimahuishoudens|2018</th>
      <th>gemiddeld inkomen per persoon met inkomen|2018</th>
      <th>gemiddeld huishoudensinkomen|2018</th>
      <th>aangiften diefstal af/uit woning|2018</th>
      <th>aangiften diefstal auto|2018</th>
      <th>aangiften diefstal fiets|2018</th>
      <th>aangiften diefstal af/uit bedrijf|2018</th>
      <th>aangiften diefstal af/uit auto|2018</th>
      <th>aangiften van vernieling|2018</th>
      <th>aangiften van mishandeling|2018</th>
      <th>buurtcode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>141</td>
      <td>3,5</td>
      <td>4404.0</td>
      <td>17,1</td>
      <td>290</td>
      <td>22,5</td>
      <td>30,0</td>
      <td>25.0</td>
      <td>1.0</td>
      <td>176.0</td>
      <td>17.0</td>
      <td>8.0</td>
      <td>25.0</td>
      <td>35.0</td>
      <td>BU000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>224</td>
      <td>3,7</td>
      <td>6626.0</td>
      <td>21,3</td>
      <td>530</td>
      <td>22,2</td>
      <td>30,7</td>
      <td>42.0</td>
      <td>3.0</td>
      <td>306.0</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>96.0</td>
      <td>196.0</td>
      <td>BU000001</td>
    </tr>
    <tr>
      <th>2</th>
      <td>215</td>
      <td>6,0</td>
      <td>4017.0</td>
      <td>20,4</td>
      <td>350</td>
      <td>22,1</td>
      <td>27,8</td>
      <td>17.0</td>
      <td>1.0</td>
      <td>56.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>8.0</td>
      <td>BU000002</td>
    </tr>
    <tr>
      <th>3</th>
      <td>54</td>
      <td>3,6</td>
      <td>1708.0</td>
      <td>17,9</td>
      <td>140</td>
      <td>24,6</td>
      <td>29,7</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>2.0</td>
      <td>4.0</td>
      <td>8.0</td>
      <td>5.0</td>
      <td>BU000003</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>11.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>BU000004</td>
    </tr>
  </tbody>
</table>
</div>



Let's then merge `data_dogs`, `data_buurtkaart` and `data_overig` together to the main dataset based on `buurtcode` column


```python
data = pd.merge(data_buurtkaart, data_overig, on="buurtcode")
data = pd.merge(data, data_dogs, on="buurtcode")

data.head()
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
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>bevolkingsdichtheid_inwoners_per_km2</th>
      <th>aantal_inwoners</th>
      <th>mannen</th>
      <th>vrouwen</th>
      <th>percentage_personen_0_tot_15_jaar</th>
      <th>percentage_personen_15_tot_25_jaar</th>
      <th>percentage_personen_25_tot_45_jaar</th>
      <th>...</th>
      <th>gemiddeld inkomen per persoon met inkomen|2018</th>
      <th>gemiddeld huishoudensinkomen|2018</th>
      <th>aangiften diefstal af/uit woning|2018</th>
      <th>aangiften diefstal auto|2018</th>
      <th>aangiften diefstal fiets|2018</th>
      <th>aangiften diefstal af/uit bedrijf|2018</th>
      <th>aangiften diefstal af/uit auto|2018</th>
      <th>aangiften van vernieling|2018</th>
      <th>aangiften van mishandeling|2018</th>
      <th>dogs_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BU000000</td>
      <td>Binnenstad-Noord</td>
      <td>WK001400</td>
      <td>12085</td>
      <td>4480</td>
      <td>2335</td>
      <td>2145</td>
      <td>2</td>
      <td>49</td>
      <td>33</td>
      <td>...</td>
      <td>22,5</td>
      <td>30,0</td>
      <td>25.0</td>
      <td>1.0</td>
      <td>176.0</td>
      <td>17.0</td>
      <td>8.0</td>
      <td>25.0</td>
      <td>35.0</td>
      <td>37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BU000103</td>
      <td>Rivierenbuurt</td>
      <td>WK001401</td>
      <td>8294</td>
      <td>4590</td>
      <td>2250</td>
      <td>2335</td>
      <td>5</td>
      <td>30</td>
      <td>37</td>
      <td>...</td>
      <td>26,3</td>
      <td>31,0</td>
      <td>8.0</td>
      <td>0.0</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>13.0</td>
      <td>4.0</td>
      <td>76</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000704</td>
      <td>Piccardthof</td>
      <td>WK001407</td>
      <td>1332</td>
      <td>1190</td>
      <td>595</td>
      <td>590</td>
      <td>21</td>
      <td>14</td>
      <td>12</td>
      <td>...</td>
      <td>55,1</td>
      <td>78,6</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>2.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU001301</td>
      <td>Engelbert</td>
      <td>WK001413</td>
      <td>276</td>
      <td>900</td>
      <td>470</td>
      <td>435</td>
      <td>16</td>
      <td>12</td>
      <td>21</td>
      <td>...</td>
      <td>35,9</td>
      <td>49,8</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>62</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000812</td>
      <td>De Kring</td>
      <td>WK001408</td>
      <td>3557</td>
      <td>330</td>
      <td>160</td>
      <td>170</td>
      <td>23</td>
      <td>12</td>
      <td>27</td>
      <td>...</td>
      <td>15,9</td>
      <td>25,2</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 43 columns</p>
</div>




```python
print(data.shape)
print(data.columns)
```

    (92, 43)
    Index(['buurtcode', 'buurtnaam', 'wijkcode',
           'bevolkingsdichtheid_inwoners_per_km2', 'aantal_inwoners', 'mannen',
           'vrouwen', 'percentage_personen_0_tot_15_jaar',
           'percentage_personen_15_tot_25_jaar',
           'percentage_personen_25_tot_45_jaar',
           'percentage_personen_45_tot_65_jaar',
           'percentage_personen_65_jaar_en_ouder', 'percentage_ongehuwd',
           'percentage_gehuwd', 'percentage_gescheid', 'percentage_verweduwd',
           'aantal_huishoudens', 'percentage_eenpersoonshuishoudens',
           'percentage_huishoudens_zonder_kinderen',
           'percentage_huishoudens_met_kinderen', 'gemiddelde_huishoudsgrootte',
           'percentage_westerse_migratieachtergrond',
           'percentage_niet_westerse_migratieachtergrond',
           'percentage_uit_marokko',
           'percentage_uit_nederlandse_antillen_en_aruba',
           'percentage_uit_suriname', 'percentage_uit_turkije',
           'percentage_overige_nietwestersemigratieachtergrond',
           'aantal mensen met een uitkering Participatiewet',
           'percentage mensen met een uitkering krachtens de Participatiewet|2018',
           'bevolking totaal|2018', 'percentage minimahuishoudens|2018',
           'aantal minimahuishoudens|2018',
           'gemiddeld inkomen per persoon met inkomen|2018',
           'gemiddeld huishoudensinkomen|2018',
           'aangiften diefstal af/uit woning|2018', 'aangiften diefstal auto|2018',
           'aangiften diefstal fiets|2018',
           'aangiften diefstal af/uit bedrijf|2018',
           'aangiften diefstal af/uit auto|2018', 'aangiften van vernieling|2018',
           'aangiften van mishandeling|2018', 'dogs_amount'],
          dtype='object')


We can see that a lot of columns contains "|2018" in the name, but all the data in this dataset is from 2018 year, so we can remove this part od the name. And add '_' instead of space


```python
for name, _ in data.items():
    new_name = name.replace('|2018', '')
    new_name = new_name.replace('.', '')
    new_name = new_name.replace(',', '')
    new_name = new_name.replace(':', '')
    new_name = new_name.replace('% ', 'perc ')
    new_name = new_name.replace('(', '')
    new_name = new_name.replace(')', '')
    new_name = new_name.replace(' ', '_')
    new_name = new_name.replace('_t/m_', '_')
    new_name = new_name.replace('af/uit', 'af_uit')
    data = data.rename(columns={name: new_name})


print(data.columns)
data.head()
```

    Index(['buurtcode', 'buurtnaam', 'wijkcode',
           'bevolkingsdichtheid_inwoners_per_km2', 'aantal_inwoners', 'mannen',
           'vrouwen', 'percentage_personen_0_tot_15_jaar',
           'percentage_personen_15_tot_25_jaar',
           'percentage_personen_25_tot_45_jaar',
           'percentage_personen_45_tot_65_jaar',
           'percentage_personen_65_jaar_en_ouder', 'percentage_ongehuwd',
           'percentage_gehuwd', 'percentage_gescheid', 'percentage_verweduwd',
           'aantal_huishoudens', 'percentage_eenpersoonshuishoudens',
           'percentage_huishoudens_zonder_kinderen',
           'percentage_huishoudens_met_kinderen', 'gemiddelde_huishoudsgrootte',
           'percentage_westerse_migratieachtergrond',
           'percentage_niet_westerse_migratieachtergrond',
           'percentage_uit_marokko',
           'percentage_uit_nederlandse_antillen_en_aruba',
           'percentage_uit_suriname', 'percentage_uit_turkije',
           'percentage_overige_nietwestersemigratieachtergrond',
           'aantal_mensen_met_een_uitkering_Participatiewet',
           'percentage_mensen_met_een_uitkering_krachtens_de_Participatiewet',
           'bevolking_totaal', 'percentage_minimahuishoudens',
           'aantal_minimahuishoudens', 'gemiddeld_inkomen_per_persoon_met_inkomen',
           'gemiddeld_huishoudensinkomen', 'aangiften_diefstal_af_uit_woning',
           'aangiften_diefstal_auto', 'aangiften_diefstal_fiets',
           'aangiften_diefstal_af_uit_bedrijf', 'aangiften_diefstal_af_uit_auto',
           'aangiften_van_vernieling', 'aangiften_van_mishandeling',
           'dogs_amount'],
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
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>bevolkingsdichtheid_inwoners_per_km2</th>
      <th>aantal_inwoners</th>
      <th>mannen</th>
      <th>vrouwen</th>
      <th>percentage_personen_0_tot_15_jaar</th>
      <th>percentage_personen_15_tot_25_jaar</th>
      <th>percentage_personen_25_tot_45_jaar</th>
      <th>...</th>
      <th>gemiddeld_inkomen_per_persoon_met_inkomen</th>
      <th>gemiddeld_huishoudensinkomen</th>
      <th>aangiften_diefstal_af_uit_woning</th>
      <th>aangiften_diefstal_auto</th>
      <th>aangiften_diefstal_fiets</th>
      <th>aangiften_diefstal_af_uit_bedrijf</th>
      <th>aangiften_diefstal_af_uit_auto</th>
      <th>aangiften_van_vernieling</th>
      <th>aangiften_van_mishandeling</th>
      <th>dogs_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BU000000</td>
      <td>Binnenstad-Noord</td>
      <td>WK001400</td>
      <td>12085</td>
      <td>4480</td>
      <td>2335</td>
      <td>2145</td>
      <td>2</td>
      <td>49</td>
      <td>33</td>
      <td>...</td>
      <td>22,5</td>
      <td>30,0</td>
      <td>25.0</td>
      <td>1.0</td>
      <td>176.0</td>
      <td>17.0</td>
      <td>8.0</td>
      <td>25.0</td>
      <td>35.0</td>
      <td>37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BU000103</td>
      <td>Rivierenbuurt</td>
      <td>WK001401</td>
      <td>8294</td>
      <td>4590</td>
      <td>2250</td>
      <td>2335</td>
      <td>5</td>
      <td>30</td>
      <td>37</td>
      <td>...</td>
      <td>26,3</td>
      <td>31,0</td>
      <td>8.0</td>
      <td>0.0</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>13.0</td>
      <td>4.0</td>
      <td>76</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000704</td>
      <td>Piccardthof</td>
      <td>WK001407</td>
      <td>1332</td>
      <td>1190</td>
      <td>595</td>
      <td>590</td>
      <td>21</td>
      <td>14</td>
      <td>12</td>
      <td>...</td>
      <td>55,1</td>
      <td>78,6</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>2.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU001301</td>
      <td>Engelbert</td>
      <td>WK001413</td>
      <td>276</td>
      <td>900</td>
      <td>470</td>
      <td>435</td>
      <td>16</td>
      <td>12</td>
      <td>21</td>
      <td>...</td>
      <td>35,9</td>
      <td>49,8</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>62</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000812</td>
      <td>De Kring</td>
      <td>WK001408</td>
      <td>3557</td>
      <td>330</td>
      <td>160</td>
      <td>170</td>
      <td>23</td>
      <td>12</td>
      <td>27</td>
      <td>...</td>
      <td>15,9</td>
      <td>25,2</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 43 columns</p>
</div>




```python
print(data.dtypes)
```

    buurtcode                                                            object
    buurtnaam                                                            object
    wijkcode                                                             object
    bevolkingsdichtheid_inwoners_per_km2                                  int64
    aantal_inwoners                                                       int64
    mannen                                                                int64
    vrouwen                                                               int64
    percentage_personen_0_tot_15_jaar                                     int64
    percentage_personen_15_tot_25_jaar                                    int64
    percentage_personen_25_tot_45_jaar                                    int64
    percentage_personen_45_tot_65_jaar                                    int64
    percentage_personen_65_jaar_en_ouder                                  int64
    percentage_ongehuwd                                                   int64
    percentage_gehuwd                                                     int64
    percentage_gescheid                                                   int64
    percentage_verweduwd                                                  int64
    aantal_huishoudens                                                    int64
    percentage_eenpersoonshuishoudens                                     int64
    percentage_huishoudens_zonder_kinderen                                int64
    percentage_huishoudens_met_kinderen                                   int64
    gemiddelde_huishoudsgrootte                                         float64
    percentage_westerse_migratieachtergrond                               int64
    percentage_niet_westerse_migratieachtergrond                          int64
    percentage_uit_marokko                                                int64
    percentage_uit_nederlandse_antillen_en_aruba                          int64
    percentage_uit_suriname                                               int64
    percentage_uit_turkije                                                int64
    percentage_overige_nietwestersemigratieachtergrond                    int64
    aantal_mensen_met_een_uitkering_Participatiewet                      object
    percentage_mensen_met_een_uitkering_krachtens_de_Participatiewet     object
    bevolking_totaal                                                    float64
    percentage_minimahuishoudens                                         object
    aantal_minimahuishoudens                                             object
    gemiddeld_inkomen_per_persoon_met_inkomen                            object
    gemiddeld_huishoudensinkomen                                         object
    aangiften_diefstal_af_uit_woning                                    float64
    aangiften_diefstal_auto                                             float64
    aangiften_diefstal_fiets                                            float64
    aangiften_diefstal_af_uit_bedrijf                                   float64
    aangiften_diefstal_af_uit_auto                                      float64
    aangiften_van_vernieling                                            float64
    aangiften_van_mishandeling                                          float64
    dogs_amount                                                           int64
    dtype: object


We can see that there are a lot of columns which should be numiric one, but they has a type 'object', so it means something wrong with their values. Let's explore it and maybe clean of fill the missing values. 

We'll start with 'aantal_mensen_met_een_uitkering_Participatiewet' column


```python
print(data['aantal_mensen_met_een_uitkering_Participatiewet'].value_counts())
print(data['gemiddeld_huishoudensinkomen'].value_counts())
print(data['percentage_personen_0_tot_15_jaar'].value_counts())
```

    X      29
    54      2
    14      2
    141     1
    18      1
    273     1
    291     1
    426     1
    252     1
    60      1
    229     1
    122     1
    380     1
    215     1
    155     1
    279     1
    64      1
    96      1
    153     1
    654     1
    142     1
    92      1
    313     1
    213     1
    50      1
    224     1
    21      1
    47      1
    185     1
    487     1
    11      1
    217     1
    181     1
    913     1
    35      1
    38      1
    434     1
    516     1
    12      1
    256     1
    257     1
    65      1
    10      1
    16      1
    136     1
    48      1
    302     1
    27      1
    445     1
    106     1
    17      1
    97      1
    Name: aantal_mensen_met_een_uitkering_Participatiewet, dtype: int64
    -       4
    30,0    3
    39,6    2
    27,8    2
    31,0    2
    36,8    2
    39,1    2
    30,7    2
    49,8    2
    34,3    1
    28,4    1
    30,6    1
    29,2    1
    30,3    1
    30,1    1
    64,6    1
    81,7    1
    50,5    1
    30,9    1
    43,9    1
    24,9    1
    32,1    1
    68,9    1
    27,6    1
    49,9    1
    52,6    1
    37,4    1
    32,4    1
    33,0    1
    29,9    1
    38,4    1
    37,1    1
    26,0    1
    48,3    1
    41,8    1
    25,3    1
    78,6    1
    25,2    1
    33,8    1
    63,8    1
    28,1    1
    25,6    1
    61,6    1
    42,8    1
    29,3    1
    51,8    1
    60,0    1
    31,5    1
    35,9    1
    29,7    1
    49,6    1
    38,1    1
    57,7    1
    44,7    1
    28,8    1
    34,7    1
    43,4    1
    27,2    1
    63,7    1
    Name: gemiddeld_huishoudensinkomen, dtype: int64
    -99999999    11
     13           6
     17           6
     5            6
     8            6
     16           5
     11           5
     15           5
     14           4
     18           4
     3            4
     7            3
     19           3
     23           3
     12           3
     9            3
     10           3
     20           2
     2            2
     0            2
     21           2
     4            2
     22           1
     6            1
    Name: percentage_personen_0_tot_15_jaar, dtype: int64


We can see that there are `X`, `-` and `-99999999` values which definately not a number! So let's replace this value with 0


```python
data.replace(to_replace="X", value="0", inplace=True)
data.replace(to_replace="-", value="0", inplace=True)
data.replace(to_replace=-99999999, value=0, inplace=True)

for col_name in data.select_dtypes(include=[object]):
    data[col_name] = data[col_name].str.replace(',','.')
    
data.fillna(0, inplace=True)
```


```python

```


```python
print(data.isnull().sum())
```

    buurtcode                                                           0
    buurtnaam                                                           0
    wijkcode                                                            0
    bevolkingsdichtheid_inwoners_per_km2                                0
    aantal_inwoners                                                     0
    mannen                                                              0
    vrouwen                                                             0
    percentage_personen_0_tot_15_jaar                                   0
    percentage_personen_15_tot_25_jaar                                  0
    percentage_personen_25_tot_45_jaar                                  0
    percentage_personen_45_tot_65_jaar                                  0
    percentage_personen_65_jaar_en_ouder                                0
    percentage_ongehuwd                                                 0
    percentage_gehuwd                                                   0
    percentage_gescheid                                                 0
    percentage_verweduwd                                                0
    aantal_huishoudens                                                  0
    percentage_eenpersoonshuishoudens                                   0
    percentage_huishoudens_zonder_kinderen                              0
    percentage_huishoudens_met_kinderen                                 0
    gemiddelde_huishoudsgrootte                                         0
    percentage_westerse_migratieachtergrond                             0
    percentage_niet_westerse_migratieachtergrond                        0
    percentage_uit_marokko                                              0
    percentage_uit_nederlandse_antillen_en_aruba                        0
    percentage_uit_suriname                                             0
    percentage_uit_turkije                                              0
    percentage_overige_nietwestersemigratieachtergrond                  0
    aantal_mensen_met_een_uitkering_Participatiewet                     0
    percentage_mensen_met_een_uitkering_krachtens_de_Participatiewet    0
    bevolking_totaal                                                    0
    percentage_minimahuishoudens                                        0
    aantal_minimahuishoudens                                            0
    gemiddeld_inkomen_per_persoon_met_inkomen                           0
    gemiddeld_huishoudensinkomen                                        0
    aangiften_diefstal_af_uit_woning                                    0
    aangiften_diefstal_auto                                             0
    aangiften_diefstal_fiets                                            0
    aangiften_diefstal_af_uit_bedrijf                                   0
    aangiften_diefstal_af_uit_auto                                      0
    aangiften_van_vernieling                                            0
    aangiften_van_mishandeling                                          0
    dogs_amount                                                         0
    dtype: int64



```python
cols=[i for i in data.columns if i not in ["wijkcode","buurtnaam","buurtcode"]]
for col in cols:
    data[col]=pd.to_numeric(data[col])

print(data.dtypes)
```

    buurtcode                                                            object
    buurtnaam                                                            object
    wijkcode                                                             object
    bevolkingsdichtheid_inwoners_per_km2                                  int64
    aantal_inwoners                                                       int64
    mannen                                                                int64
    vrouwen                                                               int64
    percentage_personen_0_tot_15_jaar                                     int64
    percentage_personen_15_tot_25_jaar                                    int64
    percentage_personen_25_tot_45_jaar                                    int64
    percentage_personen_45_tot_65_jaar                                    int64
    percentage_personen_65_jaar_en_ouder                                  int64
    percentage_ongehuwd                                                   int64
    percentage_gehuwd                                                     int64
    percentage_gescheid                                                   int64
    percentage_verweduwd                                                  int64
    aantal_huishoudens                                                    int64
    percentage_eenpersoonshuishoudens                                     int64
    percentage_huishoudens_zonder_kinderen                                int64
    percentage_huishoudens_met_kinderen                                   int64
    gemiddelde_huishoudsgrootte                                         float64
    percentage_westerse_migratieachtergrond                               int64
    percentage_niet_westerse_migratieachtergrond                          int64
    percentage_uit_marokko                                                int64
    percentage_uit_nederlandse_antillen_en_aruba                          int64
    percentage_uit_suriname                                               int64
    percentage_uit_turkije                                                int64
    percentage_overige_nietwestersemigratieachtergrond                    int64
    aantal_mensen_met_een_uitkering_Participatiewet                       int64
    percentage_mensen_met_een_uitkering_krachtens_de_Participatiewet    float64
    bevolking_totaal                                                    float64
    percentage_minimahuishoudens                                        float64
    aantal_minimahuishoudens                                              int64
    gemiddeld_inkomen_per_persoon_met_inkomen                           float64
    gemiddeld_huishoudensinkomen                                        float64
    aangiften_diefstal_af_uit_woning                                    float64
    aangiften_diefstal_auto                                             float64
    aangiften_diefstal_fiets                                            float64
    aangiften_diefstal_af_uit_bedrijf                                   float64
    aangiften_diefstal_af_uit_auto                                      float64
    aangiften_van_vernieling                                            float64
    aangiften_van_mishandeling                                          float64
    dogs_amount                                                           int64
    dtype: object



```python
print(data.columns)
data.head()
```

    Index(['buurtcode', 'buurtnaam', 'wijkcode',
           'bevolkingsdichtheid_inwoners_per_km2', 'aantal_inwoners', 'mannen',
           'vrouwen', 'percentage_personen_0_tot_15_jaar',
           'percentage_personen_15_tot_25_jaar',
           'percentage_personen_25_tot_45_jaar',
           'percentage_personen_45_tot_65_jaar',
           'percentage_personen_65_jaar_en_ouder', 'percentage_ongehuwd',
           'percentage_gehuwd', 'percentage_gescheid', 'percentage_verweduwd',
           'aantal_huishoudens', 'percentage_eenpersoonshuishoudens',
           'percentage_huishoudens_zonder_kinderen',
           'percentage_huishoudens_met_kinderen', 'gemiddelde_huishoudsgrootte',
           'percentage_westerse_migratieachtergrond',
           'percentage_niet_westerse_migratieachtergrond',
           'percentage_uit_marokko',
           'percentage_uit_nederlandse_antillen_en_aruba',
           'percentage_uit_suriname', 'percentage_uit_turkije',
           'percentage_overige_nietwestersemigratieachtergrond',
           'aantal_mensen_met_een_uitkering_Participatiewet',
           'percentage_mensen_met_een_uitkering_krachtens_de_Participatiewet',
           'bevolking_totaal', 'percentage_minimahuishoudens',
           'aantal_minimahuishoudens', 'gemiddeld_inkomen_per_persoon_met_inkomen',
           'gemiddeld_huishoudensinkomen', 'aangiften_diefstal_af_uit_woning',
           'aangiften_diefstal_auto', 'aangiften_diefstal_fiets',
           'aangiften_diefstal_af_uit_bedrijf', 'aangiften_diefstal_af_uit_auto',
           'aangiften_van_vernieling', 'aangiften_van_mishandeling',
           'dogs_amount'],
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
      <th>buurtcode</th>
      <th>buurtnaam</th>
      <th>wijkcode</th>
      <th>bevolkingsdichtheid_inwoners_per_km2</th>
      <th>aantal_inwoners</th>
      <th>mannen</th>
      <th>vrouwen</th>
      <th>percentage_personen_0_tot_15_jaar</th>
      <th>percentage_personen_15_tot_25_jaar</th>
      <th>percentage_personen_25_tot_45_jaar</th>
      <th>...</th>
      <th>gemiddeld_inkomen_per_persoon_met_inkomen</th>
      <th>gemiddeld_huishoudensinkomen</th>
      <th>aangiften_diefstal_af_uit_woning</th>
      <th>aangiften_diefstal_auto</th>
      <th>aangiften_diefstal_fiets</th>
      <th>aangiften_diefstal_af_uit_bedrijf</th>
      <th>aangiften_diefstal_af_uit_auto</th>
      <th>aangiften_van_vernieling</th>
      <th>aangiften_van_mishandeling</th>
      <th>dogs_amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BU000000</td>
      <td>Binnenstad-Noord</td>
      <td>WK001400</td>
      <td>12085</td>
      <td>4480</td>
      <td>2335</td>
      <td>2145</td>
      <td>2</td>
      <td>49</td>
      <td>33</td>
      <td>...</td>
      <td>22.5</td>
      <td>30.0</td>
      <td>25.0</td>
      <td>1.0</td>
      <td>176.0</td>
      <td>17.0</td>
      <td>8.0</td>
      <td>25.0</td>
      <td>35.0</td>
      <td>37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BU000103</td>
      <td>Rivierenbuurt</td>
      <td>WK001401</td>
      <td>8294</td>
      <td>4590</td>
      <td>2250</td>
      <td>2335</td>
      <td>5</td>
      <td>30</td>
      <td>37</td>
      <td>...</td>
      <td>26.3</td>
      <td>31.0</td>
      <td>8.0</td>
      <td>0.0</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>13.0</td>
      <td>4.0</td>
      <td>76</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000704</td>
      <td>Piccardthof</td>
      <td>WK001407</td>
      <td>1332</td>
      <td>1190</td>
      <td>595</td>
      <td>590</td>
      <td>21</td>
      <td>14</td>
      <td>12</td>
      <td>...</td>
      <td>55.1</td>
      <td>78.6</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>2.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU001301</td>
      <td>Engelbert</td>
      <td>WK001413</td>
      <td>276</td>
      <td>900</td>
      <td>470</td>
      <td>435</td>
      <td>16</td>
      <td>12</td>
      <td>21</td>
      <td>...</td>
      <td>35.9</td>
      <td>49.8</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>62</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000812</td>
      <td>De Kring</td>
      <td>WK001408</td>
      <td>3557</td>
      <td>330</td>
      <td>160</td>
      <td>170</td>
      <td>23</td>
      <td>12</td>
      <td>27</td>
      <td>...</td>
      <td>15.9</td>
      <td>25.2</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 43 columns</p>
</div>




```python
data['politie_report_aantal'] = data['aangiften_diefstal_af_uit_woning'] + data['aangiften_diefstal_auto'] + data['aangiften_diefstal_af_uit_bedrijf'] + data['aangiften_diefstal_fiets'] + data['aangiften_diefstal_af_uit_auto'] + data['aangiften_van_mishandeling'] + data['aangiften_van_vernieling']
```


```python
data.to_csv(r'population_clean_data.csv', index = False)
```

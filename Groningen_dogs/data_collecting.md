---

title:  Dogs owners dataset construction

---

On this page I will show you where I got data, how I collected, investigated and clean it.

There is a website called [Gronometer](https://groningen.buurtmonitor.nl/) where you can find a lot of open data about Groningen. This data is also available to download as datasets. I choosed some data from 2018 year available for different neighbourhoods and downloaded it to `Groningen_population_data_2018.csv` file.



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



Now I would like to read and explore the data from [Wijk- en buurtkaart 2018 dataset](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2018). I saved a data about neighbourhoods to `.csv` file. 

First I needed to filter this data because only data about Groningen is needed:

``` 
data_groningen = new_data[new_data['gemeentenaam'] == "Groningen"]

```
and then I saved it back to `.csv` file

Let's take a look at what we have here:


```python
data_buurtkaart = pd.read_csv('groningen_buurten_2018.csv')
data_buurtkaart['buurtcode'] = data_buurtkaart['buurtcode'].astype(str).str.replace('BU0014', 'BU00')

print(data_buurtkaart.shape)
data_buurtkaart.head()
```

    (110, 209)





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
      <th>0</th>
      <td>151</td>
      <td>GP</td>
      <td>BU000000</td>
      <td>Binnenstad-Noord</td>
      <td>WK001400</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9712</td>
      <td>...</td>
      <td>0.6</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>0.3</td>
      <td>5.0</td>
      <td>5.9</td>
      <td>17.0</td>
      <td>2018BU00140000</td>
      <td>2018.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>152</td>
      <td>GP</td>
      <td>BU000001</td>
      <td>Binnenstad-Zuid</td>
      <td>WK001400</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9711</td>
      <td>...</td>
      <td>0.4</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>0.4</td>
      <td>5.0</td>
      <td>5.6</td>
      <td>17.0</td>
      <td>2018BU00140001</td>
      <td>2018.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>153</td>
      <td>GP</td>
      <td>BU000002</td>
      <td>Binnenstad-Oost</td>
      <td>WK001400</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9711</td>
      <td>...</td>
      <td>0.4</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>0.5</td>
      <td>5.0</td>
      <td>5.3</td>
      <td>17.0</td>
      <td>2018BU00140002</td>
      <td>2018.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>154</td>
      <td>GP</td>
      <td>BU000003</td>
      <td>Binnenstad-West</td>
      <td>WK001400</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9718</td>
      <td>...</td>
      <td>1.1</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>0.3</td>
      <td>5.0</td>
      <td>6.3</td>
      <td>17.0</td>
      <td>2018BU00140003</td>
      <td>2018.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>155</td>
      <td>GP</td>
      <td>BU000004</td>
      <td>Noorderplantsoen</td>
      <td>WK001400</td>
      <td>GM0014</td>
      <td>Groningen</td>
      <td>1</td>
      <td>NEE</td>
      <td>9717</td>
      <td>...</td>
      <td>1.0</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>0.5</td>
      <td>5.0</td>
      <td>6.0</td>
      <td>17.0</td>
      <td>2018BU00140004</td>
      <td>2018.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 209 columns</p>
</div>



We don't need some of the columns here:


```python
data_groningen = data_buurtkaart.copy()
data_groningen.drop('fid', axis=1, inplace = True)
data_groningen.drop('geom', axis=1, inplace = True)
data_groningen.drop('gemeentecode', axis=1, inplace = True)
data_groningen.drop('gemeentenaam', axis=1, inplace = True)
data_groningen.drop('indelingswijziging_wijken_en_buurten', axis=1, inplace = True)
data_groningen.drop('water', axis=1, inplace = True)
data_groningen.drop('meest_voorkomende_postcode', axis=1, inplace = True)
data_groningen.drop('dekkingspercentage', axis=1, inplace = True)
data_groningen.drop('omgevingsadressendichtheid', axis=1, inplace = True)
data_groningen.drop('stedelijkheid_adressen_per_km2', axis=1, inplace = True)
data_groningen.drop('aantal_inwoners', axis=1, inplace = True)
data_groningen.drop('mannen', axis=1, inplace = True)
data_groningen.drop('vrouwen', axis=1, inplace = True)
data_groningen.drop('geboorte_totaal', axis=1, inplace = True)
data_groningen.drop('geboortes_per_1000_inwoners', axis=1, inplace = True)
data_groningen.drop('sterfte_totaal', axis=1, inplace = True)
data_groningen.drop('sterfte_relatief', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijven_nijverheid_energie', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijven_handel_en_horeca', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijven_vervoer_informatie_communicatie', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijven_financieel_onroerend_goed', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijven_zakelijke_dienstverlening', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijven_cultuur_recreatie_overige', axis=1, inplace = True)
data_groningen.drop('aantal_bedrijfsvestigingen', axis=1, inplace = True)
data_groningen.drop('woningvoorraad', axis=1, inplace = True)
data_groningen.drop('percentage_bewoond', axis=1, inplace = True)
data_groningen.drop('percentage_bouwjaarklasse_tot_2000', axis=1, inplace = True)
data_groningen.drop('percentage_bouwjaarklasse_vanaf_2000', axis=1, inplace = True)
data_groningen.drop('percentage_leegstand_woningen', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_totaal', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_appartement', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_tussenwoning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_hoekwoning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_2_onder_1_kap_woning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_vrijstaande_woning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruik_huurwoning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gasverbruikkoopwoning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_elektriciteitsverbruik_totaal', axis=1, inplace = True)
data_groningen.drop('gemiddeld_elektriciteitsverbruik_appartement', axis=1, inplace = True)
data_groningen.drop('gemiddeld_elektriciteitsverbruik_tussenwoning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_elektriciteitsverbruik_hoekwoning', axis=1, inplace = True)
data_groningen.drop('gem_elektriciteitsverbruik_2_onder_1_kap_woning', axis=1, inplace = True)
data_groningen.drop('gem_elektriciteitsverbruik_vrijstaande_woning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_elektriciteitsverbruik_huurwoning', axis=1, inplace = True)
data_groningen.drop('gemiddeld_elektriciteitsverbruikkoopwoning', axis=1, inplace = True)
data_groningen.drop('netto_arbeidsparticipatie', axis=1, inplace = True)
data_groningen.drop('aantal_inkomensontvangers', axis=1, inplace = True)
data_groningen.drop('gemiddeld_inkomen_per_inkomensontvanger', axis=1, inplace = True)
data_groningen.drop('percentage_personen_met_laag_inkomen', axis=1, inplace = True)
data_groningen.drop('percentage_personen_met_hoog_inkomen', axis=1, inplace = True)
data_groningen.drop('aantal_personen_met_een_ao_uitkering_totaal', axis=1, inplace = True)
data_groningen.drop('aantal_personen_met_een_ww_uitkering_totaal', axis=1, inplace = True)
data_groningen.drop('percentage_huishoudens_onder_of_rond_sociaal_minimum', axis=1, inplace = True)
data_groningen.drop('percentage_huishoudens_met_lage_koopkracht', axis=1, inplace = True)
data_groningen.drop('aantal_personen_met_een_aow_uitkering_totaal', axis=1, inplace = True)
data_groningen.drop('gemiddeld_gestandaardiseerd_inkomen_van_huishoudens', axis=1, inplace = True)
data_groningen.drop('huishoudens_tot_110_percent_van_sociaal_minimum', axis=1, inplace = True)
data_groningen.drop('huishoudens_tot_120_percent_van_sociaal_minimum', axis=1, inplace = True)
data_groningen.drop('mediaan_vermogen_van_particuliere_huish', axis=1, inplace = True)
data_groningen.drop('aantal_jongeren_met_jeugdzorg_in_natura', axis=1, inplace = True)
data_groningen.drop('percentage_jongeren_met_jeugdzorg_in_natura', axis=1, inplace = True)
data_groningen.drop('aantal_Wmo_cliënten', axis=1, inplace = True)
data_groningen.drop('aantal_Wmo_cliënten_per_1000_inwoners', axis=1, inplace = True)
data_groningen.drop('personenautos_per_km2', axis=1, inplace = True)
data_groningen.drop('aantal_personenautos_met_brandstof_benzine', axis=1, inplace = True)
data_groningen.drop('aantal_personenautos_met_overige_brandstof', axis=1, inplace = True)
data_groningen.drop('oppervlakte_totaal_in_ha', axis=1, inplace = True)
data_groningen.drop('oppervlakte_land_in_ha', axis=1, inplace = True)
data_groningen.drop('oppervlakte_water_in_ha', axis=1, inplace = True)
data_groningen.drop('huisartsenpraktijk_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('huisartsenpraktijk_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('huisartsenpraktijk_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('huisartsenpraktijk_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('huisartsenpost_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('apotheek_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_incl_buitenpolikliniek_gem_afst_in_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_incl_buitenpoli_gem_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_incl_buitenpoli_gem_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_incl_buitenpoli_gem_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_excl_buitenpolikliniek_gem_afst_in_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_excl_buitenpoli_gem_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_excl_buitenpoli_gem_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('ziekenhuis_excl_buitenpoli_gem_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('grote_supermarkt_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('grote_supermarkt_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('grote_supermarkt_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('grote_supermarkt_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('winkels_ov_dagelijkse_levensm_gem_afst_in_km', axis=1, inplace = True)
data_groningen.drop('winkels_ov_dagel_levensm_gem_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('winkels_ov_dagel_levensm_gem_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('winkels_ov_dagel_levensm_gem_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('warenhuis_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('warenhuis_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('warenhuis_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('warenhuis_gemiddeld_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('cafe_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('cafe_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('cafe_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('cafe_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('cafetaria_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('cafetaria_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('cafetaria_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('cafetaria_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('restaurant_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('restaurant_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('restaurant_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('restaurant_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('hotel_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('hotel_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('hotel_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('hotel_gemiddeld_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('kinderdagverblijf_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('kinderdagverblijf_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('kinderdagverblijf_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('kinderdagverblijf_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('buitenschoolse_opvang_gem_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('buitenschoolse_opvang_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('buitenschoolse_opvang_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('buitenschoolse_opvang_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('basisonderwijs_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('basisonderwijs_gemiddeld_aantal_binnen_1_km', axis=1, inplace = True)
data_groningen.drop('basisonderwijs_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('basisonderwijs_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('voortgezet_onderwijs_gem_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('voortgezet_onderwijs_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('voortgezet_onderwijs_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('voortgezet_onderwijs_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('vmbo_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('vmbo_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('vmbo_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('vmbo_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('havo_vwo_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('havo_vwo_gemiddeld_aantal_binnen_3_km', axis=1, inplace = True)
data_groningen.drop('havo_vwo_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('havo_vwo_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('brandweerkazerne_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('oprit_hoofdverkeersweg_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('treinstation_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('overstapstation_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('zwembad_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('kunstijsbaan_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('bibliotheek_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('poppodium_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('bioscoop_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('bioscoop_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('bioscoop_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('bioscoop_gemiddeld_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('sauna_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('zonnebank_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('attractiepark_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('attractiepark_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('attractiepark_gemiddeld_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('attractiepark_gemiddeld_aantal_binnen_50_km', axis=1, inplace = True)
data_groningen.drop('theater_gemiddelde_afstand_in_km', axis=1, inplace = True)
data_groningen.drop('theater_gemiddeld_aantal_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('theater_gemiddeld_aantal_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('theater_gemiddeld_aantal_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('gemiddelde_afstand_tot_museum', axis=1, inplace = True)
data_groningen.drop('gemiddeld_aantal_musea_binnen_5_km', axis=1, inplace = True)
data_groningen.drop('gemiddeld_aantal_musea_binnen_10_km', axis=1, inplace = True)
data_groningen.drop('gemiddeld_aantal_musea_binnen_20_km', axis=1, inplace = True)
data_groningen.drop('percentage_woningen_met_eigendom_onbekend', axis=1, inplace = True)
data_groningen.drop('vernieling_misdrijf_tegen_openbare_orde', axis=1, inplace = True)
data_groningen.drop('totaal_diefstal', axis=1, inplace = True)
data_groningen.drop('jaarstatcode', axis=1, inplace = True)
data_groningen.drop('jaar', axis=1, inplace = True)
```

And now let's read the data I choosed and downloaded from [Gronometer](https://groningen.buurtmonitor.nl/):


```python
data_overig = pd.read_csv("Groningen_population_data_2018.csv", sep=';') 
data_overig[['buurt_number', 'buurt_name']] = data_overig['Buurten'].str.extract('(?P<buurt_number>[\d]+) (?P<buurt_name>[\s\S]+)', expand=True)
data_overig['buurtcode'] = data_overig.apply(lambda row: "BU00" + str(row['buurt_number']), axis=1)

data_overig.drop('buurt_number', axis=1, inplace = True)
data_overig.drop('buurt_name', axis=1, inplace = True)
data_overig.drop('Buurten', axis=1, inplace = True)
data_overig.drop('aantal mensen met een uitkering Participatiewet (Pw)|2018', axis=1, inplace = True)
data_overig.drop('percentage mensen met een uitkering krachtens de Participatiewet|2018', axis=1, inplace = True)
data_overig.drop('gemiddeld inkomen per persoon met inkomen|2018', axis=1, inplace = True)
data_overig.rename(columns={'aantal mensen met een uitkering Participatiewet (Pw)|2018': 'aantal mensen met een uitkering Participatiewet'}, inplace = True)

print(data_overig.shape)
print(data_overig.columns)
data_overig.head()
```

    (151, 12)
    Index(['bevolking totaal|2018', 'percentage minimahuishoudens|2018',
           'aantal minimahuishoudens|2018', 'gemiddeld huishoudensinkomen|2018',
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
      <th>bevolking totaal|2018</th>
      <th>percentage minimahuishoudens|2018</th>
      <th>aantal minimahuishoudens|2018</th>
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
      <td>4404.0</td>
      <td>17,1</td>
      <td>290</td>
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
      <td>6626.0</td>
      <td>21,3</td>
      <td>530</td>
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
      <td>4017.0</td>
      <td>20,4</td>
      <td>350</td>
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
      <td>1708.0</td>
      <td>17,9</td>
      <td>140</td>
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
      <td>11.0</td>
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



Let's then merge `data_dogs`, `data_groningen` and `data_overig` together to the main dataset based on `buurtcode` column


```python
data = pd.merge(data_groningen, data_overig, on="buurtcode")
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
      <th>percentage_personen_0_tot_15_jaar</th>
      <th>percentage_personen_15_tot_25_jaar</th>
      <th>percentage_personen_25_tot_45_jaar</th>
      <th>percentage_personen_45_tot_65_jaar</th>
      <th>percentage_personen_65_jaar_en_ouder</th>
      <th>percentage_ongehuwd</th>
      <th>...</th>
      <th>aantal minimahuishoudens|2018</th>
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
      <td>11891.0</td>
      <td>2.0</td>
      <td>46.0</td>
      <td>35.0</td>
      <td>11.0</td>
      <td>6.0</td>
      <td>86.0</td>
      <td>...</td>
      <td>290</td>
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
      <td>BU000001</td>
      <td>Binnenstad-Zuid</td>
      <td>WK001400</td>
      <td>12011.0</td>
      <td>2.0</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>11.0</td>
      <td>6.0</td>
      <td>87.0</td>
      <td>...</td>
      <td>530</td>
      <td>30,7</td>
      <td>42.0</td>
      <td>3.0</td>
      <td>306.0</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>96.0</td>
      <td>196.0</td>
      <td>51</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000002</td>
      <td>Binnenstad-Oost</td>
      <td>WK001400</td>
      <td>14979.0</td>
      <td>3.0</td>
      <td>40.0</td>
      <td>36.0</td>
      <td>14.0</td>
      <td>7.0</td>
      <td>84.0</td>
      <td>...</td>
      <td>350</td>
      <td>27,8</td>
      <td>17.0</td>
      <td>1.0</td>
      <td>56.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>8.0</td>
      <td>48</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU000003</td>
      <td>Binnenstad-West</td>
      <td>WK001400</td>
      <td>17507.0</td>
      <td>2.0</td>
      <td>38.0</td>
      <td>40.0</td>
      <td>11.0</td>
      <td>9.0</td>
      <td>84.0</td>
      <td>...</td>
      <td>140</td>
      <td>29,7</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>2.0</td>
      <td>4.0</td>
      <td>8.0</td>
      <td>5.0</td>
      <td>21</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000005</td>
      <td>Hortusbuurt-Ebbingekwartier</td>
      <td>WK001400</td>
      <td>12509.0</td>
      <td>5.0</td>
      <td>41.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>9.0</td>
      <td>82.0</td>
      <td>...</td>
      <td>450</td>
      <td>32,1</td>
      <td>20.0</td>
      <td>2.0</td>
      <td>68.0</td>
      <td>16.0</td>
      <td>9.0</td>
      <td>24.0</td>
      <td>13.0</td>
      <td>108</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 55 columns</p>
</div>




```python
print(data.shape)
print(data.columns)
```

    (90, 55)
    Index(['buurtcode', 'buurtnaam', 'wijkcode',
           'bevolkingsdichtheid_inwoners_per_km2',
           'percentage_personen_0_tot_15_jaar',
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
           'aantal_bedrijven_landbouw_bosbouw_visserij', 'gemiddelde_woningwaarde',
           'percentage_eengezinswoning', 'percentage_meergezinswoning',
           'percentage_koopwoningen', 'percentage_huurwoningen',
           'perc_huurwoningen_in_bezit_woningcorporaties',
           'perc_huurwoningen_in_bezit_overige_verhuurders',
           'percentage_werknemers', 'percentage_zelfstandigen',
           'gemiddeld_inkomen_per_inwoner',
           'percentage_huishoudens_met_laag_inkomen',
           'percentage_huishoudens_met_hoog_inkomen',
           'aantal_personen_met_een_algemene_bijstandsuitkering_totaal',
           'gewelds_en_seksuele_misdrijven', 'personenautos_totaal',
           'personenautos_per_huishouden', 'motortweewielers_totaal',
           'bevolking totaal|2018', 'percentage minimahuishoudens|2018',
           'aantal minimahuishoudens|2018', 'gemiddeld huishoudensinkomen|2018',
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
           'bevolkingsdichtheid_inwoners_per_km2',
           'percentage_personen_0_tot_15_jaar',
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
           'aantal_bedrijven_landbouw_bosbouw_visserij', 'gemiddelde_woningwaarde',
           'percentage_eengezinswoning', 'percentage_meergezinswoning',
           'percentage_koopwoningen', 'percentage_huurwoningen',
           'perc_huurwoningen_in_bezit_woningcorporaties',
           'perc_huurwoningen_in_bezit_overige_verhuurders',
           'percentage_werknemers', 'percentage_zelfstandigen',
           'gemiddeld_inkomen_per_inwoner',
           'percentage_huishoudens_met_laag_inkomen',
           'percentage_huishoudens_met_hoog_inkomen',
           'aantal_personen_met_een_algemene_bijstandsuitkering_totaal',
           'gewelds_en_seksuele_misdrijven', 'personenautos_totaal',
           'personenautos_per_huishouden', 'motortweewielers_totaal',
           'bevolking_totaal', 'percentage_minimahuishoudens',
           'aantal_minimahuishoudens', 'gemiddeld_huishoudensinkomen',
           'aangiften_diefstal_af_uit_woning', 'aangiften_diefstal_auto',
           'aangiften_diefstal_fiets', 'aangiften_diefstal_af_uit_bedrijf',
           'aangiften_diefstal_af_uit_auto', 'aangiften_van_vernieling',
           'aangiften_van_mishandeling', 'dogs_amount'],
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
      <th>percentage_personen_0_tot_15_jaar</th>
      <th>percentage_personen_15_tot_25_jaar</th>
      <th>percentage_personen_25_tot_45_jaar</th>
      <th>percentage_personen_45_tot_65_jaar</th>
      <th>percentage_personen_65_jaar_en_ouder</th>
      <th>percentage_ongehuwd</th>
      <th>...</th>
      <th>aantal_minimahuishoudens</th>
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
      <td>11891.0</td>
      <td>2.0</td>
      <td>46.0</td>
      <td>35.0</td>
      <td>11.0</td>
      <td>6.0</td>
      <td>86.0</td>
      <td>...</td>
      <td>290</td>
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
      <td>BU000001</td>
      <td>Binnenstad-Zuid</td>
      <td>WK001400</td>
      <td>12011.0</td>
      <td>2.0</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>11.0</td>
      <td>6.0</td>
      <td>87.0</td>
      <td>...</td>
      <td>530</td>
      <td>30,7</td>
      <td>42.0</td>
      <td>3.0</td>
      <td>306.0</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>96.0</td>
      <td>196.0</td>
      <td>51</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000002</td>
      <td>Binnenstad-Oost</td>
      <td>WK001400</td>
      <td>14979.0</td>
      <td>3.0</td>
      <td>40.0</td>
      <td>36.0</td>
      <td>14.0</td>
      <td>7.0</td>
      <td>84.0</td>
      <td>...</td>
      <td>350</td>
      <td>27,8</td>
      <td>17.0</td>
      <td>1.0</td>
      <td>56.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>8.0</td>
      <td>48</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU000003</td>
      <td>Binnenstad-West</td>
      <td>WK001400</td>
      <td>17507.0</td>
      <td>2.0</td>
      <td>38.0</td>
      <td>40.0</td>
      <td>11.0</td>
      <td>9.0</td>
      <td>84.0</td>
      <td>...</td>
      <td>140</td>
      <td>29,7</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>2.0</td>
      <td>4.0</td>
      <td>8.0</td>
      <td>5.0</td>
      <td>21</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000005</td>
      <td>Hortusbuurt-Ebbingekwartier</td>
      <td>WK001400</td>
      <td>12509.0</td>
      <td>5.0</td>
      <td>41.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>9.0</td>
      <td>82.0</td>
      <td>...</td>
      <td>450</td>
      <td>32,1</td>
      <td>20.0</td>
      <td>2.0</td>
      <td>68.0</td>
      <td>16.0</td>
      <td>9.0</td>
      <td>24.0</td>
      <td>13.0</td>
      <td>108</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 55 columns</p>
</div>




```python
print(data.dtypes)
```

    buurtcode                                                      object
    buurtnaam                                                      object
    wijkcode                                                       object
    bevolkingsdichtheid_inwoners_per_km2                          float64
    percentage_personen_0_tot_15_jaar                             float64
    percentage_personen_15_tot_25_jaar                            float64
    percentage_personen_25_tot_45_jaar                            float64
    percentage_personen_45_tot_65_jaar                            float64
    percentage_personen_65_jaar_en_ouder                          float64
    percentage_ongehuwd                                           float64
    percentage_gehuwd                                             float64
    percentage_gescheid                                           float64
    percentage_verweduwd                                          float64
    aantal_huishoudens                                            float64
    percentage_eenpersoonshuishoudens                             float64
    percentage_huishoudens_zonder_kinderen                        float64
    percentage_huishoudens_met_kinderen                           float64
    gemiddelde_huishoudsgrootte                                   float64
    percentage_westerse_migratieachtergrond                       float64
    percentage_niet_westerse_migratieachtergrond                  float64
    percentage_uit_marokko                                        float64
    percentage_uit_nederlandse_antillen_en_aruba                  float64
    percentage_uit_suriname                                       float64
    percentage_uit_turkije                                        float64
    percentage_overige_nietwestersemigratieachtergrond            float64
    aantal_bedrijven_landbouw_bosbouw_visserij                    float64
    gemiddelde_woningwaarde                                       float64
    percentage_eengezinswoning                                    float64
    percentage_meergezinswoning                                   float64
    percentage_koopwoningen                                       float64
    percentage_huurwoningen                                       float64
    perc_huurwoningen_in_bezit_woningcorporaties                  float64
    perc_huurwoningen_in_bezit_overige_verhuurders                float64
    percentage_werknemers                                         float64
    percentage_zelfstandigen                                      float64
    gemiddeld_inkomen_per_inwoner                                 float64
    percentage_huishoudens_met_laag_inkomen                       float64
    percentage_huishoudens_met_hoog_inkomen                       float64
    aantal_personen_met_een_algemene_bijstandsuitkering_totaal    float64
    gewelds_en_seksuele_misdrijven                                float64
    personenautos_totaal                                          float64
    personenautos_per_huishouden                                  float64
    motortweewielers_totaal                                       float64
    bevolking_totaal                                              float64
    percentage_minimahuishoudens                                   object
    aantal_minimahuishoudens                                       object
    gemiddeld_huishoudensinkomen                                   object
    aangiften_diefstal_af_uit_woning                              float64
    aangiften_diefstal_auto                                       float64
    aangiften_diefstal_fiets                                      float64
    aangiften_diefstal_af_uit_bedrijf                             float64
    aangiften_diefstal_af_uit_auto                                float64
    aangiften_van_vernieling                                      float64
    aangiften_van_mishandeling                                    float64
    dogs_amount                                                     int64
    dtype: object


We can see that there are a lot of columns which should be numiric one, but they has a type 'object', so it means something wrong with their values. Let's explore it and maybe clean of fill the missing values. 

We'll start with 'aantal_mensen_met_een_uitkering_Participatiewet' column


```python
print(data['aantal_minimahuishoudens'].value_counts())
print(data['gemiddeld_huishoudensinkomen'].value_counts())
print(data['percentage_personen_0_tot_15_jaar'].value_counts())
```

    X       6
    450     5
    60      4
    10      4
    130     3
    30      3
    20      3
    15      2
    460     2
    570     2
    350     2
    280     2
    40      2
    430     1
    170     1
    510     1
    260     1
    600     1
    680     1
    190     1
    870     1
    690     1
    290     1
    90      1
    530     1
    550     1
    330     1
    1300    1
    700     1
    320     1
    240     1
    220     1
    230     1
    100     1
    110     1
    80      1
    140     1
    14      1
    Name: aantal_minimahuishoudens, dtype: int64
    -       4
    30,0    3
    27,8    2
    30,7    2
    49,8    2
    39,1    2
    39,6    2
    31,0    2
    60,0    1
    28,8    1
    25,2    1
    63,8    1
    78,6    1
    36,8    1
    32,4    1
    61,6    1
    63,7    1
    29,2    1
    28,4    1
    49,9    1
    27,2    1
    30,3    1
    34,3    1
    29,9    1
    49,6    1
    64,6    1
    48,3    1
    33,8    1
    44,7    1
    41,8    1
    37,4    1
    35,9    1
    52,6    1
    29,3    1
    29,7    1
    32,1    1
    43,4    1
    30,9    1
    31,5    1
    27,6    1
    30,1    1
    38,1    1
    38,4    1
    34,7    1
    37,1    1
    28,1    1
    25,3    1
    25,6    1
    33,0    1
    26,0    1
    24,9    1
    30,6    1
    43,9    1
    51,8    1
    42,8    1
    68,9    1
    81,7    1
    50,5    1
    Name: gemiddeld_huishoudensinkomen, dtype: int64
    -99999999.0    12
     10.0           7
     16.0           6
     13.0           5
     5.0            5
     8.0            5
     19.0           5
     14.0           5
     7.0            4
     17.0           4
     21.0           4
     18.0           3
     15.0           3
     2.0            3
     4.0            3
     12.0           2
     11.0           2
     6.0            2
     29.0           2
     9.0            2
     20.0           1
     3.0            1
     1.0            1
     25.0           1
     24.0           1
     26.0           1
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
print(data.isnull().sum())
```

    buurtcode                                                     0
    buurtnaam                                                     0
    wijkcode                                                      0
    bevolkingsdichtheid_inwoners_per_km2                          0
    percentage_personen_0_tot_15_jaar                             0
    percentage_personen_15_tot_25_jaar                            0
    percentage_personen_25_tot_45_jaar                            0
    percentage_personen_45_tot_65_jaar                            0
    percentage_personen_65_jaar_en_ouder                          0
    percentage_ongehuwd                                           0
    percentage_gehuwd                                             0
    percentage_gescheid                                           0
    percentage_verweduwd                                          0
    aantal_huishoudens                                            0
    percentage_eenpersoonshuishoudens                             0
    percentage_huishoudens_zonder_kinderen                        0
    percentage_huishoudens_met_kinderen                           0
    gemiddelde_huishoudsgrootte                                   0
    percentage_westerse_migratieachtergrond                       0
    percentage_niet_westerse_migratieachtergrond                  0
    percentage_uit_marokko                                        0
    percentage_uit_nederlandse_antillen_en_aruba                  0
    percentage_uit_suriname                                       0
    percentage_uit_turkije                                        0
    percentage_overige_nietwestersemigratieachtergrond            0
    aantal_bedrijven_landbouw_bosbouw_visserij                    0
    gemiddelde_woningwaarde                                       0
    percentage_eengezinswoning                                    0
    percentage_meergezinswoning                                   0
    percentage_koopwoningen                                       0
    percentage_huurwoningen                                       0
    perc_huurwoningen_in_bezit_woningcorporaties                  0
    perc_huurwoningen_in_bezit_overige_verhuurders                0
    percentage_werknemers                                         0
    percentage_zelfstandigen                                      0
    gemiddeld_inkomen_per_inwoner                                 0
    percentage_huishoudens_met_laag_inkomen                       0
    percentage_huishoudens_met_hoog_inkomen                       0
    aantal_personen_met_een_algemene_bijstandsuitkering_totaal    0
    gewelds_en_seksuele_misdrijven                                0
    personenautos_totaal                                          0
    personenautos_per_huishouden                                  0
    motortweewielers_totaal                                       0
    bevolking_totaal                                              0
    percentage_minimahuishoudens                                  0
    aantal_minimahuishoudens                                      0
    gemiddeld_huishoudensinkomen                                  0
    aangiften_diefstal_af_uit_woning                              0
    aangiften_diefstal_auto                                       0
    aangiften_diefstal_fiets                                      0
    aangiften_diefstal_af_uit_bedrijf                             0
    aangiften_diefstal_af_uit_auto                                0
    aangiften_van_vernieling                                      0
    aangiften_van_mishandeling                                    0
    dogs_amount                                                   0
    dtype: int64



```python
cols=[i for i in data.columns if i not in ["wijkcode","buurtnaam","buurtcode"]]
for col in cols:
    data[col]=pd.to_numeric(data[col])

print(data.dtypes)
```

    buurtcode                                                      object
    buurtnaam                                                      object
    wijkcode                                                       object
    bevolkingsdichtheid_inwoners_per_km2                          float64
    percentage_personen_0_tot_15_jaar                             float64
    percentage_personen_15_tot_25_jaar                            float64
    percentage_personen_25_tot_45_jaar                            float64
    percentage_personen_45_tot_65_jaar                            float64
    percentage_personen_65_jaar_en_ouder                          float64
    percentage_ongehuwd                                           float64
    percentage_gehuwd                                             float64
    percentage_gescheid                                           float64
    percentage_verweduwd                                          float64
    aantal_huishoudens                                            float64
    percentage_eenpersoonshuishoudens                             float64
    percentage_huishoudens_zonder_kinderen                        float64
    percentage_huishoudens_met_kinderen                           float64
    gemiddelde_huishoudsgrootte                                   float64
    percentage_westerse_migratieachtergrond                       float64
    percentage_niet_westerse_migratieachtergrond                  float64
    percentage_uit_marokko                                        float64
    percentage_uit_nederlandse_antillen_en_aruba                  float64
    percentage_uit_suriname                                       float64
    percentage_uit_turkije                                        float64
    percentage_overige_nietwestersemigratieachtergrond            float64
    aantal_bedrijven_landbouw_bosbouw_visserij                    float64
    gemiddelde_woningwaarde                                       float64
    percentage_eengezinswoning                                    float64
    percentage_meergezinswoning                                   float64
    percentage_koopwoningen                                       float64
    percentage_huurwoningen                                       float64
    perc_huurwoningen_in_bezit_woningcorporaties                  float64
    perc_huurwoningen_in_bezit_overige_verhuurders                float64
    percentage_werknemers                                         float64
    percentage_zelfstandigen                                      float64
    gemiddeld_inkomen_per_inwoner                                 float64
    percentage_huishoudens_met_laag_inkomen                       float64
    percentage_huishoudens_met_hoog_inkomen                       float64
    aantal_personen_met_een_algemene_bijstandsuitkering_totaal    float64
    gewelds_en_seksuele_misdrijven                                float64
    personenautos_totaal                                          float64
    personenautos_per_huishouden                                  float64
    motortweewielers_totaal                                       float64
    bevolking_totaal                                              float64
    percentage_minimahuishoudens                                  float64
    aantal_minimahuishoudens                                        int64
    gemiddeld_huishoudensinkomen                                  float64
    aangiften_diefstal_af_uit_woning                              float64
    aangiften_diefstal_auto                                       float64
    aangiften_diefstal_fiets                                      float64
    aangiften_diefstal_af_uit_bedrijf                             float64
    aangiften_diefstal_af_uit_auto                                float64
    aangiften_van_vernieling                                      float64
    aangiften_van_mishandeling                                    float64
    dogs_amount                                                     int64
    dtype: object



```python
print(data.columns)
data.head()
```

    Index(['buurtcode', 'buurtnaam', 'wijkcode',
           'bevolkingsdichtheid_inwoners_per_km2',
           'percentage_personen_0_tot_15_jaar',
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
           'aantal_bedrijven_landbouw_bosbouw_visserij', 'gemiddelde_woningwaarde',
           'percentage_eengezinswoning', 'percentage_meergezinswoning',
           'percentage_koopwoningen', 'percentage_huurwoningen',
           'perc_huurwoningen_in_bezit_woningcorporaties',
           'perc_huurwoningen_in_bezit_overige_verhuurders',
           'percentage_werknemers', 'percentage_zelfstandigen',
           'gemiddeld_inkomen_per_inwoner',
           'percentage_huishoudens_met_laag_inkomen',
           'percentage_huishoudens_met_hoog_inkomen',
           'aantal_personen_met_een_algemene_bijstandsuitkering_totaal',
           'gewelds_en_seksuele_misdrijven', 'personenautos_totaal',
           'personenautos_per_huishouden', 'motortweewielers_totaal',
           'bevolking_totaal', 'percentage_minimahuishoudens',
           'aantal_minimahuishoudens', 'gemiddeld_huishoudensinkomen',
           'aangiften_diefstal_af_uit_woning', 'aangiften_diefstal_auto',
           'aangiften_diefstal_fiets', 'aangiften_diefstal_af_uit_bedrijf',
           'aangiften_diefstal_af_uit_auto', 'aangiften_van_vernieling',
           'aangiften_van_mishandeling', 'dogs_amount'],
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
      <th>percentage_personen_0_tot_15_jaar</th>
      <th>percentage_personen_15_tot_25_jaar</th>
      <th>percentage_personen_25_tot_45_jaar</th>
      <th>percentage_personen_45_tot_65_jaar</th>
      <th>percentage_personen_65_jaar_en_ouder</th>
      <th>percentage_ongehuwd</th>
      <th>...</th>
      <th>aantal_minimahuishoudens</th>
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
      <td>11891.0</td>
      <td>2.0</td>
      <td>46.0</td>
      <td>35.0</td>
      <td>11.0</td>
      <td>6.0</td>
      <td>86.0</td>
      <td>...</td>
      <td>290</td>
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
      <td>BU000001</td>
      <td>Binnenstad-Zuid</td>
      <td>WK001400</td>
      <td>12011.0</td>
      <td>2.0</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>11.0</td>
      <td>6.0</td>
      <td>87.0</td>
      <td>...</td>
      <td>530</td>
      <td>30.7</td>
      <td>42.0</td>
      <td>3.0</td>
      <td>306.0</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>96.0</td>
      <td>196.0</td>
      <td>51</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BU000002</td>
      <td>Binnenstad-Oost</td>
      <td>WK001400</td>
      <td>14979.0</td>
      <td>3.0</td>
      <td>40.0</td>
      <td>36.0</td>
      <td>14.0</td>
      <td>7.0</td>
      <td>84.0</td>
      <td>...</td>
      <td>350</td>
      <td>27.8</td>
      <td>17.0</td>
      <td>1.0</td>
      <td>56.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>8.0</td>
      <td>48</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BU000003</td>
      <td>Binnenstad-West</td>
      <td>WK001400</td>
      <td>17507.0</td>
      <td>2.0</td>
      <td>38.0</td>
      <td>40.0</td>
      <td>11.0</td>
      <td>9.0</td>
      <td>84.0</td>
      <td>...</td>
      <td>140</td>
      <td>29.7</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>2.0</td>
      <td>4.0</td>
      <td>8.0</td>
      <td>5.0</td>
      <td>21</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BU000005</td>
      <td>Hortusbuurt-Ebbingekwartier</td>
      <td>WK001400</td>
      <td>12509.0</td>
      <td>5.0</td>
      <td>41.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>9.0</td>
      <td>82.0</td>
      <td>...</td>
      <td>450</td>
      <td>32.1</td>
      <td>20.0</td>
      <td>2.0</td>
      <td>68.0</td>
      <td>16.0</td>
      <td>9.0</td>
      <td>24.0</td>
      <td>13.0</td>
      <td>108</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 55 columns</p>
</div>




```python
data['politie_report_aantal'] = data['aangiften_diefstal_af_uit_woning'] + data['aangiften_diefstal_auto'] + data['aangiften_diefstal_af_uit_bedrijf'] + data['aangiften_diefstal_fiets'] + data['aangiften_diefstal_af_uit_auto'] + data['aangiften_van_mishandeling'] + data['aangiften_van_vernieling']
```

I can see that there are a lot of columns with very long names. Let's make it shorter


```python
data = data.rename(columns={'bevolkingsdichtheid_inwoners_per_km2': 'inwoners_per_km2'})
data = data.rename(columns={'percentage_personen_0_tot_15_jaar': 'perc_0_15_jaar'})
data = data.rename(columns={'percentage_personen_15_tot_25_jaar': 'perc_15_25_jaar'})
data = data.rename(columns={'percentage_personen_25_tot_45_jaar': 'perc_25_45_jaar'})
data = data.rename(columns={'percentage_personen_45_tot_65_jaar': 'perc_45_65_jaar'})
data = data.rename(columns={'percentage_personen_65_jaar_en_ouder': 'perc_65_en_ouder'})
data = data.rename(columns={'percentage_ongehuwd': 'perc_ongehuwd'})
data = data.rename(columns={'percentage_gehuwd': 'perc_gehuwd'})
data = data.rename(columns={'percentage_gescheid': 'perc_gescheid'})
data = data.rename(columns={'percentage_verweduwd': 'perc_verweduwd'})
data = data.rename(columns={'percentage_eenpersoonshuishoudens': 'perc_eenpersoonshuishoudens'})
data = data.rename(columns={'percentage_huishoudens_zonder_kinderen': 'perc_huishoudens_z_kind'})
data = data.rename(columns={'percentage_huishoudens_met_kinderen': 'perc_huishoudens_met_kind'})
data = data.rename(columns={'percentage_westerse_migratieachtergrond': 'perc_west_migratie'})
data = data.rename(columns={'percentage_niet_westerse_migratieachtergrond': 'perc_niet_west_migratie'})
data = data.rename(columns={'percentage_uit_nederlandse_antillen_en_aruba': 'perc_uit_antillen_en_aruba'})
data = data.rename(columns={'percentage_overige_nietwestersemigratieachtergrond': 'perc_overige_nietwestersemigratie'})
data = data.rename(columns={'aantal_bedrijven_landbouw_bosbouw_visserij': 'bedrijven_landbouw'})
data = data.rename(columns={'gemiddelde_woningwaarde': 'gemid_won_waarde'})
data = data.rename(columns={'percentage_eengezinswoning': 'perc_eengezinswoning'})
data = data.rename(columns={'percentage_meergezinswoning': 'perc_meergezinswoning'})
data = data.rename(columns={'percentage_koopwoningen': 'perc_koopwoningen'})
data = data.rename(columns={'percentage_huurwoningen': 'perc_huurwoningen'})
data = data.rename(columns={'perc_huurwoningen_in_bezit_woningcorporaties': 'perc_huurwoning_van_corporaties'})
data = data.rename(columns={'perc_huurwoningen_in_bezit_overige_verhuurders': 'perc_huurwoning_van_verhuurder'})
data = data.rename(columns={'percentage_werknemers': 'perc_werknemers'})
data = data.rename(columns={'percentage_zelfstandigen': 'perc_zelfstandigen'})
data = data.rename(columns={'percentage_huishoudens_met_laag_inkomen': 'perc_huishoud_laag_inkomen'})
data = data.rename(columns={'percentage_huishoudens_met_hoog_inkomen': 'perc_huishoud_hoog_inkomen'})
data = data.rename(columns={'aantal_personen_met_een_algemene_bijstandsuitkering_totaal': 'personen_met_bijstandsuitkering'})
data = data.rename(columns={'gewelds_en_seksuele_misdrijven': 'gewelds_misdrijven'})
data = data.rename(columns={'personenautos_totaal': 'autos_totaal'})
data = data.rename(columns={'personenautos_per_huishouden': 'autos_per_huishouden'})
data = data.rename(columns={'motortweewielers_totaal': 'motortweewielers'})
data = data.rename(columns={'aangiften_diefstal_af_uit_woning': 'diefstal_af_uit_woning'})
data = data.rename(columns={'aangiften_diefstal_auto': 'diefstal_auto'})
data = data.rename(columns={'aangiften_diefstal_fiets': 'diefstal_fiets'})
data = data.rename(columns={'aangiften_diefstal_af_uit_bedrijf': 'diefstal_af_uit_bedrijf'})
data = data.rename(columns={'aangiften_diefstal_af_uit_auto': 'diefstal_af_uit_auto'})
```


```python
data.to_csv(r'population_clean_data.csv', index = False)
```

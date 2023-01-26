## The map of the distribution of the dogs owners in Groningen


On this page I would like to show you how map of dog's owner distribution was drawn.

First I downloaded the database with geometry coordinates of Groningen neighborhoods (to execute this code you also need to download [Wijk- en buurtkaart 2022 dataset](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2022) because this database is too big to add it to the github repo where I store my code).

Now let's read the geo database, convert Rijksdriehoeksmeting (RD) GIS coordinates format to Lat/Long and also prepare dogs owner dataset to make it more convenient to use:


```python
import pandas as pd
import geopandas as gpd
import folium
import matplotlib.pyplot as plt

geo_data = gpd.GeoDataFrame(gpd.read_file("WijkBuurtkaart_Groningen_2022/wijkenbuurten_2022_v1.gpkg"))

geo_data.set_crs(epsg='28992')
geo_data = geo_data.to_crs(epsg='4326')


dogs_data=pd.read_csv('honden_data.csv', sep = ";")

dogs_data['buurtcode'] = dogs_data.apply(lambda row: "BU00" + str(row['buurtnr']), axis=1)

dogs_data['2016'] = dogs_data['2016'].replace('.',0)
convert_dict = {'2016': int}
dogs_data = dogs_data.astype(convert_dict)
```

And now let's take a look at the map and see where most of the dogs live and who their owners are.


```python
m = folium.Map(location=[53.22222, 6.56757], zoom_start=12, tiles='CartoDB positron')

year = '2018'
from branca.colormap import linear
colormap = linear.YlOrRd_09.scale(min(dogs_data[year]),
                                            max(dogs_data[year]))
colormap.caption = 'Amount of dogs owners in Groningen in ' + year
colormap.add_to(m)


def style_fn(feature):
    #colormap = branca.colormap.LinearColormap(["white", "green"], vmin=0, vmax=100)
    most_common = feature["properties"]["dogs_registered"]
    ss = {
        "fillColor": colormap(most_common),
        "fillOpacity": 0.9,
        "weight": 0.9,
        "opacity": 1,
        "color": "black",
    }
    return ss

def get_dogs_amount(row):
    dogs_row = dogs_data[dogs_data['buurtcode'] == row['buurtcode']]
    dogs = 0 if dogs_row.empty else dogs_row[year].iloc[0]
    return dogs                                           

def get_buurt_naam(row):
    return row["buurtnaam"]
    
geo_data['dogs_registered'] = geo_data.apply(lambda row: get_dogs_amount(row), axis=1)
geo_data['owners_age'] = geo_data[['percentage_personen_0_tot_15_jaar',
                           'percentage_personen_15_tot_25_jaar',
                           'percentage_personen_25_tot_45_jaar',
                           'percentage_personen_45_tot_65_jaar',
                           'percentage_personen_65_jaar_en_ouder']].idxmax(axis=1)
geo_data['owners_age'] = geo_data['owners_age'].astype(str).str.replace('percentage_personen_', '')
geo_data['owners_age'] = geo_data['owners_age'].astype(str).str.replace('_', ' ')

geo_data['status'] =  geo_data[['percentage_ongehuwd',
                                 'percentage_gehuwd',
                                 'percentage_gescheid',
                                 'percentage_verweduwd']].idxmax(axis=1)
geo_data['status'] = geo_data['status'].astype(str).str.replace('percentage_', '')

geo_data['buurt'] = geo_data.apply(lambda row: get_buurt_naam(row), axis=1)
geo_data['most_migrants_met'] =  geo_data[['percentage_westerse_migratieachtergrond',
                                 'percentage_niet_westerse_migratieachtergrond',
                                 'percentage_overige_nietwestersemigratieachtergrond']].idxmax(axis=1)
geo_data['most_migrants_met'] = geo_data['most_migrants_met'].astype(str).str.replace('percentage_', '')
geo_data['most_migrants_met'] = geo_data['most_migrants_met'].astype(str).str.replace('_', ' ')


folium.GeoJson(
    geo_data.__geo_interface__,
    style_function=style_fn,
    tooltip=folium.features.GeoJsonTooltip(['buurt', "dogs_registered", "owners_age", 'status', 'most_migrants_met']),
).add_to(m)

m
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;head&gt;    
    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.6.0/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-1.12.4.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.6.0/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://maxcdn.bootstrapcdn.com/font-awesome/4.6.3/css/font-awesome.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_ed356993e9a4eba0572681c6be763cd3 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min.js&quot;&gt;&lt;/script&gt;

                    &lt;style&gt;
                        .foliumtooltip {

                        }
                       .foliumtooltip table{
                            margin: auto;
                        }
                        .foliumtooltip tr{
                            text-align: left;
                        }
                        .foliumtooltip th{
                            padding: 2px; padding-right: 8px;
                        }
                    &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_ed356993e9a4eba0572681c6be763cd3&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_ed356993e9a4eba0572681c6be763cd3 = L.map(
                &quot;map_ed356993e9a4eba0572681c6be763cd3&quot;,
                {
                    center: [53.22222, 6.56757],
                    crs: L.CRS.EPSG3857,
                    zoom: 12,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_86c664c30b5fab7da853e6309fa42422 = L.tileLayer(
                &quot;https://cartodb-basemaps-{s}.global.ssl.fastly.net/light_all/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;\u0026copy; \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eOpenStreetMap\u003c/a\u003e contributors \u0026copy; \u003ca href=\&quot;http://cartodb.com/attributions\&quot;\u003eCartoDB\u003c/a\u003e, CartoDB \u003ca href =\&quot;http://cartodb.com/attributions\&quot;\u003eattributions\u003c/a\u003e&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_ed356993e9a4eba0572681c6be763cd3);


    var color_map_adc234f7c79364fbc8c5f3d6af5f1faa = {};


    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.color = d3.scale.threshold()
              .domain([0.0, 0.9138276553106213, 1.8276553106212425, 2.741482965931864, 3.655310621242485, 4.569138276553106, 5.482965931863728, 6.396793587174349, 7.31062124248497, 8.224448897795591, 9.138276553106213, 10.052104208416834, 10.965931863727455, 11.879759519038076, 12.793587174348698, 13.707414829659319, 14.62124248496994, 15.535070140280562, 16.448897795591183, 17.362725450901802, 18.276553106212425, 19.190380761523045, 20.104208416833668, 21.018036072144287, 21.93186372745491, 22.84569138276553, 23.759519038076153, 24.673346693386772, 25.587174348697395, 26.501002004008015, 27.414829659318638, 28.328657314629258, 29.24248496993988, 30.1563126252505, 31.070140280561123, 31.983967935871743, 32.897795591182366, 33.811623246492985, 34.725450901803605, 35.63927855711423, 36.55310621242485, 37.46693386773547, 38.38076152304609, 39.294589178356716, 40.208416833667336, 41.122244488977955, 42.036072144288575, 42.9498997995992, 43.86372745490982, 44.77755511022044, 45.69138276553106, 46.605210420841686, 47.519038076152306, 48.432865731462925, 49.346693386773545, 50.26052104208417, 51.17434869739479, 52.08817635270541, 53.00200400801603, 53.91583166332666, 54.829659318637276, 55.743486973947896, 56.657314629258515, 57.57114228456914, 58.48496993987976, 59.39879759519038, 60.312625250501, 61.22645290581163, 62.140280561122246, 63.054108216432866, 63.967935871743485, 64.8817635270541, 65.79559118236473, 66.70941883767534, 67.62324649298597, 68.5370741482966, 69.45090180360721, 70.36472945891784, 71.27855711422846, 72.19238476953907, 73.1062124248497, 74.02004008016031, 74.93386773547094, 75.84769539078157, 76.76152304609218, 77.6753507014028, 78.58917835671343, 79.50300601202404, 80.41683366733467, 81.33066132264528, 82.24448897795591, 83.15831663326654, 84.07214428857715, 84.98597194388778, 85.8997995991984, 86.81362725450902, 87.72745490981964, 88.64128256513025, 89.55511022044088, 90.46893787575151, 91.38276553106212, 92.29659318637275, 93.21042084168337, 94.12424849699399, 95.03807615230461, 95.95190380761522, 96.86573146292585, 97.77955911823648, 98.69338677354709, 99.60721442885772, 100.52104208416834, 101.43486973947896, 102.34869739478958, 103.2625250501002, 104.17635270541082, 105.09018036072145, 106.00400801603206, 106.91783567134269, 107.83166332665331, 108.74549098196393, 109.65931863727455, 110.57314629258516, 111.48697394789579, 112.40080160320642, 113.31462925851703, 114.22845691382766, 115.14228456913828, 116.0561122244489, 116.96993987975952, 117.88376753507013, 118.79759519038076, 119.71142284569139, 120.625250501002, 121.53907815631263, 122.45290581162325, 123.36673346693387, 124.28056112224449, 125.1943887775551, 126.10821643286573, 127.02204408817636, 127.93587174348697, 128.84969939879758, 129.7635270541082, 130.67735470941884, 131.59118236472946, 132.5050100200401, 133.4188376753507, 134.3326653306613, 135.24649298597194, 136.16032064128257, 137.0741482965932, 137.98797595190382, 138.90180360721442, 139.81563126252505, 140.72945891783567, 141.6432865731463, 142.55711422845692, 143.47094188376752, 144.38476953907815, 145.29859719438878, 146.2124248496994, 147.12625250501003, 148.04008016032063, 148.95390781563125, 149.86773547094188, 150.7815631262525, 151.69539078156313, 152.60921843687376, 153.52304609218436, 154.43687374749499, 155.3507014028056, 156.26452905811624, 157.17835671342687, 158.09218436873746, 159.0060120240481, 159.91983967935872, 160.83366733466934, 161.74749498997997, 162.66132264529057, 163.5751503006012, 164.48897795591182, 165.40280561122245, 166.31663326653307, 167.2304609218437, 168.1442885771543, 169.05811623246493, 169.97194388777555, 170.88577154308618, 171.7995991983968, 172.7134268537074, 173.62725450901803, 174.54108216432866, 175.45490981963928, 176.3687374749499, 177.2825651302605, 178.19639278557113, 179.11022044088176, 180.0240480961924, 180.93787575150301, 181.85170340681364, 182.76553106212424, 183.67935871743487, 184.5931863727455, 185.50701402805612, 186.42084168336675, 187.33466933867734, 188.24849699398797, 189.1623246492986, 190.07615230460922, 190.98997995991985, 191.90380761523045, 192.81763527054107, 193.7314629258517, 194.64529058116233, 195.55911823647295, 196.47294589178355, 197.38677354709418, 198.3006012024048, 199.21442885771543, 200.12825651302606, 201.0420841683367, 201.95591182364728, 202.8697394789579, 203.78356713426854, 204.69739478957916, 205.6112224448898, 206.5250501002004, 207.43887775551102, 208.35270541082164, 209.26653306613227, 210.1803607214429, 211.0941883767535, 212.00801603206412, 212.92184368737475, 213.83567134268537, 214.749498997996, 215.66332665330663, 216.57715430861722, 217.49098196392785, 218.40480961923848, 219.3186372745491, 220.23246492985973, 221.14629258517033, 222.06012024048096, 222.97394789579158, 223.8877755511022, 224.80160320641284, 225.71543086172343, 226.62925851703406, 227.5430861723447, 228.4569138276553, 229.37074148296594, 230.28456913827657, 231.19839679358716, 232.1122244488978, 233.02605210420842, 233.93987975951904, 234.85370741482967, 235.76753507014027, 236.6813627254509, 237.59519038076152, 238.50901803607215, 239.42284569138278, 240.33667334669337, 241.250501002004, 242.16432865731463, 243.07815631262525, 243.99198396793588, 244.9058116232465, 245.8196392785571, 246.73346693386773, 247.64729458917836, 248.56112224448898, 249.4749498997996, 250.3887775551102, 251.30260521042084, 252.21643286573146, 253.1302605210421, 254.04408817635272, 254.9579158316633, 255.87174348697394, 256.7855711422846, 257.69939879759517, 258.6132264529058, 259.5270541082164, 260.44088176352705, 261.3547094188377, 262.2685370741483, 263.1823647294589, 264.09619238476955, 265.0100200400802, 265.9238476953908, 266.8376753507014, 267.751503006012, 268.6653306613226, 269.57915831663325, 270.4929859719439, 271.4068136272545, 272.32064128256513, 273.23446893787576, 274.1482965931864, 275.062124248497, 275.97595190380764, 276.8897795591182, 277.80360721442884, 278.71743486973946, 279.6312625250501, 280.5450901803607, 281.45891783567134, 282.37274549098197, 283.2865731462926, 284.2004008016032, 285.11422845691385, 286.0280561122245, 286.94188376753505, 287.8557114228457, 288.7695390781563, 289.6833667334669, 290.59719438877755, 291.5110220440882, 292.4248496993988, 293.33867735470943, 294.25250501002006, 295.1663326653307, 296.08016032064126, 296.9939879759519, 297.9078156312625, 298.82164328657313, 299.73547094188376, 300.6492985971944, 301.563126252505, 302.47695390781564, 303.39078156312627, 304.3046092184369, 305.2184368737475, 306.1322645290581, 307.0460921843687, 307.95991983967934, 308.87374749498997, 309.7875751503006, 310.7014028056112, 311.61523046092185, 312.5290581162325, 313.4428857715431, 314.35671342685373, 315.27054108216436, 316.1843687374749, 317.09819639278555, 318.0120240480962, 318.9258517034068, 319.83967935871743, 320.75350701402806, 321.6673346693387, 322.5811623246493, 323.49498997995994, 324.40881763527057, 325.32264529058114, 326.23647294589176, 327.1503006012024, 328.064128256513, 328.97795591182364, 329.89178356713427, 330.8056112224449, 331.7194388777555, 332.63326653306615, 333.5470941883768, 334.4609218436874, 335.37474949899797, 336.2885771543086, 337.2024048096192, 338.11623246492985, 339.0300601202405, 339.9438877755511, 340.85771543086173, 341.77154308617236, 342.685370741483, 343.5991983967936, 344.5130260521042, 345.4268537074148, 346.34068136272543, 347.25450901803606, 348.1683366733467, 349.0821643286573, 349.99599198396794, 350.90981963927857, 351.8236472945892, 352.7374749498998, 353.65130260521045, 354.565130260521, 355.47895791583164, 356.39278557114227, 357.3066132264529, 358.2204408817635, 359.13426853707415, 360.0480961923848, 360.9619238476954, 361.87575150300603, 362.78957915831666, 363.7034068136273, 364.61723446893785, 365.5310621242485, 366.4448897795591, 367.35871743486973, 368.27254509018036, 369.186372745491, 370.1002004008016, 371.01402805611224, 371.92785571142286, 372.8416833667335, 373.75551102204406, 374.6693386773547, 375.5831663326653, 376.49699398797594, 377.41082164328657, 378.3246492985972, 379.2384769539078, 380.15230460921845, 381.0661322645291, 381.9799599198397, 382.8937875751503, 383.8076152304609, 384.7214428857715, 385.63527054108215, 386.5490981963928, 387.4629258517034, 388.37675350701403, 389.29058116232466, 390.2044088176353, 391.1182364729459, 392.03206412825654, 392.9458917835671, 393.85971943887773, 394.77354709418836, 395.687374749499, 396.6012024048096, 397.51503006012024, 398.42885771543087, 399.3426853707415, 400.2565130260521, 401.17034068136275, 402.0841683366734, 402.99799599198394, 403.91182364729457, 404.8256513026052, 405.7394789579158, 406.65330661322645, 407.5671342685371, 408.4809619238477, 409.3947895791583, 410.30861723446895, 411.2224448897796, 412.1362725450902, 413.0501002004008, 413.9639278557114, 414.87775551102203, 415.79158316633266, 416.7054108216433, 417.6192384769539, 418.53306613226454, 419.44689378757516, 420.3607214428858, 421.2745490981964, 422.188376753507, 423.1022044088176, 424.01603206412824, 424.92985971943887, 425.8436873747495, 426.7575150300601, 427.67134268537075, 428.5851703406814, 429.498997995992, 430.4128256513026, 431.32665330661325, 432.2404809619238, 433.15430861723445, 434.0681362725451, 434.9819639278557, 435.89579158316633, 436.80961923847696, 437.7234468937876, 438.6372745490982, 439.55110220440883, 440.46492985971946, 441.3787575150301, 442.29258517034066, 443.2064128256513, 444.1202404809619, 445.03406813627254, 445.94789579158316, 446.8617234468938, 447.7755511022044, 448.68937875751504, 449.60320641282567, 450.5170340681363, 451.43086172344687, 452.3446893787575, 453.2585170340681, 454.17234468937875, 455.0861723446894, 456.0])
              .range([&#x27;#ffffccff&#x27;, &#x27;#ffffccff&#x27;, &#x27;#ffffcbff&#x27;, &#x27;#ffffcaff&#x27;, &#x27;#fffec9ff&#x27;, &#x27;#fffec9ff&#x27;, &#x27;#fffec8ff&#x27;, &#x27;#fffdc7ff&#x27;, &#x27;#fffdc7ff&#x27;, &#x27;#fffdc6ff&#x27;, &#x27;#fffdc5ff&#x27;, &#x27;#fffcc5ff&#x27;, &#x27;#fffcc4ff&#x27;, &#x27;#fffcc3ff&#x27;, &#x27;#fffbc2ff&#x27;, &#x27;#fffbc2ff&#x27;, &#x27;#fffbc1ff&#x27;, &#x27;#fffbc0ff&#x27;, &#x27;#fffac0ff&#x27;, &#x27;#fffabfff&#x27;, &#x27;#fffabeff&#x27;, &#x27;#fff9bdff&#x27;, &#x27;#fff9bdff&#x27;, &#x27;#fff9bcff&#x27;, &#x27;#fff9bbff&#x27;, &#x27;#fff8bbff&#x27;, &#x27;#fff8baff&#x27;, &#x27;#fff8b9ff&#x27;, &#x27;#fff7b8ff&#x27;, &#x27;#fff7b8ff&#x27;, &#x27;#fff7b7ff&#x27;, &#x27;#fff7b6ff&#x27;, &#x27;#fff6b6ff&#x27;, &#x27;#fff6b5ff&#x27;, &#x27;#fff6b4ff&#x27;, &#x27;#fff5b4ff&#x27;, &#x27;#fff5b3ff&#x27;, &#x27;#fff5b2ff&#x27;, &#x27;#fff4b1ff&#x27;, &#x27;#fff4b1ff&#x27;, &#x27;#fff4b0ff&#x27;, &#x27;#fff4afff&#x27;, &#x27;#fff3afff&#x27;, &#x27;#fff3aeff&#x27;, &#x27;#fff3adff&#x27;, &#x27;#fff2acff&#x27;, &#x27;#fff2acff&#x27;, &#x27;#fff2abff&#x27;, &#x27;#fff2aaff&#x27;, &#x27;#fff1aaff&#x27;, &#x27;#fff1a9ff&#x27;, &#x27;#fff1a8ff&#x27;, &#x27;#fff0a7ff&#x27;, &#x27;#fff0a7ff&#x27;, &#x27;#fff0a6ff&#x27;, &#x27;#fff0a5ff&#x27;, &#x27;#ffefa5ff&#x27;, &#x27;#ffefa4ff&#x27;, &#x27;#ffefa3ff&#x27;, &#x27;#ffeea3ff&#x27;, &#x27;#ffeea2ff&#x27;, &#x27;#ffeea1ff&#x27;, &#x27;#ffeea0ff&#x27;, &#x27;#ffeda0ff&#x27;, &#x27;#ffed9fff&#x27;, &#x27;#ffed9eff&#x27;, &#x27;#ffec9eff&#x27;, &#x27;#ffec9dff&#x27;, &#x27;#ffec9cff&#x27;, &#x27;#ffeb9cff&#x27;, &#x27;#ffeb9bff&#x27;, &#x27;#ffeb9aff&#x27;, &#x27;#ffea9aff&#x27;, &#x27;#ffea99ff&#x27;, &#x27;#ffea98ff&#x27;, &#x27;#ffe998ff&#x27;, &#x27;#ffe997ff&#x27;, &#x27;#ffe996ff&#x27;, &#x27;#ffe896ff&#x27;, &#x27;#ffe895ff&#x27;, &#x27;#ffe894ff&#x27;, &#x27;#ffe794ff&#x27;, &#x27;#ffe793ff&#x27;, &#x27;#ffe792ff&#x27;, &#x27;#ffe692ff&#x27;, &#x27;#ffe691ff&#x27;, &#x27;#ffe690ff&#x27;, &#x27;#ffe68fff&#x27;, &#x27;#ffe58fff&#x27;, &#x27;#ffe58eff&#x27;, &#x27;#ffe58dff&#x27;, &#x27;#ffe48dff&#x27;, &#x27;#ffe48cff&#x27;, &#x27;#ffe48bff&#x27;, &#x27;#ffe38bff&#x27;, &#x27;#ffe38aff&#x27;, &#x27;#ffe389ff&#x27;, &#x27;#ffe289ff&#x27;, &#x27;#ffe288ff&#x27;, &#x27;#ffe287ff&#x27;, &#x27;#ffe187ff&#x27;, &#x27;#ffe186ff&#x27;, &#x27;#ffe185ff&#x27;, &#x27;#ffe085ff&#x27;, &#x27;#ffe084ff&#x27;, &#x27;#ffe083ff&#x27;, &#x27;#ffdf83ff&#x27;, &#x27;#ffdf82ff&#x27;, &#x27;#ffdf81ff&#x27;, &#x27;#ffde81ff&#x27;, &#x27;#ffde80ff&#x27;, &#x27;#ffde7fff&#x27;, &#x27;#ffdd7fff&#x27;, &#x27;#ffdd7eff&#x27;, &#x27;#ffdd7dff&#x27;, &#x27;#ffdc7dff&#x27;, &#x27;#ffdc7cff&#x27;, &#x27;#ffdc7bff&#x27;, &#x27;#ffdc7bff&#x27;, &#x27;#ffdb7aff&#x27;, &#x27;#ffdb79ff&#x27;, &#x27;#ffdb78ff&#x27;, &#x27;#ffda78ff&#x27;, &#x27;#ffda77ff&#x27;, &#x27;#ffda76ff&#x27;, &#x27;#fed976ff&#x27;, &#x27;#fed975ff&#x27;, &#x27;#fed874ff&#x27;, &#x27;#fed774ff&#x27;, &#x27;#fed773ff&#x27;, &#x27;#fed672ff&#x27;, &#x27;#fed572ff&#x27;, &#x27;#fed571ff&#x27;, &#x27;#fed470ff&#x27;, &#x27;#fed470ff&#x27;, &#x27;#fed36fff&#x27;, &#x27;#fed26eff&#x27;, &#x27;#fed26eff&#x27;, &#x27;#fed16dff&#x27;, &#x27;#fed06cff&#x27;, &#x27;#fed06cff&#x27;, &#x27;#fecf6bff&#x27;, &#x27;#fecf6aff&#x27;, &#x27;#fece6aff&#x27;, &#x27;#fecd69ff&#x27;, &#x27;#fecd68ff&#x27;, &#x27;#fecc68ff&#x27;, &#x27;#fecb67ff&#x27;, &#x27;#fecb66ff&#x27;, &#x27;#feca66ff&#x27;, &#x27;#feca65ff&#x27;, &#x27;#fec964ff&#x27;, &#x27;#fec864ff&#x27;, &#x27;#fec863ff&#x27;, &#x27;#fec762ff&#x27;, &#x27;#fec662ff&#x27;, &#x27;#fec661ff&#x27;, &#x27;#fec560ff&#x27;, &#x27;#fec45fff&#x27;, &#x27;#fec45fff&#x27;, &#x27;#fec35eff&#x27;, &#x27;#fec35dff&#x27;, &#x27;#fec25dff&#x27;, &#x27;#fec15cff&#x27;, &#x27;#fec15bff&#x27;, &#x27;#fec05bff&#x27;, &#x27;#febf5aff&#x27;, &#x27;#febf59ff&#x27;, &#x27;#febe59ff&#x27;, &#x27;#febe58ff&#x27;, &#x27;#febd57ff&#x27;, &#x27;#febc57ff&#x27;, &#x27;#febc56ff&#x27;, &#x27;#febb55ff&#x27;, &#x27;#feba55ff&#x27;, &#x27;#feba54ff&#x27;, &#x27;#feb953ff&#x27;, &#x27;#feb953ff&#x27;, &#x27;#feb852ff&#x27;, &#x27;#feb751ff&#x27;, &#x27;#feb751ff&#x27;, &#x27;#feb650ff&#x27;, &#x27;#feb54fff&#x27;, &#x27;#feb54fff&#x27;, &#x27;#feb44eff&#x27;, &#x27;#feb44dff&#x27;, &#x27;#feb34dff&#x27;, &#x27;#feb24cff&#x27;, &#x27;#feb24cff&#x27;, &#x27;#feb14bff&#x27;, &#x27;#feb04bff&#x27;, &#x27;#feb04bff&#x27;, &#x27;#feaf4bff&#x27;, &#x27;#feaf4aff&#x27;, &#x27;#feae4aff&#x27;, &#x27;#feae4aff&#x27;, &#x27;#fead4aff&#x27;, &#x27;#feac49ff&#x27;, &#x27;#feac49ff&#x27;, &#x27;#feab49ff&#x27;, &#x27;#feab48ff&#x27;, &#x27;#feaa48ff&#x27;, &#x27;#fea948ff&#x27;, &#x27;#fea948ff&#x27;, &#x27;#fea847ff&#x27;, &#x27;#fea847ff&#x27;, &#x27;#fea747ff&#x27;, &#x27;#fea647ff&#x27;, &#x27;#fea646ff&#x27;, &#x27;#fea546ff&#x27;, &#x27;#fea546ff&#x27;, &#x27;#fea446ff&#x27;, &#x27;#fea345ff&#x27;, &#x27;#fea345ff&#x27;, &#x27;#fea245ff&#x27;, &#x27;#fea245ff&#x27;, &#x27;#fea144ff&#x27;, &#x27;#fea044ff&#x27;, &#x27;#fea044ff&#x27;, &#x27;#fe9f44ff&#x27;, &#x27;#fe9f43ff&#x27;, &#x27;#fe9e43ff&#x27;, &#x27;#fe9d43ff&#x27;, &#x27;#fe9d43ff&#x27;, &#x27;#fe9c42ff&#x27;, &#x27;#fe9c42ff&#x27;, &#x27;#fe9b42ff&#x27;, &#x27;#fe9a42ff&#x27;, &#x27;#fe9a41ff&#x27;, &#x27;#fe9941ff&#x27;, &#x27;#fe9941ff&#x27;, &#x27;#fe9840ff&#x27;, &#x27;#fe9740ff&#x27;, &#x27;#fe9740ff&#x27;, &#x27;#fe9640ff&#x27;, &#x27;#fe963fff&#x27;, &#x27;#fe953fff&#x27;, &#x27;#fe943fff&#x27;, &#x27;#fe943fff&#x27;, &#x27;#fe933eff&#x27;, &#x27;#fe933eff&#x27;, &#x27;#fe923eff&#x27;, &#x27;#fe923eff&#x27;, &#x27;#fe913dff&#x27;, &#x27;#fe903dff&#x27;, &#x27;#fe903dff&#x27;, &#x27;#fe8f3dff&#x27;, &#x27;#fe8f3cff&#x27;, &#x27;#fe8e3cff&#x27;, &#x27;#fe8d3cff&#x27;, &#x27;#fd8d3cff&#x27;, &#x27;#fd8c3bff&#x27;, &#x27;#fd8b3bff&#x27;, &#x27;#fd8a3bff&#x27;, &#x27;#fd883aff&#x27;, &#x27;#fd873aff&#x27;, &#x27;#fd863aff&#x27;, &#x27;#fd853aff&#x27;, &#x27;#fd8439ff&#x27;, &#x27;#fd8339ff&#x27;, &#x27;#fd8239ff&#x27;, &#x27;#fd8138ff&#x27;, &#x27;#fd8038ff&#x27;, &#x27;#fd7f38ff&#x27;, &#x27;#fd7e38ff&#x27;, &#x27;#fd7d37ff&#x27;, &#x27;#fd7c37ff&#x27;, &#x27;#fd7b37ff&#x27;, &#x27;#fd7a36ff&#x27;, &#x27;#fd7936ff&#x27;, &#x27;#fd7836ff&#x27;, &#x27;#fd7736ff&#x27;, &#x27;#fd7635ff&#x27;, &#x27;#fd7535ff&#x27;, &#x27;#fd7435ff&#x27;, &#x27;#fd7334ff&#x27;, &#x27;#fd7234ff&#x27;, &#x27;#fd7134ff&#x27;, &#x27;#fd7033ff&#x27;, &#x27;#fd6f33ff&#x27;, &#x27;#fd6e33ff&#x27;, &#x27;#fd6d33ff&#x27;, &#x27;#fd6c32ff&#x27;, &#x27;#fd6b32ff&#x27;, &#x27;#fd6a32ff&#x27;, &#x27;#fd6931ff&#x27;, &#x27;#fd6831ff&#x27;, &#x27;#fd6731ff&#x27;, &#x27;#fd6631ff&#x27;, &#x27;#fd6530ff&#x27;, &#x27;#fd6430ff&#x27;, &#x27;#fd6330ff&#x27;, &#x27;#fd622fff&#x27;, &#x27;#fd612fff&#x27;, &#x27;#fd602fff&#x27;, &#x27;#fd5f2fff&#x27;, &#x27;#fd5e2eff&#x27;, &#x27;#fd5d2eff&#x27;, &#x27;#fd5c2eff&#x27;, &#x27;#fd5b2dff&#x27;, &#x27;#fd5a2dff&#x27;, &#x27;#fd592dff&#x27;, &#x27;#fd582dff&#x27;, &#x27;#fd572cff&#x27;, &#x27;#fd562cff&#x27;, &#x27;#fd552cff&#x27;, &#x27;#fd542bff&#x27;, &#x27;#fd532bff&#x27;, &#x27;#fd522bff&#x27;, &#x27;#fd512aff&#x27;, &#x27;#fd502aff&#x27;, &#x27;#fd4f2aff&#x27;, &#x27;#fc4e2aff&#x27;, &#x27;#fc4d29ff&#x27;, &#x27;#fc4c29ff&#x27;, &#x27;#fb4b29ff&#x27;, &#x27;#fb4a29ff&#x27;, &#x27;#fa4a29ff&#x27;, &#x27;#fa4928ff&#x27;, &#x27;#fa4828ff&#x27;, &#x27;#f94728ff&#x27;, &#x27;#f94628ff&#x27;, &#x27;#f84527ff&#x27;, &#x27;#f84427ff&#x27;, &#x27;#f84427ff&#x27;, &#x27;#f74327ff&#x27;, &#x27;#f74226ff&#x27;, &#x27;#f64126ff&#x27;, &#x27;#f64026ff&#x27;, &#x27;#f63f26ff&#x27;, &#x27;#f53f26ff&#x27;, &#x27;#f53e25ff&#x27;, &#x27;#f43d25ff&#x27;, &#x27;#f43c25ff&#x27;, &#x27;#f43b25ff&#x27;, &#x27;#f33a24ff&#x27;, &#x27;#f33a24ff&#x27;, &#x27;#f23924ff&#x27;, &#x27;#f23824ff&#x27;, &#x27;#f23724ff&#x27;, &#x27;#f13623ff&#x27;, &#x27;#f13523ff&#x27;, &#x27;#f03523ff&#x27;, &#x27;#f03423ff&#x27;, &#x27;#f03322ff&#x27;, &#x27;#ef3222ff&#x27;, &#x27;#ef3122ff&#x27;, &#x27;#ee3022ff&#x27;, &#x27;#ee3022ff&#x27;, &#x27;#ee2f21ff&#x27;, &#x27;#ed2e21ff&#x27;, &#x27;#ed2d21ff&#x27;, &#x27;#ec2c21ff&#x27;, &#x27;#ec2b20ff&#x27;, &#x27;#ec2b20ff&#x27;, &#x27;#eb2a20ff&#x27;, &#x27;#eb2920ff&#x27;, &#x27;#ea281fff&#x27;, &#x27;#ea271fff&#x27;, &#x27;#ea261fff&#x27;, &#x27;#e9261fff&#x27;, &#x27;#e9251fff&#x27;, &#x27;#e8241eff&#x27;, &#x27;#e8231eff&#x27;, &#x27;#e8221eff&#x27;, &#x27;#e7211eff&#x27;, &#x27;#e7211dff&#x27;, &#x27;#e6201dff&#x27;, &#x27;#e61f1dff&#x27;, &#x27;#e61e1dff&#x27;, &#x27;#e51d1dff&#x27;, &#x27;#e51c1cff&#x27;, &#x27;#e41b1cff&#x27;, &#x27;#e41b1cff&#x27;, &#x27;#e31a1cff&#x27;, &#x27;#e3191cff&#x27;, &#x27;#e2191cff&#x27;, &#x27;#e2181cff&#x27;, &#x27;#e1181cff&#x27;, &#x27;#e0181cff&#x27;, &#x27;#e0171dff&#x27;, &#x27;#df171dff&#x27;, &#x27;#df161dff&#x27;, &#x27;#de161dff&#x27;, &#x27;#dd161dff&#x27;, &#x27;#dd151dff&#x27;, &#x27;#dc151eff&#x27;, &#x27;#dc141eff&#x27;, &#x27;#db141eff&#x27;, &#x27;#da131eff&#x27;, &#x27;#da131eff&#x27;, &#x27;#d9131eff&#x27;, &#x27;#d9121eff&#x27;, &#x27;#d8121fff&#x27;, &#x27;#d7111fff&#x27;, &#x27;#d7111fff&#x27;, &#x27;#d6111fff&#x27;, &#x27;#d5101fff&#x27;, &#x27;#d5101fff&#x27;, &#x27;#d40f20ff&#x27;, &#x27;#d40f20ff&#x27;, &#x27;#d30e20ff&#x27;, &#x27;#d20e20ff&#x27;, &#x27;#d20e20ff&#x27;, &#x27;#d10d20ff&#x27;, &#x27;#d10d21ff&#x27;, &#x27;#d00c21ff&#x27;, &#x27;#cf0c21ff&#x27;, &#x27;#cf0b21ff&#x27;, &#x27;#ce0b21ff&#x27;, &#x27;#ce0b21ff&#x27;, &#x27;#cd0a22ff&#x27;, &#x27;#cc0a22ff&#x27;, &#x27;#cc0922ff&#x27;, &#x27;#cb0922ff&#x27;, &#x27;#ca0922ff&#x27;, &#x27;#ca0822ff&#x27;, &#x27;#c90822ff&#x27;, &#x27;#c90723ff&#x27;, &#x27;#c80723ff&#x27;, &#x27;#c70623ff&#x27;, &#x27;#c70623ff&#x27;, &#x27;#c60623ff&#x27;, &#x27;#c60523ff&#x27;, &#x27;#c50524ff&#x27;, &#x27;#c40424ff&#x27;, &#x27;#c40424ff&#x27;, &#x27;#c30424ff&#x27;, &#x27;#c30324ff&#x27;, &#x27;#c20324ff&#x27;, &#x27;#c10225ff&#x27;, &#x27;#c10225ff&#x27;, &#x27;#c00125ff&#x27;, &#x27;#bf0125ff&#x27;, &#x27;#bf0125ff&#x27;, &#x27;#be0025ff&#x27;, &#x27;#be0026ff&#x27;, &#x27;#bd0026ff&#x27;, &#x27;#bc0026ff&#x27;, &#x27;#bb0026ff&#x27;, &#x27;#ba0026ff&#x27;, &#x27;#b90026ff&#x27;, &#x27;#b80026ff&#x27;, &#x27;#b70026ff&#x27;, &#x27;#b60026ff&#x27;, &#x27;#b50026ff&#x27;, &#x27;#b40026ff&#x27;, &#x27;#b30026ff&#x27;, &#x27;#b20026ff&#x27;, &#x27;#b10026ff&#x27;, &#x27;#b00026ff&#x27;, &#x27;#af0026ff&#x27;, &#x27;#ae0026ff&#x27;, &#x27;#ad0026ff&#x27;, &#x27;#ac0026ff&#x27;, &#x27;#ab0026ff&#x27;, &#x27;#aa0026ff&#x27;, &#x27;#a90026ff&#x27;, &#x27;#a80026ff&#x27;, &#x27;#a70026ff&#x27;, &#x27;#a60026ff&#x27;, &#x27;#a50026ff&#x27;, &#x27;#a40026ff&#x27;, &#x27;#a30026ff&#x27;, &#x27;#a20026ff&#x27;, &#x27;#a10026ff&#x27;, &#x27;#a00026ff&#x27;, &#x27;#9f0026ff&#x27;, &#x27;#9e0026ff&#x27;, &#x27;#9d0026ff&#x27;, &#x27;#9c0026ff&#x27;, &#x27;#9b0026ff&#x27;, &#x27;#9b0026ff&#x27;, &#x27;#9a0026ff&#x27;, &#x27;#990026ff&#x27;, &#x27;#980026ff&#x27;, &#x27;#970026ff&#x27;, &#x27;#960026ff&#x27;, &#x27;#950026ff&#x27;, &#x27;#940026ff&#x27;, &#x27;#930026ff&#x27;, &#x27;#920026ff&#x27;, &#x27;#910026ff&#x27;, &#x27;#900026ff&#x27;, &#x27;#8f0026ff&#x27;, &#x27;#8e0026ff&#x27;, &#x27;#8d0026ff&#x27;, &#x27;#8c0026ff&#x27;, &#x27;#8b0026ff&#x27;, &#x27;#8a0026ff&#x27;, &#x27;#890026ff&#x27;, &#x27;#880026ff&#x27;, &#x27;#870026ff&#x27;, &#x27;#860026ff&#x27;, &#x27;#850026ff&#x27;, &#x27;#840026ff&#x27;, &#x27;#830026ff&#x27;, &#x27;#820026ff&#x27;, &#x27;#810026ff&#x27;, &#x27;#800026ff&#x27;]);


    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.x = d3.scale.linear()
              .domain([0.0, 456.0])
              .range([0, 400]);

    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.legend = L.control({position: &#x27;topright&#x27;});
    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.legend.onAdd = function (map) {var div = L.DomUtil.create(&#x27;div&#x27;, &#x27;legend&#x27;); return div};
    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.legend.addTo(map_ed356993e9a4eba0572681c6be763cd3);

    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.xAxis = d3.svg.axis()
        .scale(color_map_adc234f7c79364fbc8c5f3d6af5f1faa.x)
        .orient(&quot;top&quot;)
        .tickSize(1)
        .tickValues([0.0, 57.0, 114.0, 171.0, 228.0, 285.0, 342.0, 399.0, 456.0]);

    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.svg = d3.select(&quot;.legend.leaflet-control&quot;).append(&quot;svg&quot;)
        .attr(&quot;id&quot;, &#x27;legend&#x27;)
        .attr(&quot;width&quot;, 450)
        .attr(&quot;height&quot;, 40);

    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.g = color_map_adc234f7c79364fbc8c5f3d6af5f1faa.svg.append(&quot;g&quot;)
        .attr(&quot;class&quot;, &quot;key&quot;)
        .attr(&quot;transform&quot;, &quot;translate(25,16)&quot;);

    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.g.selectAll(&quot;rect&quot;)
        .data(color_map_adc234f7c79364fbc8c5f3d6af5f1faa.color.range().map(function(d, i) {
          return {
            x0: i ? color_map_adc234f7c79364fbc8c5f3d6af5f1faa.x(color_map_adc234f7c79364fbc8c5f3d6af5f1faa.color.domain()[i - 1]) : color_map_adc234f7c79364fbc8c5f3d6af5f1faa.x.range()[0],
            x1: i &lt; color_map_adc234f7c79364fbc8c5f3d6af5f1faa.color.domain().length ? color_map_adc234f7c79364fbc8c5f3d6af5f1faa.x(color_map_adc234f7c79364fbc8c5f3d6af5f1faa.color.domain()[i]) : color_map_adc234f7c79364fbc8c5f3d6af5f1faa.x.range()[1],
            z: d
          };
        }))
      .enter().append(&quot;rect&quot;)
        .attr(&quot;height&quot;, 10)
        .attr(&quot;x&quot;, function(d) { return d.x0; })
        .attr(&quot;width&quot;, function(d) { return d.x1 - d.x0; })
        .style(&quot;fill&quot;, function(d) { return d.z; });

    color_map_adc234f7c79364fbc8c5f3d6af5f1faa.g.call(color_map_adc234f7c79364fbc8c5f3d6af5f1faa.xAxis).append(&quot;text&quot;)
        .attr(&quot;class&quot;, &quot;caption&quot;)
        .attr(&quot;y&quot;, 21)
        .text(&#x27;Amount of dogs owners in Groningen in 2018&#x27;);

        function geo_json_1bd2bffcea5e5eea4e76105154b7b526_styler(feature) {
            switch(feature.id) {
                case &quot;0&quot;: case &quot;79&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff4b0ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;2&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe792ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;5&quot;: case &quot;100&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff4b1ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;6&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffec9cff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;8&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffbc1ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;10&quot;: case &quot;15&quot;: case &quot;64&quot;: case &quot;141&quot;: case &quot;147&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffffcbff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;11&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fd6831ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;12&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe997ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;13&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe794ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;14&quot;: case &quot;43&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff9bcff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;19&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fd7f38ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;22&quot;: case &quot;112&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff6b4ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;23&quot;: case &quot;47&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffde80ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;24&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe38aff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;25&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fed06cff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;26&quot;: case &quot;138&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffcc5ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;28&quot;: case &quot;63&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#feb650ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;30&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffdd7dff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;33&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffda77ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;36&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fed36fff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;39&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fec35eff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;41&quot;: case &quot;75&quot;: case &quot;148&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffdc7ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;44&quot;: case &quot;48&quot;: case &quot;142&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff7b7ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;50&quot;: case &quot;66&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffcc4ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;51&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#e0171cff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;52&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff5b4ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;54&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fed774ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;55&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fd7434ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;57&quot;: case &quot;76&quot;: case &quot;93&quot;: case &quot;98&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffdc5ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;59&quot;: case &quot;72&quot;: case &quot;113&quot;: case &quot;119&quot;: case &quot;135&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffffcaff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;60&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fec964ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;67&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff8baff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;68&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe691ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;69&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff5b3ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;70&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff9bdff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;73&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#feb24cff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;77&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff5b2ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;81&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffdc7cff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;82&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffdf83ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;83&quot;: case &quot;85&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffdc6ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;87&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#febf59ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;88&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fd8c3bff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;91&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffec9eff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;94&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffac0ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;95&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fecd69ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;101&quot;: case &quot;106&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff0a6ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;102&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe896ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;105&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fed26fff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;109&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#feb14bff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;110&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff0a7ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;114&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fecb66ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;115&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fd7033ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;120&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fec762ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;123&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#feb04bff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;125&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffdd7eff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;126&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#800026ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;127&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffe58fff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;129&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffea99ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;131&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#bf0025ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;132&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffdb7aff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;137&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fffabfff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;139&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffefa5ff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;145&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff3afff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                case &quot;149&quot;: 
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#fff9bbff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
                default:
                    return {&quot;color&quot;: &quot;black&quot;, &quot;fillColor&quot;: &quot;#ffffccff&quot;, &quot;fillOpacity&quot;: 0.9, &quot;opacity&quot;: 1, &quot;weight&quot;: 0.9};
            }
        }

        function geo_json_1bd2bffcea5e5eea4e76105154b7b526_onEachFeature(feature, layer) {
            layer.on({
            });
        };
        var geo_json_1bd2bffcea5e5eea4e76105154b7b526 = L.geoJson(null, {
                onEachFeature: geo_json_1bd2bffcea5e5eea4e76105154b7b526_onEachFeature,

                style: geo_json_1bd2bffcea5e5eea4e76105154b7b526_styler,
        });

        function geo_json_1bd2bffcea5e5eea4e76105154b7b526_add (data) {
            geo_json_1bd2bffcea5e5eea4e76105154b7b526
                .addData(data)
                .addTo(map_ed356993e9a4eba0572681c6be763cd3);
        }



    geo_json_1bd2bffcea5e5eea4e76105154b7b526.bindTooltip(
    function(layer){
    let div = L.DomUtil.create(&#x27;div&#x27;);

    let handleObject = feature=&gt;typeof(feature)==&#x27;object&#x27; ? JSON.stringify(feature) : feature;
    let fields = [&quot;buurt&quot;, &quot;dogs_registered&quot;, &quot;owners_age&quot;, &quot;status&quot;, &quot;most_migrants_met&quot;];
    let aliases = [&quot;buurt&quot;, &quot;dogs_registered&quot;, &quot;owners_age&quot;, &quot;status&quot;, &quot;most_migrants_met&quot;];
    let table = &#x27;&lt;table&gt;&#x27; +
        String(
        fields.map(
        (v,i)=&gt;
        `&lt;tr&gt;
            &lt;th&gt;${aliases[i]}&lt;/th&gt;

            &lt;td&gt;${handleObject(layer.feature.properties[v])}&lt;/td&gt;
        &lt;/tr&gt;`).join(&#x27;&#x27;))
    +&#x27;&lt;/table&gt;&#x27;;
    div.innerHTML=table;

    return div
    }
    ,{&quot;className&quot;: &quot;foliumtooltip&quot;, &quot;sticky&quot;: true});

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



An additional [data analysis of the dogs owners in Groningen in general](./dogs_eda_groningen.html) as well as [analysis of the neighborhoods where most of the dogs live](./dogs_lovers_eda.html) you can find at the separate pages.
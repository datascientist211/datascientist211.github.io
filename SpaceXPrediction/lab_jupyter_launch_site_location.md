<p style="text-align:center">
    <a href="https://skills.network/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01" target="_blank">
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/assets/logos/SN_web_lightmode.png" width="200" alt="Skills Network Logo"  />
    </a>
</p>


# **Launch Sites Locations Analysis with Folium**


Estimated time needed: **40** minutes


The launch success rate may depend on many factors such as payload mass, orbit type, and so on. It may also depend on the location and proximities of a launch site, i.e., the initial position of rocket trajectories. Finding an optimal location for building a launch site certainly involves many factors and hopefully we could discover some of the factors by analyzing the existing launch site locations.


In the previous exploratory data analysis labs, you have visualized the SpaceX launch dataset using `matplotlib` and `seaborn` and discovered some preliminary correlations between the launch site and success rates. In this lab, you will be performing more interactive visual analytics using `Folium`.


## Objectives


This lab contains the following tasks:

*   **TASK 1:** Mark all launch sites on a map
*   **TASK 2:** Mark the success/failed launches for each site on the map
*   **TASK 3:** Calculate the distances between a launch site to its proximities

After completed the above tasks, you should be able to find some geographical patterns about launch sites.


Let's first import required Python packages for this lab:



```python
!pip3 install folium
!pip3 install wget
```

    Requirement already satisfied: folium in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (0.13.0)
    Requirement already satisfied: branca>=0.3.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from folium) (0.5.0)
    Requirement already satisfied: jinja2>=2.9 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from folium) (3.1.2)
    Requirement already satisfied: numpy in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from folium) (1.23.4)
    Requirement already satisfied: requests in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from folium) (2.28.1)
    Requirement already satisfied: MarkupSafe>=2.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from jinja2>=2.9->folium) (2.1.1)
    Requirement already satisfied: charset-normalizer<3,>=2 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from requests->folium) (2.1.1)
    Requirement already satisfied: idna<4,>=2.5 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from requests->folium) (3.4)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from requests->folium) (1.26.12)
    Requirement already satisfied: certifi>=2017.4.17 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from requests->folium) (2022.9.24)
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip available: [0m[31;49m22.3[0m[39;49m -> [0m[32;49m22.3.1[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m
    Requirement already satisfied: wget in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (3.2)
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip available: [0m[31;49m22.3[0m[39;49m -> [0m[32;49m22.3.1[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m



```python
import folium
import wget
import pandas as pd
```


```python
# Import folium MarkerCluster plugin
from folium.plugins import MarkerCluster
# Import folium MousePosition plugin
from folium.plugins import MousePosition
# Import folium DivIcon plugin
from folium.features import DivIcon
```

If you need to refresh your memory about folium, you may download and refer to this previous folium lab:


[Generating Maps with Python](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module\_3/DV0101EN-3-5-1-Generating-Maps-in-Python-py-v2.0.ipynb)


## Task 1: Mark all launch sites on a map


First, let's try to add each site's location on a map using site's latitude and longitude coordinates


The following dataset with the name `spacex_launch_geo.csv` is an augmented dataset with latitude and longitude added for each site.



```python
# Download and read the `spacex_launch_geo.csv`
spacex_df=pd.read_csv('spacex_launch_geo.csv')
# Select relevant sub-columns: `Launch Site`, `Lat(Latitude)`, `Long(Longitude)`, `class`
spacex_df = spacex_df[['Launch Site', 'Lat', 'Long', 'class']]
launch_sites_df = spacex_df.groupby(['Launch Site'], as_index=False).first()
launch_sites_df = launch_sites_df[['Launch Site', 'Lat', 'Long']]
launch_sites_df.head()
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
      <th>Launch Site</th>
      <th>Lat</th>
      <th>Long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCAFS LC-40</td>
      <td>28.562302</td>
      <td>-80.577356</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CCAFS SLC-40</td>
      <td>28.563197</td>
      <td>-80.576820</td>
    </tr>
    <tr>
      <th>2</th>
      <td>KSC LC-39A</td>
      <td>28.573255</td>
      <td>-80.646895</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VAFB SLC-4E</td>
      <td>34.632834</td>
      <td>-120.610745</td>
    </tr>
  </tbody>
</table>
</div>



Now, you can take a look at what are the coordinates for each site.



```python
spacex_df=pd.read_csv('dataset_part_2.csv')
spacex_df.head()

# Select relevant sub-columns: `Launch Site`, `Lat(Latitude)`, `Long(Longitude)`, `class`
spacex_df = spacex_df[['LaunchSite', 'Latitude', 'Longitude', 'Class']]
launch_sites_df = spacex_df.groupby(['LaunchSite'], as_index=False).first()
launch_sites_df = launch_sites_df[['LaunchSite', 'Latitude', 'Longitude']]
launch_sites_df.head()
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
      <th>LaunchSite</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
    </tr>
    <tr>
      <th>1</th>
      <td>KSC LC 39A</td>
      <td>28.608058</td>
      <td>-80.603956</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VAFB SLC 4E</td>
      <td>34.632093</td>
      <td>-120.610829</td>
    </tr>
  </tbody>
</table>
</div>



Above coordinates are just plain numbers that can not give you any intuitive insights about where are those launch sites. If you are very good at geography, you can interpret those numbers directly in your mind. If not, that's fine too. Let's visualize those locations by pinning them on a map.


We first need to create a folium `Map` object, with an initial center location to be NASA Johnson Space Center at Houston, Texas.



```python
# Start location is NASA Johnson Space Center
nasa_coordinate = [29.559684888503615, -95.0830971930759]
site_map = folium.Map(location=nasa_coordinate, zoom_start=10)
```

We could use `folium.Circle` to add a highlighted circle area with a text label on a specific coordinate. For example,



```python
# Create a blue circle at NASA Johnson Space Center's coordinate with a popup label showing its name
circle = folium.Circle(nasa_coordinate, radius=1000, color='#d35400', fill=True).add_child(folium.Popup('NASA Johnson Space Center'))
# Create a blue circle at NASA Johnson Space Center's coordinate with a icon showing its name
marker = folium.map.Marker(
    nasa_coordinate,
    # Create an icon as a text label
    icon=DivIcon(
        icon_size=(20,20),
        icon_anchor=(0,0),
        html='<div style="font-size: 12; color:#d35400;"><b>%s</b></div>' % 'NASA JSC',
        )
    )
site_map.add_child(circle)
site_map.add_child(marker)
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
                #map_15139428df27afb7126845ed872b77b4 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_15139428df27afb7126845ed872b77b4&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_15139428df27afb7126845ed872b77b4 = L.map(
                &quot;map_15139428df27afb7126845ed872b77b4&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 10,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_26e4f2d71634aa556c122debeb1764c8 = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_15139428df27afb7126845ed872b77b4);


            var circle_35e2a38c714c8e9e22ed417b61e28b85 = L.circle(
                [29.559684888503615, -95.0830971930759],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_15139428df27afb7126845ed872b77b4);


        var popup_c9c6325d15f6479fbd87fe06b716c792 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_12599e14c2c4688323b779818a4b5e1e = $(`&lt;div id=&quot;html_12599e14c2c4688323b779818a4b5e1e&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;NASA Johnson Space Center&lt;/div&gt;`)[0];
                popup_c9c6325d15f6479fbd87fe06b716c792.setContent(html_12599e14c2c4688323b779818a4b5e1e);



        circle_35e2a38c714c8e9e22ed417b61e28b85.bindPopup(popup_c9c6325d15f6479fbd87fe06b716c792)
        ;




            var marker_8aea3f4256a39022cf3b15a31cc49d56 = L.marker(
                [29.559684888503615, -95.0830971930759],
                {}
            ).addTo(map_15139428df27afb7126845ed872b77b4);


            var div_icon_39aa522793b77237dcd5bd31dffde214 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eNASA JSC\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_8aea3f4256a39022cf3b15a31cc49d56.setIcon(div_icon_39aa522793b77237dcd5bd31dffde214);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



and you should find a small yellow circle near the city of Houston and you can zoom-in to see a larger circle.


Now, let's add a circle for each launch site in data frame `launch_sites`


*TODO:*  Create and add `folium.Circle` and `folium.Marker` for each launch site on the site map


An example of folium.Circle:


`folium.Circle(coordinate, radius=1000, color='#000000', fill=True).add_child(folium.Popup(...))`


An example of folium.Marker:


`folium.map.Marker(coordinate, icon=DivIcon(icon_size=(20,20),icon_anchor=(0,0), html='<div style="font-size: 12; color:#d35400;"><b>%s</b></div>' % 'label', ))`



```python
# Initial the map
site_map = folium.Map(location=nasa_coordinate, zoom_start=5)
# For each launch site, add a Circle object based on its coordinate (Lat, Long) values. In addition, add Launch site name as a popup label
for index, row in launch_sites_df.iterrows():
    coordinate = [row["Latitude"], row["Longitude"]]#coordinate = [row["Lat"], row["Long"]]
    launchSite = row["LaunchSite"]#launchSite = row["Launch Site"]
    circle = folium.Circle(coordinate, radius=1000, color='#d35400', fill=True).add_child(folium.Popup(launchSite)).add_to(site_map)
    marker = folium.map.Marker(
    coordinate,
    # Create an icon as a text label
    icon=DivIcon(
        icon_size=(20,20),
        icon_anchor=(0,0),html='<div style="font-size: 12; color:#d35400;"><b>%s</b></div>' % launchSite,
        )
    ).add_to(site_map)
    
site_map
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
                #map_d368eaa66911ac76cdc66071a54e7a4a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_d368eaa66911ac76cdc66071a54e7a4a = L.map(
                &quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 5,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_6a6dd63d3a16d33dbc7c6a5ce61a614f = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var circle_09e82ae54ea55eb70d02663e6d4fd807 = L.circle(
                [28.5618571, -80.577366],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_26200c74ecb8356af72e59c8de14bc0a = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_0b4d23af6deac36ab2a0e6ad9141e28a = $(`&lt;div id=&quot;html_0b4d23af6deac36ab2a0e6ad9141e28a&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;CCSFS SLC 40&lt;/div&gt;`)[0];
                popup_26200c74ecb8356af72e59c8de14bc0a.setContent(html_0b4d23af6deac36ab2a0e6ad9141e28a);



        circle_09e82ae54ea55eb70d02663e6d4fd807.bindPopup(popup_26200c74ecb8356af72e59c8de14bc0a)
        ;




            var marker_e171461b01d6b4700b99847473339ad4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_3f1d400bf8c012d2ef5764b75c6ab41a = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eCCSFS SLC 40\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_e171461b01d6b4700b99847473339ad4.setIcon(div_icon_3f1d400bf8c012d2ef5764b75c6ab41a);


            var circle_d98165e55558f9f5f11d0b869e76c55a = L.circle(
                [28.6080585, -80.6039558],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_777bb510b47559a11702bc169d967e63 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_6497d437dcb578c0f8c6bb7b5a3dbdd6 = $(`&lt;div id=&quot;html_6497d437dcb578c0f8c6bb7b5a3dbdd6&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;KSC LC 39A&lt;/div&gt;`)[0];
                popup_777bb510b47559a11702bc169d967e63.setContent(html_6497d437dcb578c0f8c6bb7b5a3dbdd6);



        circle_d98165e55558f9f5f11d0b869e76c55a.bindPopup(popup_777bb510b47559a11702bc169d967e63)
        ;




            var marker_f57fea6caa82c60177b0fd4e8ff11a09 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_33a1c124b806db61af88c3a05ba0a3a8 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eKSC LC 39A\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f57fea6caa82c60177b0fd4e8ff11a09.setIcon(div_icon_33a1c124b806db61af88c3a05ba0a3a8);


            var circle_836706b82952ca5ef3a3059d08a13d33 = L.circle(
                [34.632093, -120.610829],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_09f17b50bcfd8edb3350a1f00d21beb7 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_1281760bebe660c6f20743f8a44a4190 = $(`&lt;div id=&quot;html_1281760bebe660c6f20743f8a44a4190&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;VAFB SLC 4E&lt;/div&gt;`)[0];
                popup_09f17b50bcfd8edb3350a1f00d21beb7.setContent(html_1281760bebe660c6f20743f8a44a4190);



        circle_836706b82952ca5ef3a3059d08a13d33.bindPopup(popup_09f17b50bcfd8edb3350a1f00d21beb7)
        ;




            var marker_f20a64e42fb3657c81fecabae2eef414 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ed87eb3ea7b7542002106081d21adce7 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eVAFB SLC 4E\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f20a64e42fb3657c81fecabae2eef414.setIcon(div_icon_ed87eb3ea7b7542002106081d21adce7);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



The generated map with marked launch sites should look similar to the following:


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/launch_site_markers.png" />
</center>


Now, you can explore the map by zoom-in/out the marked areas
, and try to answer the following questions:

*   Are all launch sites in proximity to the Equator line?
*   Are all launch sites in very close proximity to the coast?

Also please try to explain your findings.


# Task 2: Mark the success/failed launches for each site on the map


Next, let's try to enhance the map by adding the launch outcomes for each site, and see which sites have high success rates.
Recall that data frame spacex_df has detailed launch records, and the `class` column indicates if this launch was successful or not



```python
spacex_df.tail(10)
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
      <th>LaunchSite</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>80</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
    <tr>
      <th>81</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
    <tr>
      <th>82</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
    <tr>
      <th>83</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
    <tr>
      <th>84</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
    <tr>
      <th>85</th>
      <td>KSC LC 39A</td>
      <td>28.608058</td>
      <td>-80.603956</td>
      <td>1</td>
    </tr>
    <tr>
      <th>86</th>
      <td>KSC LC 39A</td>
      <td>28.608058</td>
      <td>-80.603956</td>
      <td>1</td>
    </tr>
    <tr>
      <th>87</th>
      <td>KSC LC 39A</td>
      <td>28.608058</td>
      <td>-80.603956</td>
      <td>1</td>
    </tr>
    <tr>
      <th>88</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
    <tr>
      <th>89</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



Next, let's create markers for all launch records.
If a launch was successful `(class=1)`, then we use a green marker and if a launch was failed, we use a red marker `(class=0)`


Note that a launch only happens in one of the four launch sites, which means many launch records will have the exact same coordinate. Marker clusters can be a good way to simplify a map containing many markers having the same coordinate.


Let's first create a `MarkerCluster` object



```python
marker_cluster = MarkerCluster()

```

*TODO:* Create a new column in `1` dataframe called `marker_color` to store the marker colors based on the `class` value



```python

# Apply a function to check the value of `class` column
# If class=1, marker_color value will be green
# If class=0, marker_color value will be red

```


```python
# Function to assign color to launch outcome
def assign_marker_color(launch_outcome):
    if launch_outcome == 1:
        return 'green'
    else:
        return 'red'
    
spacex_df['marker_color'] = spacex_df['Class'].apply(assign_marker_color)
spacex_df.head(10)
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
      <th>LaunchSite</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Class</th>
      <th>marker_color</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>2</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VAFB SLC 4E</td>
      <td>34.632093</td>
      <td>-120.610829</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>5</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>6</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
      <td>green</td>
    </tr>
    <tr>
      <th>7</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>1</td>
      <td>green</td>
    </tr>
    <tr>
      <th>8</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
    <tr>
      <th>9</th>
      <td>CCSFS SLC 40</td>
      <td>28.561857</td>
      <td>-80.577366</td>
      <td>0</td>
      <td>red</td>
    </tr>
  </tbody>
</table>
</div>



*TODO:* For each launch result in `spacex_df` data frame, add a `folium.Marker` to `marker_cluster`



```python
# Add marker_cluster to current site_map
site_map.add_child(marker_cluster)

# for each row in spacex_df data frame
# create a Marker object with its coordinate
# and customize the Marker's icon property to indicate if this launch was successed or failed, 
# e.g., icon=folium.Icon(color='white', icon_color=row['marker_color']
for index, record in spacex_df.iterrows():
    coordinate = [record["Latitude"], record["Longitude"]]#coordinate = [record["Lat"], record["Long"]]
    launchSite = record["LaunchSite"]#launchSite = record["Launch Site"]
    # TODO: Create and add a Marker cluster to the site map
    # marker = folium.Marker(...)
    marker = folium.map.Marker(
        coordinate,
        icon=folium.Icon(
            color='white', 
            icon_color=record['marker_color']
        )
    )#.add_to(site_map)
    marker_cluster.add_child(marker)

site_map
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
                #map_d368eaa66911ac76cdc66071a54e7a4a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/leaflet.markercluster.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.Default.css&quot;/&gt;
&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_d368eaa66911ac76cdc66071a54e7a4a = L.map(
                &quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 5,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_6a6dd63d3a16d33dbc7c6a5ce61a614f = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var circle_09e82ae54ea55eb70d02663e6d4fd807 = L.circle(
                [28.5618571, -80.577366],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_26200c74ecb8356af72e59c8de14bc0a = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_0b4d23af6deac36ab2a0e6ad9141e28a = $(`&lt;div id=&quot;html_0b4d23af6deac36ab2a0e6ad9141e28a&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;CCSFS SLC 40&lt;/div&gt;`)[0];
                popup_26200c74ecb8356af72e59c8de14bc0a.setContent(html_0b4d23af6deac36ab2a0e6ad9141e28a);



        circle_09e82ae54ea55eb70d02663e6d4fd807.bindPopup(popup_26200c74ecb8356af72e59c8de14bc0a)
        ;




            var marker_e171461b01d6b4700b99847473339ad4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_3f1d400bf8c012d2ef5764b75c6ab41a = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eCCSFS SLC 40\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_e171461b01d6b4700b99847473339ad4.setIcon(div_icon_3f1d400bf8c012d2ef5764b75c6ab41a);


            var circle_d98165e55558f9f5f11d0b869e76c55a = L.circle(
                [28.6080585, -80.6039558],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_777bb510b47559a11702bc169d967e63 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_6497d437dcb578c0f8c6bb7b5a3dbdd6 = $(`&lt;div id=&quot;html_6497d437dcb578c0f8c6bb7b5a3dbdd6&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;KSC LC 39A&lt;/div&gt;`)[0];
                popup_777bb510b47559a11702bc169d967e63.setContent(html_6497d437dcb578c0f8c6bb7b5a3dbdd6);



        circle_d98165e55558f9f5f11d0b869e76c55a.bindPopup(popup_777bb510b47559a11702bc169d967e63)
        ;




            var marker_f57fea6caa82c60177b0fd4e8ff11a09 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_33a1c124b806db61af88c3a05ba0a3a8 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eKSC LC 39A\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f57fea6caa82c60177b0fd4e8ff11a09.setIcon(div_icon_33a1c124b806db61af88c3a05ba0a3a8);


            var circle_836706b82952ca5ef3a3059d08a13d33 = L.circle(
                [34.632093, -120.610829],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_09f17b50bcfd8edb3350a1f00d21beb7 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_1281760bebe660c6f20743f8a44a4190 = $(`&lt;div id=&quot;html_1281760bebe660c6f20743f8a44a4190&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;VAFB SLC 4E&lt;/div&gt;`)[0];
                popup_09f17b50bcfd8edb3350a1f00d21beb7.setContent(html_1281760bebe660c6f20743f8a44a4190);



        circle_836706b82952ca5ef3a3059d08a13d33.bindPopup(popup_09f17b50bcfd8edb3350a1f00d21beb7)
        ;




            var marker_f20a64e42fb3657c81fecabae2eef414 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ed87eb3ea7b7542002106081d21adce7 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eVAFB SLC 4E\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f20a64e42fb3657c81fecabae2eef414.setIcon(div_icon_ed87eb3ea7b7542002106081d21adce7);


            var marker_cluster_f116777a8f89526d71ccfa29da685556 = L.markerClusterGroup(
                {}
            );
            map_d368eaa66911ac76cdc66071a54e7a4a.addLayer(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var marker_8e3fbc734492c596f285a59bbd2828eb = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_be99b870932b60b5a059abc5d3eea43b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8e3fbc734492c596f285a59bbd2828eb.setIcon(icon_be99b870932b60b5a059abc5d3eea43b);


            var marker_b265ccc5f3ff2f93e4a59d019a33539a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6ce6675244c5eec8cea2da0f8e52b4c3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b265ccc5f3ff2f93e4a59d019a33539a.setIcon(icon_6ce6675244c5eec8cea2da0f8e52b4c3);


            var marker_e7f8badc6b329032084deb57204bf78d = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_98be638d075e9c8b1753032889c91604 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e7f8badc6b329032084deb57204bf78d.setIcon(icon_98be638d075e9c8b1753032889c91604);


            var marker_9762b11ec5c9a7929169dd5226ab7a7a = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9762b11ec5c9a7929169dd5226ab7a7a.setIcon(icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c);


            var marker_05496be246e112b1586148e09ca8255b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_39d29742c4a629c987f9a863222ac384 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_05496be246e112b1586148e09ca8255b.setIcon(icon_39d29742c4a629c987f9a863222ac384);


            var marker_26b020c8440b964ed52b8233103e3807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0a8961b079f2e64d955495c92596aebe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_26b020c8440b964ed52b8233103e3807.setIcon(icon_0a8961b079f2e64d955495c92596aebe);


            var marker_0c55aa8394bec09019f5fe3bf99655fa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_bbb9ec9636836fd1c13ef05a9691e2af = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c55aa8394bec09019f5fe3bf99655fa.setIcon(icon_bbb9ec9636836fd1c13ef05a9691e2af);


            var marker_af5595c2b48dff9c4425acccf92fc5aa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3130e369bd0b97da036f4a7d8d5c916d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_af5595c2b48dff9c4425acccf92fc5aa.setIcon(icon_3130e369bd0b97da036f4a7d8d5c916d);


            var marker_2102710f13142fd96b2e8d017f15d45e = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3fb6ccb5eae44b18acb0b636f915d39a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2102710f13142fd96b2e8d017f15d45e.setIcon(icon_3fb6ccb5eae44b18acb0b636f915d39a);


            var marker_aaad09c638eba800a7811229eae101b1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d2a0ee20131bc6466a138caaca22529 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aaad09c638eba800a7811229eae101b1.setIcon(icon_0d2a0ee20131bc6466a138caaca22529);


            var marker_4683b9f2e56d38aa1acdf4ad7bbec9ec = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0494882a0773383ff6ab4f7b01c3855a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4683b9f2e56d38aa1acdf4ad7bbec9ec.setIcon(icon_0494882a0773383ff6ab4f7b01c3855a);


            var marker_2c87eb3a097f8f52b16674aceca0c4e0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_c03256aaa0fb0c018707714a45110c29 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c87eb3a097f8f52b16674aceca0c4e0.setIcon(icon_c03256aaa0fb0c018707714a45110c29);


            var marker_acd191db0be53bfd6645a49e1328dc14 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_10e4b136da16c6dae6f8747b86a009de = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_acd191db0be53bfd6645a49e1328dc14.setIcon(icon_10e4b136da16c6dae6f8747b86a009de);


            var marker_c90eed40cb69231d1ad1d5b1b14b2fd1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_4e3460ba0bea820a9b8811d9374d663b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c90eed40cb69231d1ad1d5b1b14b2fd1.setIcon(icon_4e3460ba0bea820a9b8811d9374d663b);


            var marker_280d50c0fecd2cf9a46560dbe6ef20f1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b94714aae52c32397682f3a3aa734555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_280d50c0fecd2cf9a46560dbe6ef20f1.setIcon(icon_b94714aae52c32397682f3a3aa734555);


            var marker_533be051d47af44f20479dde1ed40de0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1df4fc9edee2cae23a865eaded820bf8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_533be051d47af44f20479dde1ed40de0.setIcon(icon_1df4fc9edee2cae23a865eaded820bf8);


            var marker_e19ccf9d8957d95c236100fd2a48623c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e93489884b4c7553766caae417e1a5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e19ccf9d8957d95c236100fd2a48623c.setIcon(icon_e93489884b4c7553766caae417e1a5e6);


            var marker_0ee3df4535b0e25e208cec9a6d0d2c56 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b2d7914a31750a68d48b38d61d336ab9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ee3df4535b0e25e208cec9a6d0d2c56.setIcon(icon_b2d7914a31750a68d48b38d61d336ab9);


            var marker_510b2c2da7cea926b61216c9f4ad7f5b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3315d1234c29a44ed5b3d22c92c20526 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_510b2c2da7cea926b61216c9f4ad7f5b.setIcon(icon_3315d1234c29a44ed5b3d22c92c20526);


            var marker_0c94063a2c7c0b18d2c25fb20f610644 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fde389ae32dedeeeb62bbe811db695eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c94063a2c7c0b18d2c25fb20f610644.setIcon(icon_fde389ae32dedeeeb62bbe811db695eb);


            var marker_0ca96a4682816125e07dbbba2696044c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_47bac55069c2de5ef303c417a2a220eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ca96a4682816125e07dbbba2696044c.setIcon(icon_47bac55069c2de5ef303c417a2a220eb);


            var marker_a3a2d8d01b7de1145acf689ee5bacec8 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_83635d34593afb3ca36a337417c6d55a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a3a2d8d01b7de1145acf689ee5bacec8.setIcon(icon_83635d34593afb3ca36a337417c6d55a);


            var marker_8c2f1eaf61c7e33d6893c83f9ad04f0f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6063d0c719d73692bb69610728fdfa41 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8c2f1eaf61c7e33d6893c83f9ad04f0f.setIcon(icon_6063d0c719d73692bb69610728fdfa41);


            var marker_823751abb97e42969d8e1ab7bb1338c0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_147445c198f6902c97d5ddabb2cbbe6e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_823751abb97e42969d8e1ab7bb1338c0.setIcon(icon_147445c198f6902c97d5ddabb2cbbe6e);


            var marker_dd5006505c03160f51c570ec351bb2e2 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b98939d141ba86ae0b85d89229220c0a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dd5006505c03160f51c570ec351bb2e2.setIcon(icon_b98939d141ba86ae0b85d89229220c0a);


            var marker_8d451c8bbf3b65058c32bfd171379021 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f96f31fc348a21e9883ec56e022eb5d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d451c8bbf3b65058c32bfd171379021.setIcon(icon_1f96f31fc348a21e9883ec56e022eb5d);


            var marker_708fcdb0eb1bee81a0676714a97c4b7e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8d0d02face42693df9cc809b6b1b8b88 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_708fcdb0eb1bee81a0676714a97c4b7e.setIcon(icon_8d0d02face42693df9cc809b6b1b8b88);


            var marker_b20e0cffdc1dcabd8977b8743bfdc23f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e05584755ae63037f94b6629a8a59059 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b20e0cffdc1dcabd8977b8743bfdc23f.setIcon(icon_e05584755ae63037f94b6629a8a59059);


            var marker_12c5ae814402608fe9fcaa0fb953baa3 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_298559faf2923df091deb8a400f32a3a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_12c5ae814402608fe9fcaa0fb953baa3.setIcon(icon_298559faf2923df091deb8a400f32a3a);


            var marker_c7099d8bd405dca8c5d799d78046d989 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8ea03eccae8270a9959977a4e83ad81c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c7099d8bd405dca8c5d799d78046d989.setIcon(icon_8ea03eccae8270a9959977a4e83ad81c);


            var marker_2000a471ecc645745690b5601bf318b9 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_06a011613bb1dd586a10d8b689767a67 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2000a471ecc645745690b5601bf318b9.setIcon(icon_06a011613bb1dd586a10d8b689767a67);


            var marker_9add50083d8f937cdbf3268c42ea3458 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f1103989b51f2b44128d791224a47fb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9add50083d8f937cdbf3268c42ea3458.setIcon(icon_0f1103989b51f2b44128d791224a47fb);


            var marker_fc40396968e3dbf3f0ac5f4e4d2b5a25 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_143a907a9fb195f8a49fc5e047916e95 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fc40396968e3dbf3f0ac5f4e4d2b5a25.setIcon(icon_143a907a9fb195f8a49fc5e047916e95);


            var marker_0191d721bf323854bc15826cdfe155dc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b1888b082353ed7ee63f02be42251844 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0191d721bf323854bc15826cdfe155dc.setIcon(icon_b1888b082353ed7ee63f02be42251844);


            var marker_519c51aeb2798288434522e9ac6c87c1 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2d0385ab07b5db8469e705e50c436347 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_519c51aeb2798288434522e9ac6c87c1.setIcon(icon_2d0385ab07b5db8469e705e50c436347);


            var marker_0c386873db0e06685e966e18238cd440 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5aee252c188ed86e6db7603935c81304 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c386873db0e06685e966e18238cd440.setIcon(icon_5aee252c188ed86e6db7603935c81304);


            var marker_23ec039ad7d08965f7fe479f3ac68d16 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b8059df365b79a0aa6df0de16ac9b5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_23ec039ad7d08965f7fe479f3ac68d16.setIcon(icon_b8059df365b79a0aa6df0de16ac9b5e6);


            var marker_84633a4c74c61cb29d1203a61dd3c729 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8400de5dc2bd3806576abe981463b13f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_84633a4c74c61cb29d1203a61dd3c729.setIcon(icon_8400de5dc2bd3806576abe981463b13f);


            var marker_daf80663ebf4530b28f47594d7374072 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e9a05afd4d6a84c274fa4130eb78b10a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_daf80663ebf4530b28f47594d7374072.setIcon(icon_e9a05afd4d6a84c274fa4130eb78b10a);


            var marker_d6bedb7815d6f288e1d6e598e9baf08f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d6bedb7815d6f288e1d6e598e9baf08f.setIcon(icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6);


            var marker_2988ed8d1eb975d3e0289077e01561a6 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f8a0e6456d1d85ca04f373538d9dab31 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2988ed8d1eb975d3e0289077e01561a6.setIcon(icon_f8a0e6456d1d85ca04f373538d9dab31);


            var marker_ba02f01ffa1ca683ec3cd7b81f4eea0a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_97fcd29be95d04c61837ac90161f99ea = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ba02f01ffa1ca683ec3cd7b81f4eea0a.setIcon(icon_97fcd29be95d04c61837ac90161f99ea);


            var marker_bc822aae3c14f72a8da810e9a99b6bac = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fc9de103d81f6167149e720d5aa5aa2b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_bc822aae3c14f72a8da810e9a99b6bac.setIcon(icon_fc9de103d81f6167149e720d5aa5aa2b);


            var marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_838977de5280226a2bfa533389b76480 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20.setIcon(icon_838977de5280226a2bfa533389b76480);


            var marker_a52800e1677bceb4962f64c50390bb66 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f1dbbf3a3c33e7722e96b26b0e738ec6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a52800e1677bceb4962f64c50390bb66.setIcon(icon_f1dbbf3a3c33e7722e96b26b0e738ec6);


            var marker_7d1155bf736c8cdb6c4901fc51d8fd38 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_ef825d97216fbe7e868ca772588987d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7d1155bf736c8cdb6c4901fc51d8fd38.setIcon(icon_ef825d97216fbe7e868ca772588987d8);


            var marker_ca923c0327d25870f976c7f15784b537 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1f2b83e998d1e4caf4a9d56832f4743 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ca923c0327d25870f976c7f15784b537.setIcon(icon_a1f2b83e998d1e4caf4a9d56832f4743);


            var marker_ff790ea4e51733a9a60bccb92e9abafc = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2ab4348c668a55fc9294e5960858a828 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff790ea4e51733a9a60bccb92e9abafc.setIcon(icon_2ab4348c668a55fc9294e5960858a828);


            var marker_dac712da8151aa914d0d499e9c23fae5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_706d6845d3884cf2f2193927191f7205 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dac712da8151aa914d0d499e9c23fae5.setIcon(icon_706d6845d3884cf2f2193927191f7205);


            var marker_59d64bd4daa0e4c76bc0b7e96db776a5 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_71f216c61addb87f1116e9c737ad2a58 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_59d64bd4daa0e4c76bc0b7e96db776a5.setIcon(icon_71f216c61addb87f1116e9c737ad2a58);


            var marker_1ed241d8a0b5227618f2c00123a45520 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f6e90640e75add03cf3655d777cf298 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_1ed241d8a0b5227618f2c00123a45520.setIcon(icon_1f6e90640e75add03cf3655d777cf298);


            var marker_0fe5cf43ec2696ab17ee89435c81fa59 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7def62d3317a8bc6c77433d3754e21f1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0fe5cf43ec2696ab17ee89435c81fa59.setIcon(icon_7def62d3317a8bc6c77433d3754e21f1);


            var marker_6bb1d8ff9f26c26136f7a8d3130c7795 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_db713ff653ba3f3538a3e5cf442bee18 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6bb1d8ff9f26c26136f7a8d3130c7795.setIcon(icon_db713ff653ba3f3538a3e5cf442bee18);


            var marker_40ad47531ee3347bc2b8307c3d482f84 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_056f353f0ab0f3940d0edeb5a62d9450 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_40ad47531ee3347bc2b8307c3d482f84.setIcon(icon_056f353f0ab0f3940d0edeb5a62d9450);


            var marker_2f5e28ca54928223f465ad1e9b2d1a2c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5fe6f90fe798a441c8ef45f7d3ca4a83 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2f5e28ca54928223f465ad1e9b2d1a2c.setIcon(icon_5fe6f90fe798a441c8ef45f7d3ca4a83);


            var marker_9aa6387fdb95f9185c705393980f5d3f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_603c180022e818d583932c7ef1743ed2 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9aa6387fdb95f9185c705393980f5d3f.setIcon(icon_603c180022e818d583932c7ef1743ed2);


            var marker_03999bb96d185e87146526d6aad223da = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_59fdaca20197a09206d1af4a2af7a6ff = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_03999bb96d185e87146526d6aad223da.setIcon(icon_59fdaca20197a09206d1af4a2af7a6ff);


            var marker_9579df7e2872157b05112a0a74207fff = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b5fe47b713a39ecd2c5016e4837d8f21 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9579df7e2872157b05112a0a74207fff.setIcon(icon_b5fe47b713a39ecd2c5016e4837d8f21);


            var marker_fe58589b434072a96e9ca818337e301e = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_22ef0459c3d7134064b56333a32c3a34 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fe58589b434072a96e9ca818337e301e.setIcon(icon_22ef0459c3d7134064b56333a32c3a34);


            var marker_2c2708b05946fddc4cb79d1f570c0411 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f227efc1cf97295470efc9ff7a5250ee = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c2708b05946fddc4cb79d1f570c0411.setIcon(icon_f227efc1cf97295470efc9ff7a5250ee);


            var marker_8dfdc79822d8532d024e26c0533b3528 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e214056aead57dde15d727a608736dbc = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8dfdc79822d8532d024e26c0533b3528.setIcon(icon_e214056aead57dde15d727a608736dbc);


            var marker_65762ab5b76ba27e40d107ea6d5662fc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6fa2242edc8c567ce471f952aadcff4f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_65762ab5b76ba27e40d107ea6d5662fc.setIcon(icon_6fa2242edc8c567ce471f952aadcff4f);


            var marker_b2f4dffaa3b8995e46edc01b5e231294 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_745981901199b63d593decaaf610049d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b2f4dffaa3b8995e46edc01b5e231294.setIcon(icon_745981901199b63d593decaaf610049d);


            var marker_a9308cfef5cd0c0e7a6434475f790736 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d908ab4d9700ce83efee476e886b97c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a9308cfef5cd0c0e7a6434475f790736.setIcon(icon_0d908ab4d9700ce83efee476e886b97c);


            var marker_d0cb20b9c2fbabc28c8bd6667c4d20f4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_01f7752eabf64318413fd39cb59b0d16 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d0cb20b9c2fbabc28c8bd6667c4d20f4.setIcon(icon_01f7752eabf64318413fd39cb59b0d16);


            var marker_37ff74361f6f1ed036dde3dc72a710d4 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6bfa6cc0bd3deb7ee1305998105d067c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_37ff74361f6f1ed036dde3dc72a710d4.setIcon(icon_6bfa6cc0bd3deb7ee1305998105d067c);


            var marker_348bd81104c9276a374aeee3bf631025 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5450a9a60d82142d1bde1659c90a9a6f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_348bd81104c9276a374aeee3bf631025.setIcon(icon_5450a9a60d82142d1bde1659c90a9a6f);


            var marker_656aa8c496ce988aa58b4365bae3cb48 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_32b90f4bd7cd6a319396722faff2939e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_656aa8c496ce988aa58b4365bae3cb48.setIcon(icon_32b90f4bd7cd6a319396722faff2939e);


            var marker_b880452991c33e3f8f68f6cb4d363db9 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1aaa4c50431786cd7394f328d450b0c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b880452991c33e3f8f68f6cb4d363db9.setIcon(icon_a1aaa4c50431786cd7394f328d450b0c);


            var marker_5c48f11769ffa2eafd0f12a25ca369de = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5599a959cd465cd28701318805cb19d1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5c48f11769ffa2eafd0f12a25ca369de.setIcon(icon_5599a959cd465cd28701318805cb19d1);


            var marker_f48cc39051e69a5069cbc8fb6f789f89 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_48b719408e4dce1f37c15725ffff8c1b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_f48cc39051e69a5069cbc8fb6f789f89.setIcon(icon_48b719408e4dce1f37c15725ffff8c1b);


            var marker_df2855f0ce1391655b901800e790a7b5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e71d9950cadbbc68538039b6a52b501 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_df2855f0ce1391655b901800e790a7b5.setIcon(icon_8e71d9950cadbbc68538039b6a52b501);


            var marker_cf7cac7da28106a1abbc95f56c8cad2f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e8913d5711160f297a4ed477f016f5d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cf7cac7da28106a1abbc95f56c8cad2f.setIcon(icon_e8913d5711160f297a4ed477f016f5d8);


            var marker_49aac07dc6d5f089b3f54172a7567169 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e5dcbd4cfc8bcfabd0f03262f16434c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_49aac07dc6d5f089b3f54172a7567169.setIcon(icon_8e5dcbd4cfc8bcfabd0f03262f16434c);


            var marker_efd69645b1c9090161b21ad65eb9da28 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_40cd0958be52c8dc2f6ebe70e1860f98 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_efd69645b1c9090161b21ad65eb9da28.setIcon(icon_40cd0958be52c8dc2f6ebe70e1860f98);


            var marker_ff3388e2d1de09848799c111c1d77c75 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_cdd9837aa5d8b7d9e4b337c560b75293 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff3388e2d1de09848799c111c1d77c75.setIcon(icon_cdd9837aa5d8b7d9e4b337c560b75293);


            var marker_541ba9984e25e0505a1ba82f62b55260 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f32393a6937917b2179d8524c8d64631 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_541ba9984e25e0505a1ba82f62b55260.setIcon(icon_f32393a6937917b2179d8524c8d64631);


            var marker_b4cff47336487516d8fd112d0fd46397 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e92e21cfdb36cedfc2d6fd7134c3a3a5 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b4cff47336487516d8fd112d0fd46397.setIcon(icon_e92e21cfdb36cedfc2d6fd7134c3a3a5);


            var marker_51f0e797a0669d5c678d7fe0f11a138e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7a6e41bff251b2c74a5729b43ea93875 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_51f0e797a0669d5c678d7fe0f11a138e.setIcon(icon_7a6e41bff251b2c74a5729b43ea93875);


            var marker_5225739e6f8da47c94b201312b4d50f0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a21b7dfed11f7d1b1b1a58585fca21b0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5225739e6f8da47c94b201312b4d50f0.setIcon(icon_a21b7dfed11f7d1b1b1a58585fca21b0);


            var marker_2935d90ec3ba45cd2543ef9a64eaeecf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5d334919024cba9d301f5a4679399559 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2935d90ec3ba45cd2543ef9a64eaeecf.setIcon(icon_5d334919024cba9d301f5a4679399559);


            var marker_ef2fa80060c883e930db51a7fb2399cf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f630b606af0150713437f16c0eba879 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ef2fa80060c883e930db51a7fb2399cf.setIcon(icon_0f630b606af0150713437f16c0eba879);


            var marker_95a789ff1c1867efc53540af8910ccdd = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e0c97543bf8755ade1f6249efd380bb9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_95a789ff1c1867efc53540af8910ccdd.setIcon(icon_e0c97543bf8755ade1f6249efd380bb9);


            var marker_3f077b03e537d128163b5c86927feadf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_030810f0e1972c7cbbfd2765fa19299c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_3f077b03e537d128163b5c86927feadf.setIcon(icon_030810f0e1972c7cbbfd2765fa19299c);


            var marker_8d8d8576bfb12f4c3f0ac3750d18e505 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d8d8576bfb12f4c3f0ac3750d18e505.setIcon(icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0);


            var marker_4968ba53f5d12369310ccf9994be42b8 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91a4b514ce940d93d1511ae5ebb60c47 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4968ba53f5d12369310ccf9994be42b8.setIcon(icon_91a4b514ce940d93d1511ae5ebb60c47);


            var marker_edf1b4d1c86b086042fdb78a6a50309c = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1add5061d056faaf5a3e799ba9ae555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_edf1b4d1c86b086042fdb78a6a50309c.setIcon(icon_a1add5061d056faaf5a3e799ba9ae555);


            var marker_aec1ac925773a73c70fb629ce7d03ad2 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91148a541330e5590009d961d5206cfe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aec1ac925773a73c70fb629ce7d03ad2.setIcon(icon_91148a541330e5590009d961d5206cfe);


            var marker_0b2d624e4a25e562d789b120d4f279da = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_559e6106fc82969e429355fd2bd8dfe8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0b2d624e4a25e562d789b120d4f279da.setIcon(icon_559e6106fc82969e429355fd2bd8dfe8);


            var marker_261c349094a575034ead3af7f7919807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_70ceb3c0a910779f13a144b4550f3670 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_261c349094a575034ead3af7f7919807.setIcon(icon_70ceb3c0a910779f13a144b4550f3670);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



Your updated map may look like the following screenshots:


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/launch_site_marker_cluster.png" />
</center>


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/launch_site_marker_cluster_zoomed.png" />
</center>


From the color-labeled markers in marker clusters, you should be able to easily identify which launch sites have relatively high success rates.


# TASK 3: Calculate the distances between a launch site to its proximities


Next, we need to explore and analyze the proximities of launch sites.


Let's first add a `MousePosition` on the map to get coordinate for a mouse over a point on the map. As such, while you are exploring the map, you can easily find the coordinates of any points of interests (such as railway)



```python
# Add Mouse Position to get the coordinate (Lat, Long) for a mouse over on the map
formatter = "function(num) {return L.Util.formatNum(num, 5);};"
mouse_position = MousePosition(
    position='topright',
    separator=' Long: ',
    empty_string='NaN',
    lng_first=False,
    num_digits=20,
    prefix='Lat:',
    lat_formatter=formatter,
    lng_formatter=formatter,
)

site_map.add_child(mouse_position)
site_map
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
                #map_d368eaa66911ac76cdc66071a54e7a4a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/leaflet.markercluster.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.Default.css&quot;/&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.css&quot;/&gt;
&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_d368eaa66911ac76cdc66071a54e7a4a = L.map(
                &quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 5,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_6a6dd63d3a16d33dbc7c6a5ce61a614f = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var circle_09e82ae54ea55eb70d02663e6d4fd807 = L.circle(
                [28.5618571, -80.577366],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_26200c74ecb8356af72e59c8de14bc0a = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_0b4d23af6deac36ab2a0e6ad9141e28a = $(`&lt;div id=&quot;html_0b4d23af6deac36ab2a0e6ad9141e28a&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;CCSFS SLC 40&lt;/div&gt;`)[0];
                popup_26200c74ecb8356af72e59c8de14bc0a.setContent(html_0b4d23af6deac36ab2a0e6ad9141e28a);



        circle_09e82ae54ea55eb70d02663e6d4fd807.bindPopup(popup_26200c74ecb8356af72e59c8de14bc0a)
        ;




            var marker_e171461b01d6b4700b99847473339ad4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_3f1d400bf8c012d2ef5764b75c6ab41a = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eCCSFS SLC 40\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_e171461b01d6b4700b99847473339ad4.setIcon(div_icon_3f1d400bf8c012d2ef5764b75c6ab41a);


            var circle_d98165e55558f9f5f11d0b869e76c55a = L.circle(
                [28.6080585, -80.6039558],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_777bb510b47559a11702bc169d967e63 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_6497d437dcb578c0f8c6bb7b5a3dbdd6 = $(`&lt;div id=&quot;html_6497d437dcb578c0f8c6bb7b5a3dbdd6&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;KSC LC 39A&lt;/div&gt;`)[0];
                popup_777bb510b47559a11702bc169d967e63.setContent(html_6497d437dcb578c0f8c6bb7b5a3dbdd6);



        circle_d98165e55558f9f5f11d0b869e76c55a.bindPopup(popup_777bb510b47559a11702bc169d967e63)
        ;




            var marker_f57fea6caa82c60177b0fd4e8ff11a09 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_33a1c124b806db61af88c3a05ba0a3a8 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eKSC LC 39A\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f57fea6caa82c60177b0fd4e8ff11a09.setIcon(div_icon_33a1c124b806db61af88c3a05ba0a3a8);


            var circle_836706b82952ca5ef3a3059d08a13d33 = L.circle(
                [34.632093, -120.610829],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_09f17b50bcfd8edb3350a1f00d21beb7 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_1281760bebe660c6f20743f8a44a4190 = $(`&lt;div id=&quot;html_1281760bebe660c6f20743f8a44a4190&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;VAFB SLC 4E&lt;/div&gt;`)[0];
                popup_09f17b50bcfd8edb3350a1f00d21beb7.setContent(html_1281760bebe660c6f20743f8a44a4190);



        circle_836706b82952ca5ef3a3059d08a13d33.bindPopup(popup_09f17b50bcfd8edb3350a1f00d21beb7)
        ;




            var marker_f20a64e42fb3657c81fecabae2eef414 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ed87eb3ea7b7542002106081d21adce7 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eVAFB SLC 4E\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f20a64e42fb3657c81fecabae2eef414.setIcon(div_icon_ed87eb3ea7b7542002106081d21adce7);


            var marker_cluster_f116777a8f89526d71ccfa29da685556 = L.markerClusterGroup(
                {}
            );
            map_d368eaa66911ac76cdc66071a54e7a4a.addLayer(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var marker_8e3fbc734492c596f285a59bbd2828eb = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_be99b870932b60b5a059abc5d3eea43b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8e3fbc734492c596f285a59bbd2828eb.setIcon(icon_be99b870932b60b5a059abc5d3eea43b);


            var marker_b265ccc5f3ff2f93e4a59d019a33539a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6ce6675244c5eec8cea2da0f8e52b4c3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b265ccc5f3ff2f93e4a59d019a33539a.setIcon(icon_6ce6675244c5eec8cea2da0f8e52b4c3);


            var marker_e7f8badc6b329032084deb57204bf78d = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_98be638d075e9c8b1753032889c91604 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e7f8badc6b329032084deb57204bf78d.setIcon(icon_98be638d075e9c8b1753032889c91604);


            var marker_9762b11ec5c9a7929169dd5226ab7a7a = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9762b11ec5c9a7929169dd5226ab7a7a.setIcon(icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c);


            var marker_05496be246e112b1586148e09ca8255b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_39d29742c4a629c987f9a863222ac384 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_05496be246e112b1586148e09ca8255b.setIcon(icon_39d29742c4a629c987f9a863222ac384);


            var marker_26b020c8440b964ed52b8233103e3807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0a8961b079f2e64d955495c92596aebe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_26b020c8440b964ed52b8233103e3807.setIcon(icon_0a8961b079f2e64d955495c92596aebe);


            var marker_0c55aa8394bec09019f5fe3bf99655fa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_bbb9ec9636836fd1c13ef05a9691e2af = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c55aa8394bec09019f5fe3bf99655fa.setIcon(icon_bbb9ec9636836fd1c13ef05a9691e2af);


            var marker_af5595c2b48dff9c4425acccf92fc5aa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3130e369bd0b97da036f4a7d8d5c916d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_af5595c2b48dff9c4425acccf92fc5aa.setIcon(icon_3130e369bd0b97da036f4a7d8d5c916d);


            var marker_2102710f13142fd96b2e8d017f15d45e = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3fb6ccb5eae44b18acb0b636f915d39a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2102710f13142fd96b2e8d017f15d45e.setIcon(icon_3fb6ccb5eae44b18acb0b636f915d39a);


            var marker_aaad09c638eba800a7811229eae101b1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d2a0ee20131bc6466a138caaca22529 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aaad09c638eba800a7811229eae101b1.setIcon(icon_0d2a0ee20131bc6466a138caaca22529);


            var marker_4683b9f2e56d38aa1acdf4ad7bbec9ec = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0494882a0773383ff6ab4f7b01c3855a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4683b9f2e56d38aa1acdf4ad7bbec9ec.setIcon(icon_0494882a0773383ff6ab4f7b01c3855a);


            var marker_2c87eb3a097f8f52b16674aceca0c4e0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_c03256aaa0fb0c018707714a45110c29 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c87eb3a097f8f52b16674aceca0c4e0.setIcon(icon_c03256aaa0fb0c018707714a45110c29);


            var marker_acd191db0be53bfd6645a49e1328dc14 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_10e4b136da16c6dae6f8747b86a009de = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_acd191db0be53bfd6645a49e1328dc14.setIcon(icon_10e4b136da16c6dae6f8747b86a009de);


            var marker_c90eed40cb69231d1ad1d5b1b14b2fd1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_4e3460ba0bea820a9b8811d9374d663b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c90eed40cb69231d1ad1d5b1b14b2fd1.setIcon(icon_4e3460ba0bea820a9b8811d9374d663b);


            var marker_280d50c0fecd2cf9a46560dbe6ef20f1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b94714aae52c32397682f3a3aa734555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_280d50c0fecd2cf9a46560dbe6ef20f1.setIcon(icon_b94714aae52c32397682f3a3aa734555);


            var marker_533be051d47af44f20479dde1ed40de0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1df4fc9edee2cae23a865eaded820bf8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_533be051d47af44f20479dde1ed40de0.setIcon(icon_1df4fc9edee2cae23a865eaded820bf8);


            var marker_e19ccf9d8957d95c236100fd2a48623c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e93489884b4c7553766caae417e1a5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e19ccf9d8957d95c236100fd2a48623c.setIcon(icon_e93489884b4c7553766caae417e1a5e6);


            var marker_0ee3df4535b0e25e208cec9a6d0d2c56 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b2d7914a31750a68d48b38d61d336ab9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ee3df4535b0e25e208cec9a6d0d2c56.setIcon(icon_b2d7914a31750a68d48b38d61d336ab9);


            var marker_510b2c2da7cea926b61216c9f4ad7f5b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3315d1234c29a44ed5b3d22c92c20526 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_510b2c2da7cea926b61216c9f4ad7f5b.setIcon(icon_3315d1234c29a44ed5b3d22c92c20526);


            var marker_0c94063a2c7c0b18d2c25fb20f610644 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fde389ae32dedeeeb62bbe811db695eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c94063a2c7c0b18d2c25fb20f610644.setIcon(icon_fde389ae32dedeeeb62bbe811db695eb);


            var marker_0ca96a4682816125e07dbbba2696044c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_47bac55069c2de5ef303c417a2a220eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ca96a4682816125e07dbbba2696044c.setIcon(icon_47bac55069c2de5ef303c417a2a220eb);


            var marker_a3a2d8d01b7de1145acf689ee5bacec8 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_83635d34593afb3ca36a337417c6d55a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a3a2d8d01b7de1145acf689ee5bacec8.setIcon(icon_83635d34593afb3ca36a337417c6d55a);


            var marker_8c2f1eaf61c7e33d6893c83f9ad04f0f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6063d0c719d73692bb69610728fdfa41 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8c2f1eaf61c7e33d6893c83f9ad04f0f.setIcon(icon_6063d0c719d73692bb69610728fdfa41);


            var marker_823751abb97e42969d8e1ab7bb1338c0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_147445c198f6902c97d5ddabb2cbbe6e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_823751abb97e42969d8e1ab7bb1338c0.setIcon(icon_147445c198f6902c97d5ddabb2cbbe6e);


            var marker_dd5006505c03160f51c570ec351bb2e2 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b98939d141ba86ae0b85d89229220c0a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dd5006505c03160f51c570ec351bb2e2.setIcon(icon_b98939d141ba86ae0b85d89229220c0a);


            var marker_8d451c8bbf3b65058c32bfd171379021 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f96f31fc348a21e9883ec56e022eb5d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d451c8bbf3b65058c32bfd171379021.setIcon(icon_1f96f31fc348a21e9883ec56e022eb5d);


            var marker_708fcdb0eb1bee81a0676714a97c4b7e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8d0d02face42693df9cc809b6b1b8b88 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_708fcdb0eb1bee81a0676714a97c4b7e.setIcon(icon_8d0d02face42693df9cc809b6b1b8b88);


            var marker_b20e0cffdc1dcabd8977b8743bfdc23f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e05584755ae63037f94b6629a8a59059 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b20e0cffdc1dcabd8977b8743bfdc23f.setIcon(icon_e05584755ae63037f94b6629a8a59059);


            var marker_12c5ae814402608fe9fcaa0fb953baa3 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_298559faf2923df091deb8a400f32a3a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_12c5ae814402608fe9fcaa0fb953baa3.setIcon(icon_298559faf2923df091deb8a400f32a3a);


            var marker_c7099d8bd405dca8c5d799d78046d989 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8ea03eccae8270a9959977a4e83ad81c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c7099d8bd405dca8c5d799d78046d989.setIcon(icon_8ea03eccae8270a9959977a4e83ad81c);


            var marker_2000a471ecc645745690b5601bf318b9 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_06a011613bb1dd586a10d8b689767a67 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2000a471ecc645745690b5601bf318b9.setIcon(icon_06a011613bb1dd586a10d8b689767a67);


            var marker_9add50083d8f937cdbf3268c42ea3458 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f1103989b51f2b44128d791224a47fb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9add50083d8f937cdbf3268c42ea3458.setIcon(icon_0f1103989b51f2b44128d791224a47fb);


            var marker_fc40396968e3dbf3f0ac5f4e4d2b5a25 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_143a907a9fb195f8a49fc5e047916e95 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fc40396968e3dbf3f0ac5f4e4d2b5a25.setIcon(icon_143a907a9fb195f8a49fc5e047916e95);


            var marker_0191d721bf323854bc15826cdfe155dc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b1888b082353ed7ee63f02be42251844 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0191d721bf323854bc15826cdfe155dc.setIcon(icon_b1888b082353ed7ee63f02be42251844);


            var marker_519c51aeb2798288434522e9ac6c87c1 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2d0385ab07b5db8469e705e50c436347 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_519c51aeb2798288434522e9ac6c87c1.setIcon(icon_2d0385ab07b5db8469e705e50c436347);


            var marker_0c386873db0e06685e966e18238cd440 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5aee252c188ed86e6db7603935c81304 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c386873db0e06685e966e18238cd440.setIcon(icon_5aee252c188ed86e6db7603935c81304);


            var marker_23ec039ad7d08965f7fe479f3ac68d16 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b8059df365b79a0aa6df0de16ac9b5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_23ec039ad7d08965f7fe479f3ac68d16.setIcon(icon_b8059df365b79a0aa6df0de16ac9b5e6);


            var marker_84633a4c74c61cb29d1203a61dd3c729 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8400de5dc2bd3806576abe981463b13f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_84633a4c74c61cb29d1203a61dd3c729.setIcon(icon_8400de5dc2bd3806576abe981463b13f);


            var marker_daf80663ebf4530b28f47594d7374072 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e9a05afd4d6a84c274fa4130eb78b10a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_daf80663ebf4530b28f47594d7374072.setIcon(icon_e9a05afd4d6a84c274fa4130eb78b10a);


            var marker_d6bedb7815d6f288e1d6e598e9baf08f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d6bedb7815d6f288e1d6e598e9baf08f.setIcon(icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6);


            var marker_2988ed8d1eb975d3e0289077e01561a6 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f8a0e6456d1d85ca04f373538d9dab31 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2988ed8d1eb975d3e0289077e01561a6.setIcon(icon_f8a0e6456d1d85ca04f373538d9dab31);


            var marker_ba02f01ffa1ca683ec3cd7b81f4eea0a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_97fcd29be95d04c61837ac90161f99ea = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ba02f01ffa1ca683ec3cd7b81f4eea0a.setIcon(icon_97fcd29be95d04c61837ac90161f99ea);


            var marker_bc822aae3c14f72a8da810e9a99b6bac = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fc9de103d81f6167149e720d5aa5aa2b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_bc822aae3c14f72a8da810e9a99b6bac.setIcon(icon_fc9de103d81f6167149e720d5aa5aa2b);


            var marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_838977de5280226a2bfa533389b76480 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20.setIcon(icon_838977de5280226a2bfa533389b76480);


            var marker_a52800e1677bceb4962f64c50390bb66 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f1dbbf3a3c33e7722e96b26b0e738ec6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a52800e1677bceb4962f64c50390bb66.setIcon(icon_f1dbbf3a3c33e7722e96b26b0e738ec6);


            var marker_7d1155bf736c8cdb6c4901fc51d8fd38 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_ef825d97216fbe7e868ca772588987d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7d1155bf736c8cdb6c4901fc51d8fd38.setIcon(icon_ef825d97216fbe7e868ca772588987d8);


            var marker_ca923c0327d25870f976c7f15784b537 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1f2b83e998d1e4caf4a9d56832f4743 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ca923c0327d25870f976c7f15784b537.setIcon(icon_a1f2b83e998d1e4caf4a9d56832f4743);


            var marker_ff790ea4e51733a9a60bccb92e9abafc = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2ab4348c668a55fc9294e5960858a828 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff790ea4e51733a9a60bccb92e9abafc.setIcon(icon_2ab4348c668a55fc9294e5960858a828);


            var marker_dac712da8151aa914d0d499e9c23fae5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_706d6845d3884cf2f2193927191f7205 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dac712da8151aa914d0d499e9c23fae5.setIcon(icon_706d6845d3884cf2f2193927191f7205);


            var marker_59d64bd4daa0e4c76bc0b7e96db776a5 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_71f216c61addb87f1116e9c737ad2a58 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_59d64bd4daa0e4c76bc0b7e96db776a5.setIcon(icon_71f216c61addb87f1116e9c737ad2a58);


            var marker_1ed241d8a0b5227618f2c00123a45520 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f6e90640e75add03cf3655d777cf298 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_1ed241d8a0b5227618f2c00123a45520.setIcon(icon_1f6e90640e75add03cf3655d777cf298);


            var marker_0fe5cf43ec2696ab17ee89435c81fa59 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7def62d3317a8bc6c77433d3754e21f1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0fe5cf43ec2696ab17ee89435c81fa59.setIcon(icon_7def62d3317a8bc6c77433d3754e21f1);


            var marker_6bb1d8ff9f26c26136f7a8d3130c7795 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_db713ff653ba3f3538a3e5cf442bee18 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6bb1d8ff9f26c26136f7a8d3130c7795.setIcon(icon_db713ff653ba3f3538a3e5cf442bee18);


            var marker_40ad47531ee3347bc2b8307c3d482f84 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_056f353f0ab0f3940d0edeb5a62d9450 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_40ad47531ee3347bc2b8307c3d482f84.setIcon(icon_056f353f0ab0f3940d0edeb5a62d9450);


            var marker_2f5e28ca54928223f465ad1e9b2d1a2c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5fe6f90fe798a441c8ef45f7d3ca4a83 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2f5e28ca54928223f465ad1e9b2d1a2c.setIcon(icon_5fe6f90fe798a441c8ef45f7d3ca4a83);


            var marker_9aa6387fdb95f9185c705393980f5d3f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_603c180022e818d583932c7ef1743ed2 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9aa6387fdb95f9185c705393980f5d3f.setIcon(icon_603c180022e818d583932c7ef1743ed2);


            var marker_03999bb96d185e87146526d6aad223da = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_59fdaca20197a09206d1af4a2af7a6ff = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_03999bb96d185e87146526d6aad223da.setIcon(icon_59fdaca20197a09206d1af4a2af7a6ff);


            var marker_9579df7e2872157b05112a0a74207fff = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b5fe47b713a39ecd2c5016e4837d8f21 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9579df7e2872157b05112a0a74207fff.setIcon(icon_b5fe47b713a39ecd2c5016e4837d8f21);


            var marker_fe58589b434072a96e9ca818337e301e = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_22ef0459c3d7134064b56333a32c3a34 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fe58589b434072a96e9ca818337e301e.setIcon(icon_22ef0459c3d7134064b56333a32c3a34);


            var marker_2c2708b05946fddc4cb79d1f570c0411 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f227efc1cf97295470efc9ff7a5250ee = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c2708b05946fddc4cb79d1f570c0411.setIcon(icon_f227efc1cf97295470efc9ff7a5250ee);


            var marker_8dfdc79822d8532d024e26c0533b3528 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e214056aead57dde15d727a608736dbc = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8dfdc79822d8532d024e26c0533b3528.setIcon(icon_e214056aead57dde15d727a608736dbc);


            var marker_65762ab5b76ba27e40d107ea6d5662fc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6fa2242edc8c567ce471f952aadcff4f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_65762ab5b76ba27e40d107ea6d5662fc.setIcon(icon_6fa2242edc8c567ce471f952aadcff4f);


            var marker_b2f4dffaa3b8995e46edc01b5e231294 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_745981901199b63d593decaaf610049d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b2f4dffaa3b8995e46edc01b5e231294.setIcon(icon_745981901199b63d593decaaf610049d);


            var marker_a9308cfef5cd0c0e7a6434475f790736 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d908ab4d9700ce83efee476e886b97c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a9308cfef5cd0c0e7a6434475f790736.setIcon(icon_0d908ab4d9700ce83efee476e886b97c);


            var marker_d0cb20b9c2fbabc28c8bd6667c4d20f4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_01f7752eabf64318413fd39cb59b0d16 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d0cb20b9c2fbabc28c8bd6667c4d20f4.setIcon(icon_01f7752eabf64318413fd39cb59b0d16);


            var marker_37ff74361f6f1ed036dde3dc72a710d4 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6bfa6cc0bd3deb7ee1305998105d067c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_37ff74361f6f1ed036dde3dc72a710d4.setIcon(icon_6bfa6cc0bd3deb7ee1305998105d067c);


            var marker_348bd81104c9276a374aeee3bf631025 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5450a9a60d82142d1bde1659c90a9a6f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_348bd81104c9276a374aeee3bf631025.setIcon(icon_5450a9a60d82142d1bde1659c90a9a6f);


            var marker_656aa8c496ce988aa58b4365bae3cb48 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_32b90f4bd7cd6a319396722faff2939e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_656aa8c496ce988aa58b4365bae3cb48.setIcon(icon_32b90f4bd7cd6a319396722faff2939e);


            var marker_b880452991c33e3f8f68f6cb4d363db9 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1aaa4c50431786cd7394f328d450b0c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b880452991c33e3f8f68f6cb4d363db9.setIcon(icon_a1aaa4c50431786cd7394f328d450b0c);


            var marker_5c48f11769ffa2eafd0f12a25ca369de = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5599a959cd465cd28701318805cb19d1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5c48f11769ffa2eafd0f12a25ca369de.setIcon(icon_5599a959cd465cd28701318805cb19d1);


            var marker_f48cc39051e69a5069cbc8fb6f789f89 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_48b719408e4dce1f37c15725ffff8c1b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_f48cc39051e69a5069cbc8fb6f789f89.setIcon(icon_48b719408e4dce1f37c15725ffff8c1b);


            var marker_df2855f0ce1391655b901800e790a7b5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e71d9950cadbbc68538039b6a52b501 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_df2855f0ce1391655b901800e790a7b5.setIcon(icon_8e71d9950cadbbc68538039b6a52b501);


            var marker_cf7cac7da28106a1abbc95f56c8cad2f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e8913d5711160f297a4ed477f016f5d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cf7cac7da28106a1abbc95f56c8cad2f.setIcon(icon_e8913d5711160f297a4ed477f016f5d8);


            var marker_49aac07dc6d5f089b3f54172a7567169 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e5dcbd4cfc8bcfabd0f03262f16434c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_49aac07dc6d5f089b3f54172a7567169.setIcon(icon_8e5dcbd4cfc8bcfabd0f03262f16434c);


            var marker_efd69645b1c9090161b21ad65eb9da28 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_40cd0958be52c8dc2f6ebe70e1860f98 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_efd69645b1c9090161b21ad65eb9da28.setIcon(icon_40cd0958be52c8dc2f6ebe70e1860f98);


            var marker_ff3388e2d1de09848799c111c1d77c75 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_cdd9837aa5d8b7d9e4b337c560b75293 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff3388e2d1de09848799c111c1d77c75.setIcon(icon_cdd9837aa5d8b7d9e4b337c560b75293);


            var marker_541ba9984e25e0505a1ba82f62b55260 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f32393a6937917b2179d8524c8d64631 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_541ba9984e25e0505a1ba82f62b55260.setIcon(icon_f32393a6937917b2179d8524c8d64631);


            var marker_b4cff47336487516d8fd112d0fd46397 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e92e21cfdb36cedfc2d6fd7134c3a3a5 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b4cff47336487516d8fd112d0fd46397.setIcon(icon_e92e21cfdb36cedfc2d6fd7134c3a3a5);


            var marker_51f0e797a0669d5c678d7fe0f11a138e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7a6e41bff251b2c74a5729b43ea93875 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_51f0e797a0669d5c678d7fe0f11a138e.setIcon(icon_7a6e41bff251b2c74a5729b43ea93875);


            var marker_5225739e6f8da47c94b201312b4d50f0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a21b7dfed11f7d1b1b1a58585fca21b0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5225739e6f8da47c94b201312b4d50f0.setIcon(icon_a21b7dfed11f7d1b1b1a58585fca21b0);


            var marker_2935d90ec3ba45cd2543ef9a64eaeecf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5d334919024cba9d301f5a4679399559 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2935d90ec3ba45cd2543ef9a64eaeecf.setIcon(icon_5d334919024cba9d301f5a4679399559);


            var marker_ef2fa80060c883e930db51a7fb2399cf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f630b606af0150713437f16c0eba879 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ef2fa80060c883e930db51a7fb2399cf.setIcon(icon_0f630b606af0150713437f16c0eba879);


            var marker_95a789ff1c1867efc53540af8910ccdd = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e0c97543bf8755ade1f6249efd380bb9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_95a789ff1c1867efc53540af8910ccdd.setIcon(icon_e0c97543bf8755ade1f6249efd380bb9);


            var marker_3f077b03e537d128163b5c86927feadf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_030810f0e1972c7cbbfd2765fa19299c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_3f077b03e537d128163b5c86927feadf.setIcon(icon_030810f0e1972c7cbbfd2765fa19299c);


            var marker_8d8d8576bfb12f4c3f0ac3750d18e505 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d8d8576bfb12f4c3f0ac3750d18e505.setIcon(icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0);


            var marker_4968ba53f5d12369310ccf9994be42b8 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91a4b514ce940d93d1511ae5ebb60c47 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4968ba53f5d12369310ccf9994be42b8.setIcon(icon_91a4b514ce940d93d1511ae5ebb60c47);


            var marker_edf1b4d1c86b086042fdb78a6a50309c = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1add5061d056faaf5a3e799ba9ae555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_edf1b4d1c86b086042fdb78a6a50309c.setIcon(icon_a1add5061d056faaf5a3e799ba9ae555);


            var marker_aec1ac925773a73c70fb629ce7d03ad2 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91148a541330e5590009d961d5206cfe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aec1ac925773a73c70fb629ce7d03ad2.setIcon(icon_91148a541330e5590009d961d5206cfe);


            var marker_0b2d624e4a25e562d789b120d4f279da = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_559e6106fc82969e429355fd2bd8dfe8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0b2d624e4a25e562d789b120d4f279da.setIcon(icon_559e6106fc82969e429355fd2bd8dfe8);


            var marker_261c349094a575034ead3af7f7919807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_70ceb3c0a910779f13a144b4550f3670 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_261c349094a575034ead3af7f7919807.setIcon(icon_70ceb3c0a910779f13a144b4550f3670);


            var mouse_position_578c12ce1a7601adb7ba34cc116e3a90 = new L.Control.MousePosition(
                {&quot;emptyString&quot;: &quot;NaN&quot;, &quot;lngFirst&quot;: false, &quot;numDigits&quot;: 20, &quot;position&quot;: &quot;topright&quot;, &quot;prefix&quot;: &quot;Lat:&quot;, &quot;separator&quot;: &quot; Long: &quot;}
            );
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;latFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;lngFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            map_d368eaa66911ac76cdc66071a54e7a4a.addControl(mouse_position_578c12ce1a7601adb7ba34cc116e3a90);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



Now zoom in to a launch site and explore its proximity to see if you can easily find any railway, highway, coastline, etc. Move your mouse to these points and mark down their coordinates (shown on the top-left) in order to the distance to the launch site.


You can calculate the distance between two points on the map based on their `Lat` and `Long` values using the following method:



```python
from math import sin, cos, sqrt, atan2, radians

def calculate_distance(lat1, lon1, lat2, lon2):
    # approximate radius of earth in km
    R = 6373.0

    lat1 = radians(lat1)
    lon1 = radians(lon1)
    lat2 = radians(lat2)
    lon2 = radians(lon2)

    dlon = lon2 - lon1
    dlat = lat2 - lat1

    a = sin(dlat / 2)**2 + cos(lat1) * cos(lat2) * sin(dlon / 2)**2
    c = 2 * atan2(sqrt(a), sqrt(1 - a))

    distance = R * c
    return distance
```

*TODO:* Mark down a point on the closest coastline using MousePosition and calculate the distance between the coastline point and the launch site.



```python
# find coordinate of the closet coastline
# e.g.,: Lat: 28.56367  Lon: -80.57163
launch_site_lat = launch_sites_df.iloc[2]["Latitude"]
launch_site_lon = launch_sites_df.iloc[2]["Longitude"]
lat_coastline = 34.63617
long_coastline = -120.62503
distance_coastline = calculate_distance(launch_site_lat, launch_site_lon, lat_coastline, long_coastline)
print(distance_coastline)
print(launch_site_lat)
print(launch_site_lon)
```

    1.3765145755131973
    34.632093
    -120.610829


*TODO:* After obtained its coordinate, create a `folium.Marker` to show the distance



```python
# Create and add a folium.Marker on your selected closest coastline point on the map
# Display the distance between coastline point and launch site using the icon property 
# for example
coordinate = [lat_coastline, long_coastline]

distance_coastline_marker = folium.Marker(
    coordinate,
    icon=DivIcon(
        icon_size=(20,20),
        icon_anchor=(0,0),
        html='<div style="font-size: 12; color:#d35400;"><b>%s</b></div>' % "{:10.2f} KM".format(distance_coastline),
        )
    )
site_map.add_child(distance_coastline_marker)
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
                #map_d368eaa66911ac76cdc66071a54e7a4a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/leaflet.markercluster.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.Default.css&quot;/&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.css&quot;/&gt;
&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_d368eaa66911ac76cdc66071a54e7a4a = L.map(
                &quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 5,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_6a6dd63d3a16d33dbc7c6a5ce61a614f = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var circle_09e82ae54ea55eb70d02663e6d4fd807 = L.circle(
                [28.5618571, -80.577366],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_26200c74ecb8356af72e59c8de14bc0a = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_0b4d23af6deac36ab2a0e6ad9141e28a = $(`&lt;div id=&quot;html_0b4d23af6deac36ab2a0e6ad9141e28a&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;CCSFS SLC 40&lt;/div&gt;`)[0];
                popup_26200c74ecb8356af72e59c8de14bc0a.setContent(html_0b4d23af6deac36ab2a0e6ad9141e28a);



        circle_09e82ae54ea55eb70d02663e6d4fd807.bindPopup(popup_26200c74ecb8356af72e59c8de14bc0a)
        ;




            var marker_e171461b01d6b4700b99847473339ad4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_3f1d400bf8c012d2ef5764b75c6ab41a = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eCCSFS SLC 40\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_e171461b01d6b4700b99847473339ad4.setIcon(div_icon_3f1d400bf8c012d2ef5764b75c6ab41a);


            var circle_d98165e55558f9f5f11d0b869e76c55a = L.circle(
                [28.6080585, -80.6039558],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_777bb510b47559a11702bc169d967e63 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_6497d437dcb578c0f8c6bb7b5a3dbdd6 = $(`&lt;div id=&quot;html_6497d437dcb578c0f8c6bb7b5a3dbdd6&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;KSC LC 39A&lt;/div&gt;`)[0];
                popup_777bb510b47559a11702bc169d967e63.setContent(html_6497d437dcb578c0f8c6bb7b5a3dbdd6);



        circle_d98165e55558f9f5f11d0b869e76c55a.bindPopup(popup_777bb510b47559a11702bc169d967e63)
        ;




            var marker_f57fea6caa82c60177b0fd4e8ff11a09 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_33a1c124b806db61af88c3a05ba0a3a8 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eKSC LC 39A\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f57fea6caa82c60177b0fd4e8ff11a09.setIcon(div_icon_33a1c124b806db61af88c3a05ba0a3a8);


            var circle_836706b82952ca5ef3a3059d08a13d33 = L.circle(
                [34.632093, -120.610829],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_09f17b50bcfd8edb3350a1f00d21beb7 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_1281760bebe660c6f20743f8a44a4190 = $(`&lt;div id=&quot;html_1281760bebe660c6f20743f8a44a4190&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;VAFB SLC 4E&lt;/div&gt;`)[0];
                popup_09f17b50bcfd8edb3350a1f00d21beb7.setContent(html_1281760bebe660c6f20743f8a44a4190);



        circle_836706b82952ca5ef3a3059d08a13d33.bindPopup(popup_09f17b50bcfd8edb3350a1f00d21beb7)
        ;




            var marker_f20a64e42fb3657c81fecabae2eef414 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ed87eb3ea7b7542002106081d21adce7 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eVAFB SLC 4E\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f20a64e42fb3657c81fecabae2eef414.setIcon(div_icon_ed87eb3ea7b7542002106081d21adce7);


            var marker_cluster_f116777a8f89526d71ccfa29da685556 = L.markerClusterGroup(
                {}
            );
            map_d368eaa66911ac76cdc66071a54e7a4a.addLayer(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var marker_8e3fbc734492c596f285a59bbd2828eb = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_be99b870932b60b5a059abc5d3eea43b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8e3fbc734492c596f285a59bbd2828eb.setIcon(icon_be99b870932b60b5a059abc5d3eea43b);


            var marker_b265ccc5f3ff2f93e4a59d019a33539a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6ce6675244c5eec8cea2da0f8e52b4c3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b265ccc5f3ff2f93e4a59d019a33539a.setIcon(icon_6ce6675244c5eec8cea2da0f8e52b4c3);


            var marker_e7f8badc6b329032084deb57204bf78d = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_98be638d075e9c8b1753032889c91604 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e7f8badc6b329032084deb57204bf78d.setIcon(icon_98be638d075e9c8b1753032889c91604);


            var marker_9762b11ec5c9a7929169dd5226ab7a7a = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9762b11ec5c9a7929169dd5226ab7a7a.setIcon(icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c);


            var marker_05496be246e112b1586148e09ca8255b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_39d29742c4a629c987f9a863222ac384 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_05496be246e112b1586148e09ca8255b.setIcon(icon_39d29742c4a629c987f9a863222ac384);


            var marker_26b020c8440b964ed52b8233103e3807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0a8961b079f2e64d955495c92596aebe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_26b020c8440b964ed52b8233103e3807.setIcon(icon_0a8961b079f2e64d955495c92596aebe);


            var marker_0c55aa8394bec09019f5fe3bf99655fa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_bbb9ec9636836fd1c13ef05a9691e2af = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c55aa8394bec09019f5fe3bf99655fa.setIcon(icon_bbb9ec9636836fd1c13ef05a9691e2af);


            var marker_af5595c2b48dff9c4425acccf92fc5aa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3130e369bd0b97da036f4a7d8d5c916d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_af5595c2b48dff9c4425acccf92fc5aa.setIcon(icon_3130e369bd0b97da036f4a7d8d5c916d);


            var marker_2102710f13142fd96b2e8d017f15d45e = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3fb6ccb5eae44b18acb0b636f915d39a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2102710f13142fd96b2e8d017f15d45e.setIcon(icon_3fb6ccb5eae44b18acb0b636f915d39a);


            var marker_aaad09c638eba800a7811229eae101b1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d2a0ee20131bc6466a138caaca22529 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aaad09c638eba800a7811229eae101b1.setIcon(icon_0d2a0ee20131bc6466a138caaca22529);


            var marker_4683b9f2e56d38aa1acdf4ad7bbec9ec = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0494882a0773383ff6ab4f7b01c3855a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4683b9f2e56d38aa1acdf4ad7bbec9ec.setIcon(icon_0494882a0773383ff6ab4f7b01c3855a);


            var marker_2c87eb3a097f8f52b16674aceca0c4e0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_c03256aaa0fb0c018707714a45110c29 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c87eb3a097f8f52b16674aceca0c4e0.setIcon(icon_c03256aaa0fb0c018707714a45110c29);


            var marker_acd191db0be53bfd6645a49e1328dc14 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_10e4b136da16c6dae6f8747b86a009de = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_acd191db0be53bfd6645a49e1328dc14.setIcon(icon_10e4b136da16c6dae6f8747b86a009de);


            var marker_c90eed40cb69231d1ad1d5b1b14b2fd1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_4e3460ba0bea820a9b8811d9374d663b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c90eed40cb69231d1ad1d5b1b14b2fd1.setIcon(icon_4e3460ba0bea820a9b8811d9374d663b);


            var marker_280d50c0fecd2cf9a46560dbe6ef20f1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b94714aae52c32397682f3a3aa734555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_280d50c0fecd2cf9a46560dbe6ef20f1.setIcon(icon_b94714aae52c32397682f3a3aa734555);


            var marker_533be051d47af44f20479dde1ed40de0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1df4fc9edee2cae23a865eaded820bf8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_533be051d47af44f20479dde1ed40de0.setIcon(icon_1df4fc9edee2cae23a865eaded820bf8);


            var marker_e19ccf9d8957d95c236100fd2a48623c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e93489884b4c7553766caae417e1a5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e19ccf9d8957d95c236100fd2a48623c.setIcon(icon_e93489884b4c7553766caae417e1a5e6);


            var marker_0ee3df4535b0e25e208cec9a6d0d2c56 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b2d7914a31750a68d48b38d61d336ab9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ee3df4535b0e25e208cec9a6d0d2c56.setIcon(icon_b2d7914a31750a68d48b38d61d336ab9);


            var marker_510b2c2da7cea926b61216c9f4ad7f5b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3315d1234c29a44ed5b3d22c92c20526 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_510b2c2da7cea926b61216c9f4ad7f5b.setIcon(icon_3315d1234c29a44ed5b3d22c92c20526);


            var marker_0c94063a2c7c0b18d2c25fb20f610644 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fde389ae32dedeeeb62bbe811db695eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c94063a2c7c0b18d2c25fb20f610644.setIcon(icon_fde389ae32dedeeeb62bbe811db695eb);


            var marker_0ca96a4682816125e07dbbba2696044c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_47bac55069c2de5ef303c417a2a220eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ca96a4682816125e07dbbba2696044c.setIcon(icon_47bac55069c2de5ef303c417a2a220eb);


            var marker_a3a2d8d01b7de1145acf689ee5bacec8 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_83635d34593afb3ca36a337417c6d55a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a3a2d8d01b7de1145acf689ee5bacec8.setIcon(icon_83635d34593afb3ca36a337417c6d55a);


            var marker_8c2f1eaf61c7e33d6893c83f9ad04f0f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6063d0c719d73692bb69610728fdfa41 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8c2f1eaf61c7e33d6893c83f9ad04f0f.setIcon(icon_6063d0c719d73692bb69610728fdfa41);


            var marker_823751abb97e42969d8e1ab7bb1338c0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_147445c198f6902c97d5ddabb2cbbe6e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_823751abb97e42969d8e1ab7bb1338c0.setIcon(icon_147445c198f6902c97d5ddabb2cbbe6e);


            var marker_dd5006505c03160f51c570ec351bb2e2 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b98939d141ba86ae0b85d89229220c0a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dd5006505c03160f51c570ec351bb2e2.setIcon(icon_b98939d141ba86ae0b85d89229220c0a);


            var marker_8d451c8bbf3b65058c32bfd171379021 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f96f31fc348a21e9883ec56e022eb5d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d451c8bbf3b65058c32bfd171379021.setIcon(icon_1f96f31fc348a21e9883ec56e022eb5d);


            var marker_708fcdb0eb1bee81a0676714a97c4b7e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8d0d02face42693df9cc809b6b1b8b88 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_708fcdb0eb1bee81a0676714a97c4b7e.setIcon(icon_8d0d02face42693df9cc809b6b1b8b88);


            var marker_b20e0cffdc1dcabd8977b8743bfdc23f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e05584755ae63037f94b6629a8a59059 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b20e0cffdc1dcabd8977b8743bfdc23f.setIcon(icon_e05584755ae63037f94b6629a8a59059);


            var marker_12c5ae814402608fe9fcaa0fb953baa3 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_298559faf2923df091deb8a400f32a3a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_12c5ae814402608fe9fcaa0fb953baa3.setIcon(icon_298559faf2923df091deb8a400f32a3a);


            var marker_c7099d8bd405dca8c5d799d78046d989 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8ea03eccae8270a9959977a4e83ad81c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c7099d8bd405dca8c5d799d78046d989.setIcon(icon_8ea03eccae8270a9959977a4e83ad81c);


            var marker_2000a471ecc645745690b5601bf318b9 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_06a011613bb1dd586a10d8b689767a67 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2000a471ecc645745690b5601bf318b9.setIcon(icon_06a011613bb1dd586a10d8b689767a67);


            var marker_9add50083d8f937cdbf3268c42ea3458 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f1103989b51f2b44128d791224a47fb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9add50083d8f937cdbf3268c42ea3458.setIcon(icon_0f1103989b51f2b44128d791224a47fb);


            var marker_fc40396968e3dbf3f0ac5f4e4d2b5a25 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_143a907a9fb195f8a49fc5e047916e95 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fc40396968e3dbf3f0ac5f4e4d2b5a25.setIcon(icon_143a907a9fb195f8a49fc5e047916e95);


            var marker_0191d721bf323854bc15826cdfe155dc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b1888b082353ed7ee63f02be42251844 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0191d721bf323854bc15826cdfe155dc.setIcon(icon_b1888b082353ed7ee63f02be42251844);


            var marker_519c51aeb2798288434522e9ac6c87c1 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2d0385ab07b5db8469e705e50c436347 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_519c51aeb2798288434522e9ac6c87c1.setIcon(icon_2d0385ab07b5db8469e705e50c436347);


            var marker_0c386873db0e06685e966e18238cd440 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5aee252c188ed86e6db7603935c81304 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c386873db0e06685e966e18238cd440.setIcon(icon_5aee252c188ed86e6db7603935c81304);


            var marker_23ec039ad7d08965f7fe479f3ac68d16 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b8059df365b79a0aa6df0de16ac9b5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_23ec039ad7d08965f7fe479f3ac68d16.setIcon(icon_b8059df365b79a0aa6df0de16ac9b5e6);


            var marker_84633a4c74c61cb29d1203a61dd3c729 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8400de5dc2bd3806576abe981463b13f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_84633a4c74c61cb29d1203a61dd3c729.setIcon(icon_8400de5dc2bd3806576abe981463b13f);


            var marker_daf80663ebf4530b28f47594d7374072 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e9a05afd4d6a84c274fa4130eb78b10a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_daf80663ebf4530b28f47594d7374072.setIcon(icon_e9a05afd4d6a84c274fa4130eb78b10a);


            var marker_d6bedb7815d6f288e1d6e598e9baf08f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d6bedb7815d6f288e1d6e598e9baf08f.setIcon(icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6);


            var marker_2988ed8d1eb975d3e0289077e01561a6 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f8a0e6456d1d85ca04f373538d9dab31 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2988ed8d1eb975d3e0289077e01561a6.setIcon(icon_f8a0e6456d1d85ca04f373538d9dab31);


            var marker_ba02f01ffa1ca683ec3cd7b81f4eea0a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_97fcd29be95d04c61837ac90161f99ea = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ba02f01ffa1ca683ec3cd7b81f4eea0a.setIcon(icon_97fcd29be95d04c61837ac90161f99ea);


            var marker_bc822aae3c14f72a8da810e9a99b6bac = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fc9de103d81f6167149e720d5aa5aa2b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_bc822aae3c14f72a8da810e9a99b6bac.setIcon(icon_fc9de103d81f6167149e720d5aa5aa2b);


            var marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_838977de5280226a2bfa533389b76480 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20.setIcon(icon_838977de5280226a2bfa533389b76480);


            var marker_a52800e1677bceb4962f64c50390bb66 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f1dbbf3a3c33e7722e96b26b0e738ec6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a52800e1677bceb4962f64c50390bb66.setIcon(icon_f1dbbf3a3c33e7722e96b26b0e738ec6);


            var marker_7d1155bf736c8cdb6c4901fc51d8fd38 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_ef825d97216fbe7e868ca772588987d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7d1155bf736c8cdb6c4901fc51d8fd38.setIcon(icon_ef825d97216fbe7e868ca772588987d8);


            var marker_ca923c0327d25870f976c7f15784b537 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1f2b83e998d1e4caf4a9d56832f4743 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ca923c0327d25870f976c7f15784b537.setIcon(icon_a1f2b83e998d1e4caf4a9d56832f4743);


            var marker_ff790ea4e51733a9a60bccb92e9abafc = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2ab4348c668a55fc9294e5960858a828 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff790ea4e51733a9a60bccb92e9abafc.setIcon(icon_2ab4348c668a55fc9294e5960858a828);


            var marker_dac712da8151aa914d0d499e9c23fae5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_706d6845d3884cf2f2193927191f7205 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dac712da8151aa914d0d499e9c23fae5.setIcon(icon_706d6845d3884cf2f2193927191f7205);


            var marker_59d64bd4daa0e4c76bc0b7e96db776a5 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_71f216c61addb87f1116e9c737ad2a58 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_59d64bd4daa0e4c76bc0b7e96db776a5.setIcon(icon_71f216c61addb87f1116e9c737ad2a58);


            var marker_1ed241d8a0b5227618f2c00123a45520 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f6e90640e75add03cf3655d777cf298 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_1ed241d8a0b5227618f2c00123a45520.setIcon(icon_1f6e90640e75add03cf3655d777cf298);


            var marker_0fe5cf43ec2696ab17ee89435c81fa59 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7def62d3317a8bc6c77433d3754e21f1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0fe5cf43ec2696ab17ee89435c81fa59.setIcon(icon_7def62d3317a8bc6c77433d3754e21f1);


            var marker_6bb1d8ff9f26c26136f7a8d3130c7795 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_db713ff653ba3f3538a3e5cf442bee18 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6bb1d8ff9f26c26136f7a8d3130c7795.setIcon(icon_db713ff653ba3f3538a3e5cf442bee18);


            var marker_40ad47531ee3347bc2b8307c3d482f84 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_056f353f0ab0f3940d0edeb5a62d9450 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_40ad47531ee3347bc2b8307c3d482f84.setIcon(icon_056f353f0ab0f3940d0edeb5a62d9450);


            var marker_2f5e28ca54928223f465ad1e9b2d1a2c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5fe6f90fe798a441c8ef45f7d3ca4a83 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2f5e28ca54928223f465ad1e9b2d1a2c.setIcon(icon_5fe6f90fe798a441c8ef45f7d3ca4a83);


            var marker_9aa6387fdb95f9185c705393980f5d3f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_603c180022e818d583932c7ef1743ed2 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9aa6387fdb95f9185c705393980f5d3f.setIcon(icon_603c180022e818d583932c7ef1743ed2);


            var marker_03999bb96d185e87146526d6aad223da = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_59fdaca20197a09206d1af4a2af7a6ff = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_03999bb96d185e87146526d6aad223da.setIcon(icon_59fdaca20197a09206d1af4a2af7a6ff);


            var marker_9579df7e2872157b05112a0a74207fff = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b5fe47b713a39ecd2c5016e4837d8f21 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9579df7e2872157b05112a0a74207fff.setIcon(icon_b5fe47b713a39ecd2c5016e4837d8f21);


            var marker_fe58589b434072a96e9ca818337e301e = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_22ef0459c3d7134064b56333a32c3a34 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fe58589b434072a96e9ca818337e301e.setIcon(icon_22ef0459c3d7134064b56333a32c3a34);


            var marker_2c2708b05946fddc4cb79d1f570c0411 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f227efc1cf97295470efc9ff7a5250ee = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c2708b05946fddc4cb79d1f570c0411.setIcon(icon_f227efc1cf97295470efc9ff7a5250ee);


            var marker_8dfdc79822d8532d024e26c0533b3528 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e214056aead57dde15d727a608736dbc = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8dfdc79822d8532d024e26c0533b3528.setIcon(icon_e214056aead57dde15d727a608736dbc);


            var marker_65762ab5b76ba27e40d107ea6d5662fc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6fa2242edc8c567ce471f952aadcff4f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_65762ab5b76ba27e40d107ea6d5662fc.setIcon(icon_6fa2242edc8c567ce471f952aadcff4f);


            var marker_b2f4dffaa3b8995e46edc01b5e231294 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_745981901199b63d593decaaf610049d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b2f4dffaa3b8995e46edc01b5e231294.setIcon(icon_745981901199b63d593decaaf610049d);


            var marker_a9308cfef5cd0c0e7a6434475f790736 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d908ab4d9700ce83efee476e886b97c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a9308cfef5cd0c0e7a6434475f790736.setIcon(icon_0d908ab4d9700ce83efee476e886b97c);


            var marker_d0cb20b9c2fbabc28c8bd6667c4d20f4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_01f7752eabf64318413fd39cb59b0d16 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d0cb20b9c2fbabc28c8bd6667c4d20f4.setIcon(icon_01f7752eabf64318413fd39cb59b0d16);


            var marker_37ff74361f6f1ed036dde3dc72a710d4 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6bfa6cc0bd3deb7ee1305998105d067c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_37ff74361f6f1ed036dde3dc72a710d4.setIcon(icon_6bfa6cc0bd3deb7ee1305998105d067c);


            var marker_348bd81104c9276a374aeee3bf631025 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5450a9a60d82142d1bde1659c90a9a6f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_348bd81104c9276a374aeee3bf631025.setIcon(icon_5450a9a60d82142d1bde1659c90a9a6f);


            var marker_656aa8c496ce988aa58b4365bae3cb48 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_32b90f4bd7cd6a319396722faff2939e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_656aa8c496ce988aa58b4365bae3cb48.setIcon(icon_32b90f4bd7cd6a319396722faff2939e);


            var marker_b880452991c33e3f8f68f6cb4d363db9 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1aaa4c50431786cd7394f328d450b0c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b880452991c33e3f8f68f6cb4d363db9.setIcon(icon_a1aaa4c50431786cd7394f328d450b0c);


            var marker_5c48f11769ffa2eafd0f12a25ca369de = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5599a959cd465cd28701318805cb19d1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5c48f11769ffa2eafd0f12a25ca369de.setIcon(icon_5599a959cd465cd28701318805cb19d1);


            var marker_f48cc39051e69a5069cbc8fb6f789f89 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_48b719408e4dce1f37c15725ffff8c1b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_f48cc39051e69a5069cbc8fb6f789f89.setIcon(icon_48b719408e4dce1f37c15725ffff8c1b);


            var marker_df2855f0ce1391655b901800e790a7b5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e71d9950cadbbc68538039b6a52b501 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_df2855f0ce1391655b901800e790a7b5.setIcon(icon_8e71d9950cadbbc68538039b6a52b501);


            var marker_cf7cac7da28106a1abbc95f56c8cad2f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e8913d5711160f297a4ed477f016f5d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cf7cac7da28106a1abbc95f56c8cad2f.setIcon(icon_e8913d5711160f297a4ed477f016f5d8);


            var marker_49aac07dc6d5f089b3f54172a7567169 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e5dcbd4cfc8bcfabd0f03262f16434c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_49aac07dc6d5f089b3f54172a7567169.setIcon(icon_8e5dcbd4cfc8bcfabd0f03262f16434c);


            var marker_efd69645b1c9090161b21ad65eb9da28 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_40cd0958be52c8dc2f6ebe70e1860f98 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_efd69645b1c9090161b21ad65eb9da28.setIcon(icon_40cd0958be52c8dc2f6ebe70e1860f98);


            var marker_ff3388e2d1de09848799c111c1d77c75 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_cdd9837aa5d8b7d9e4b337c560b75293 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff3388e2d1de09848799c111c1d77c75.setIcon(icon_cdd9837aa5d8b7d9e4b337c560b75293);


            var marker_541ba9984e25e0505a1ba82f62b55260 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f32393a6937917b2179d8524c8d64631 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_541ba9984e25e0505a1ba82f62b55260.setIcon(icon_f32393a6937917b2179d8524c8d64631);


            var marker_b4cff47336487516d8fd112d0fd46397 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e92e21cfdb36cedfc2d6fd7134c3a3a5 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b4cff47336487516d8fd112d0fd46397.setIcon(icon_e92e21cfdb36cedfc2d6fd7134c3a3a5);


            var marker_51f0e797a0669d5c678d7fe0f11a138e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7a6e41bff251b2c74a5729b43ea93875 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_51f0e797a0669d5c678d7fe0f11a138e.setIcon(icon_7a6e41bff251b2c74a5729b43ea93875);


            var marker_5225739e6f8da47c94b201312b4d50f0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a21b7dfed11f7d1b1b1a58585fca21b0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5225739e6f8da47c94b201312b4d50f0.setIcon(icon_a21b7dfed11f7d1b1b1a58585fca21b0);


            var marker_2935d90ec3ba45cd2543ef9a64eaeecf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5d334919024cba9d301f5a4679399559 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2935d90ec3ba45cd2543ef9a64eaeecf.setIcon(icon_5d334919024cba9d301f5a4679399559);


            var marker_ef2fa80060c883e930db51a7fb2399cf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f630b606af0150713437f16c0eba879 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ef2fa80060c883e930db51a7fb2399cf.setIcon(icon_0f630b606af0150713437f16c0eba879);


            var marker_95a789ff1c1867efc53540af8910ccdd = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e0c97543bf8755ade1f6249efd380bb9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_95a789ff1c1867efc53540af8910ccdd.setIcon(icon_e0c97543bf8755ade1f6249efd380bb9);


            var marker_3f077b03e537d128163b5c86927feadf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_030810f0e1972c7cbbfd2765fa19299c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_3f077b03e537d128163b5c86927feadf.setIcon(icon_030810f0e1972c7cbbfd2765fa19299c);


            var marker_8d8d8576bfb12f4c3f0ac3750d18e505 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d8d8576bfb12f4c3f0ac3750d18e505.setIcon(icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0);


            var marker_4968ba53f5d12369310ccf9994be42b8 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91a4b514ce940d93d1511ae5ebb60c47 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4968ba53f5d12369310ccf9994be42b8.setIcon(icon_91a4b514ce940d93d1511ae5ebb60c47);


            var marker_edf1b4d1c86b086042fdb78a6a50309c = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1add5061d056faaf5a3e799ba9ae555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_edf1b4d1c86b086042fdb78a6a50309c.setIcon(icon_a1add5061d056faaf5a3e799ba9ae555);


            var marker_aec1ac925773a73c70fb629ce7d03ad2 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91148a541330e5590009d961d5206cfe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aec1ac925773a73c70fb629ce7d03ad2.setIcon(icon_91148a541330e5590009d961d5206cfe);


            var marker_0b2d624e4a25e562d789b120d4f279da = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_559e6106fc82969e429355fd2bd8dfe8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0b2d624e4a25e562d789b120d4f279da.setIcon(icon_559e6106fc82969e429355fd2bd8dfe8);


            var marker_261c349094a575034ead3af7f7919807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_70ceb3c0a910779f13a144b4550f3670 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_261c349094a575034ead3af7f7919807.setIcon(icon_70ceb3c0a910779f13a144b4550f3670);


            var mouse_position_578c12ce1a7601adb7ba34cc116e3a90 = new L.Control.MousePosition(
                {&quot;emptyString&quot;: &quot;NaN&quot;, &quot;lngFirst&quot;: false, &quot;numDigits&quot;: 20, &quot;position&quot;: &quot;topright&quot;, &quot;prefix&quot;: &quot;Lat:&quot;, &quot;separator&quot;: &quot; Long: &quot;}
            );
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;latFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;lngFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            map_d368eaa66911ac76cdc66071a54e7a4a.addControl(mouse_position_578c12ce1a7601adb7ba34cc116e3a90);


            var marker_a69c351acdf14dc6d859390a75f1ffb5 = L.marker(
                [34.63617, -120.62503],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_637f720d8f3dcf7801e231070b2d59d5 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003e      1.38 KM\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_a69c351acdf14dc6d859390a75f1ffb5.setIcon(div_icon_637f720d8f3dcf7801e231070b2d59d5);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



*TODO:* Draw a `PolyLine` between a launch site to the selected coastline point



```python
# Create a `folium.PolyLine` object using the coastline coordinates and launch site coordinate
points = []
points.append([launch_site_lat, launch_site_lon])
points.append([lat_coastline, long_coastline])
lines=folium.PolyLine(locations=points, weight=1)
site_map.add_child(lines)
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
                #map_d368eaa66911ac76cdc66071a54e7a4a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/leaflet.markercluster.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.Default.css&quot;/&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.css&quot;/&gt;
&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_d368eaa66911ac76cdc66071a54e7a4a = L.map(
                &quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 5,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_6a6dd63d3a16d33dbc7c6a5ce61a614f = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var circle_09e82ae54ea55eb70d02663e6d4fd807 = L.circle(
                [28.5618571, -80.577366],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_26200c74ecb8356af72e59c8de14bc0a = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_0b4d23af6deac36ab2a0e6ad9141e28a = $(`&lt;div id=&quot;html_0b4d23af6deac36ab2a0e6ad9141e28a&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;CCSFS SLC 40&lt;/div&gt;`)[0];
                popup_26200c74ecb8356af72e59c8de14bc0a.setContent(html_0b4d23af6deac36ab2a0e6ad9141e28a);



        circle_09e82ae54ea55eb70d02663e6d4fd807.bindPopup(popup_26200c74ecb8356af72e59c8de14bc0a)
        ;




            var marker_e171461b01d6b4700b99847473339ad4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_3f1d400bf8c012d2ef5764b75c6ab41a = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eCCSFS SLC 40\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_e171461b01d6b4700b99847473339ad4.setIcon(div_icon_3f1d400bf8c012d2ef5764b75c6ab41a);


            var circle_d98165e55558f9f5f11d0b869e76c55a = L.circle(
                [28.6080585, -80.6039558],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_777bb510b47559a11702bc169d967e63 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_6497d437dcb578c0f8c6bb7b5a3dbdd6 = $(`&lt;div id=&quot;html_6497d437dcb578c0f8c6bb7b5a3dbdd6&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;KSC LC 39A&lt;/div&gt;`)[0];
                popup_777bb510b47559a11702bc169d967e63.setContent(html_6497d437dcb578c0f8c6bb7b5a3dbdd6);



        circle_d98165e55558f9f5f11d0b869e76c55a.bindPopup(popup_777bb510b47559a11702bc169d967e63)
        ;




            var marker_f57fea6caa82c60177b0fd4e8ff11a09 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_33a1c124b806db61af88c3a05ba0a3a8 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eKSC LC 39A\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f57fea6caa82c60177b0fd4e8ff11a09.setIcon(div_icon_33a1c124b806db61af88c3a05ba0a3a8);


            var circle_836706b82952ca5ef3a3059d08a13d33 = L.circle(
                [34.632093, -120.610829],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_09f17b50bcfd8edb3350a1f00d21beb7 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_1281760bebe660c6f20743f8a44a4190 = $(`&lt;div id=&quot;html_1281760bebe660c6f20743f8a44a4190&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;VAFB SLC 4E&lt;/div&gt;`)[0];
                popup_09f17b50bcfd8edb3350a1f00d21beb7.setContent(html_1281760bebe660c6f20743f8a44a4190);



        circle_836706b82952ca5ef3a3059d08a13d33.bindPopup(popup_09f17b50bcfd8edb3350a1f00d21beb7)
        ;




            var marker_f20a64e42fb3657c81fecabae2eef414 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ed87eb3ea7b7542002106081d21adce7 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eVAFB SLC 4E\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f20a64e42fb3657c81fecabae2eef414.setIcon(div_icon_ed87eb3ea7b7542002106081d21adce7);


            var marker_cluster_f116777a8f89526d71ccfa29da685556 = L.markerClusterGroup(
                {}
            );
            map_d368eaa66911ac76cdc66071a54e7a4a.addLayer(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var marker_8e3fbc734492c596f285a59bbd2828eb = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_be99b870932b60b5a059abc5d3eea43b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8e3fbc734492c596f285a59bbd2828eb.setIcon(icon_be99b870932b60b5a059abc5d3eea43b);


            var marker_b265ccc5f3ff2f93e4a59d019a33539a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6ce6675244c5eec8cea2da0f8e52b4c3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b265ccc5f3ff2f93e4a59d019a33539a.setIcon(icon_6ce6675244c5eec8cea2da0f8e52b4c3);


            var marker_e7f8badc6b329032084deb57204bf78d = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_98be638d075e9c8b1753032889c91604 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e7f8badc6b329032084deb57204bf78d.setIcon(icon_98be638d075e9c8b1753032889c91604);


            var marker_9762b11ec5c9a7929169dd5226ab7a7a = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9762b11ec5c9a7929169dd5226ab7a7a.setIcon(icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c);


            var marker_05496be246e112b1586148e09ca8255b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_39d29742c4a629c987f9a863222ac384 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_05496be246e112b1586148e09ca8255b.setIcon(icon_39d29742c4a629c987f9a863222ac384);


            var marker_26b020c8440b964ed52b8233103e3807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0a8961b079f2e64d955495c92596aebe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_26b020c8440b964ed52b8233103e3807.setIcon(icon_0a8961b079f2e64d955495c92596aebe);


            var marker_0c55aa8394bec09019f5fe3bf99655fa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_bbb9ec9636836fd1c13ef05a9691e2af = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c55aa8394bec09019f5fe3bf99655fa.setIcon(icon_bbb9ec9636836fd1c13ef05a9691e2af);


            var marker_af5595c2b48dff9c4425acccf92fc5aa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3130e369bd0b97da036f4a7d8d5c916d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_af5595c2b48dff9c4425acccf92fc5aa.setIcon(icon_3130e369bd0b97da036f4a7d8d5c916d);


            var marker_2102710f13142fd96b2e8d017f15d45e = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3fb6ccb5eae44b18acb0b636f915d39a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2102710f13142fd96b2e8d017f15d45e.setIcon(icon_3fb6ccb5eae44b18acb0b636f915d39a);


            var marker_aaad09c638eba800a7811229eae101b1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d2a0ee20131bc6466a138caaca22529 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aaad09c638eba800a7811229eae101b1.setIcon(icon_0d2a0ee20131bc6466a138caaca22529);


            var marker_4683b9f2e56d38aa1acdf4ad7bbec9ec = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0494882a0773383ff6ab4f7b01c3855a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4683b9f2e56d38aa1acdf4ad7bbec9ec.setIcon(icon_0494882a0773383ff6ab4f7b01c3855a);


            var marker_2c87eb3a097f8f52b16674aceca0c4e0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_c03256aaa0fb0c018707714a45110c29 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c87eb3a097f8f52b16674aceca0c4e0.setIcon(icon_c03256aaa0fb0c018707714a45110c29);


            var marker_acd191db0be53bfd6645a49e1328dc14 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_10e4b136da16c6dae6f8747b86a009de = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_acd191db0be53bfd6645a49e1328dc14.setIcon(icon_10e4b136da16c6dae6f8747b86a009de);


            var marker_c90eed40cb69231d1ad1d5b1b14b2fd1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_4e3460ba0bea820a9b8811d9374d663b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c90eed40cb69231d1ad1d5b1b14b2fd1.setIcon(icon_4e3460ba0bea820a9b8811d9374d663b);


            var marker_280d50c0fecd2cf9a46560dbe6ef20f1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b94714aae52c32397682f3a3aa734555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_280d50c0fecd2cf9a46560dbe6ef20f1.setIcon(icon_b94714aae52c32397682f3a3aa734555);


            var marker_533be051d47af44f20479dde1ed40de0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1df4fc9edee2cae23a865eaded820bf8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_533be051d47af44f20479dde1ed40de0.setIcon(icon_1df4fc9edee2cae23a865eaded820bf8);


            var marker_e19ccf9d8957d95c236100fd2a48623c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e93489884b4c7553766caae417e1a5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e19ccf9d8957d95c236100fd2a48623c.setIcon(icon_e93489884b4c7553766caae417e1a5e6);


            var marker_0ee3df4535b0e25e208cec9a6d0d2c56 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b2d7914a31750a68d48b38d61d336ab9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ee3df4535b0e25e208cec9a6d0d2c56.setIcon(icon_b2d7914a31750a68d48b38d61d336ab9);


            var marker_510b2c2da7cea926b61216c9f4ad7f5b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3315d1234c29a44ed5b3d22c92c20526 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_510b2c2da7cea926b61216c9f4ad7f5b.setIcon(icon_3315d1234c29a44ed5b3d22c92c20526);


            var marker_0c94063a2c7c0b18d2c25fb20f610644 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fde389ae32dedeeeb62bbe811db695eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c94063a2c7c0b18d2c25fb20f610644.setIcon(icon_fde389ae32dedeeeb62bbe811db695eb);


            var marker_0ca96a4682816125e07dbbba2696044c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_47bac55069c2de5ef303c417a2a220eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ca96a4682816125e07dbbba2696044c.setIcon(icon_47bac55069c2de5ef303c417a2a220eb);


            var marker_a3a2d8d01b7de1145acf689ee5bacec8 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_83635d34593afb3ca36a337417c6d55a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a3a2d8d01b7de1145acf689ee5bacec8.setIcon(icon_83635d34593afb3ca36a337417c6d55a);


            var marker_8c2f1eaf61c7e33d6893c83f9ad04f0f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6063d0c719d73692bb69610728fdfa41 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8c2f1eaf61c7e33d6893c83f9ad04f0f.setIcon(icon_6063d0c719d73692bb69610728fdfa41);


            var marker_823751abb97e42969d8e1ab7bb1338c0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_147445c198f6902c97d5ddabb2cbbe6e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_823751abb97e42969d8e1ab7bb1338c0.setIcon(icon_147445c198f6902c97d5ddabb2cbbe6e);


            var marker_dd5006505c03160f51c570ec351bb2e2 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b98939d141ba86ae0b85d89229220c0a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dd5006505c03160f51c570ec351bb2e2.setIcon(icon_b98939d141ba86ae0b85d89229220c0a);


            var marker_8d451c8bbf3b65058c32bfd171379021 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f96f31fc348a21e9883ec56e022eb5d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d451c8bbf3b65058c32bfd171379021.setIcon(icon_1f96f31fc348a21e9883ec56e022eb5d);


            var marker_708fcdb0eb1bee81a0676714a97c4b7e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8d0d02face42693df9cc809b6b1b8b88 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_708fcdb0eb1bee81a0676714a97c4b7e.setIcon(icon_8d0d02face42693df9cc809b6b1b8b88);


            var marker_b20e0cffdc1dcabd8977b8743bfdc23f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e05584755ae63037f94b6629a8a59059 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b20e0cffdc1dcabd8977b8743bfdc23f.setIcon(icon_e05584755ae63037f94b6629a8a59059);


            var marker_12c5ae814402608fe9fcaa0fb953baa3 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_298559faf2923df091deb8a400f32a3a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_12c5ae814402608fe9fcaa0fb953baa3.setIcon(icon_298559faf2923df091deb8a400f32a3a);


            var marker_c7099d8bd405dca8c5d799d78046d989 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8ea03eccae8270a9959977a4e83ad81c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c7099d8bd405dca8c5d799d78046d989.setIcon(icon_8ea03eccae8270a9959977a4e83ad81c);


            var marker_2000a471ecc645745690b5601bf318b9 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_06a011613bb1dd586a10d8b689767a67 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2000a471ecc645745690b5601bf318b9.setIcon(icon_06a011613bb1dd586a10d8b689767a67);


            var marker_9add50083d8f937cdbf3268c42ea3458 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f1103989b51f2b44128d791224a47fb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9add50083d8f937cdbf3268c42ea3458.setIcon(icon_0f1103989b51f2b44128d791224a47fb);


            var marker_fc40396968e3dbf3f0ac5f4e4d2b5a25 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_143a907a9fb195f8a49fc5e047916e95 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fc40396968e3dbf3f0ac5f4e4d2b5a25.setIcon(icon_143a907a9fb195f8a49fc5e047916e95);


            var marker_0191d721bf323854bc15826cdfe155dc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b1888b082353ed7ee63f02be42251844 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0191d721bf323854bc15826cdfe155dc.setIcon(icon_b1888b082353ed7ee63f02be42251844);


            var marker_519c51aeb2798288434522e9ac6c87c1 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2d0385ab07b5db8469e705e50c436347 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_519c51aeb2798288434522e9ac6c87c1.setIcon(icon_2d0385ab07b5db8469e705e50c436347);


            var marker_0c386873db0e06685e966e18238cd440 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5aee252c188ed86e6db7603935c81304 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c386873db0e06685e966e18238cd440.setIcon(icon_5aee252c188ed86e6db7603935c81304);


            var marker_23ec039ad7d08965f7fe479f3ac68d16 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b8059df365b79a0aa6df0de16ac9b5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_23ec039ad7d08965f7fe479f3ac68d16.setIcon(icon_b8059df365b79a0aa6df0de16ac9b5e6);


            var marker_84633a4c74c61cb29d1203a61dd3c729 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8400de5dc2bd3806576abe981463b13f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_84633a4c74c61cb29d1203a61dd3c729.setIcon(icon_8400de5dc2bd3806576abe981463b13f);


            var marker_daf80663ebf4530b28f47594d7374072 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e9a05afd4d6a84c274fa4130eb78b10a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_daf80663ebf4530b28f47594d7374072.setIcon(icon_e9a05afd4d6a84c274fa4130eb78b10a);


            var marker_d6bedb7815d6f288e1d6e598e9baf08f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d6bedb7815d6f288e1d6e598e9baf08f.setIcon(icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6);


            var marker_2988ed8d1eb975d3e0289077e01561a6 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f8a0e6456d1d85ca04f373538d9dab31 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2988ed8d1eb975d3e0289077e01561a6.setIcon(icon_f8a0e6456d1d85ca04f373538d9dab31);


            var marker_ba02f01ffa1ca683ec3cd7b81f4eea0a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_97fcd29be95d04c61837ac90161f99ea = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ba02f01ffa1ca683ec3cd7b81f4eea0a.setIcon(icon_97fcd29be95d04c61837ac90161f99ea);


            var marker_bc822aae3c14f72a8da810e9a99b6bac = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fc9de103d81f6167149e720d5aa5aa2b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_bc822aae3c14f72a8da810e9a99b6bac.setIcon(icon_fc9de103d81f6167149e720d5aa5aa2b);


            var marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_838977de5280226a2bfa533389b76480 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20.setIcon(icon_838977de5280226a2bfa533389b76480);


            var marker_a52800e1677bceb4962f64c50390bb66 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f1dbbf3a3c33e7722e96b26b0e738ec6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a52800e1677bceb4962f64c50390bb66.setIcon(icon_f1dbbf3a3c33e7722e96b26b0e738ec6);


            var marker_7d1155bf736c8cdb6c4901fc51d8fd38 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_ef825d97216fbe7e868ca772588987d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7d1155bf736c8cdb6c4901fc51d8fd38.setIcon(icon_ef825d97216fbe7e868ca772588987d8);


            var marker_ca923c0327d25870f976c7f15784b537 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1f2b83e998d1e4caf4a9d56832f4743 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ca923c0327d25870f976c7f15784b537.setIcon(icon_a1f2b83e998d1e4caf4a9d56832f4743);


            var marker_ff790ea4e51733a9a60bccb92e9abafc = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2ab4348c668a55fc9294e5960858a828 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff790ea4e51733a9a60bccb92e9abafc.setIcon(icon_2ab4348c668a55fc9294e5960858a828);


            var marker_dac712da8151aa914d0d499e9c23fae5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_706d6845d3884cf2f2193927191f7205 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dac712da8151aa914d0d499e9c23fae5.setIcon(icon_706d6845d3884cf2f2193927191f7205);


            var marker_59d64bd4daa0e4c76bc0b7e96db776a5 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_71f216c61addb87f1116e9c737ad2a58 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_59d64bd4daa0e4c76bc0b7e96db776a5.setIcon(icon_71f216c61addb87f1116e9c737ad2a58);


            var marker_1ed241d8a0b5227618f2c00123a45520 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f6e90640e75add03cf3655d777cf298 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_1ed241d8a0b5227618f2c00123a45520.setIcon(icon_1f6e90640e75add03cf3655d777cf298);


            var marker_0fe5cf43ec2696ab17ee89435c81fa59 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7def62d3317a8bc6c77433d3754e21f1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0fe5cf43ec2696ab17ee89435c81fa59.setIcon(icon_7def62d3317a8bc6c77433d3754e21f1);


            var marker_6bb1d8ff9f26c26136f7a8d3130c7795 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_db713ff653ba3f3538a3e5cf442bee18 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6bb1d8ff9f26c26136f7a8d3130c7795.setIcon(icon_db713ff653ba3f3538a3e5cf442bee18);


            var marker_40ad47531ee3347bc2b8307c3d482f84 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_056f353f0ab0f3940d0edeb5a62d9450 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_40ad47531ee3347bc2b8307c3d482f84.setIcon(icon_056f353f0ab0f3940d0edeb5a62d9450);


            var marker_2f5e28ca54928223f465ad1e9b2d1a2c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5fe6f90fe798a441c8ef45f7d3ca4a83 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2f5e28ca54928223f465ad1e9b2d1a2c.setIcon(icon_5fe6f90fe798a441c8ef45f7d3ca4a83);


            var marker_9aa6387fdb95f9185c705393980f5d3f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_603c180022e818d583932c7ef1743ed2 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9aa6387fdb95f9185c705393980f5d3f.setIcon(icon_603c180022e818d583932c7ef1743ed2);


            var marker_03999bb96d185e87146526d6aad223da = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_59fdaca20197a09206d1af4a2af7a6ff = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_03999bb96d185e87146526d6aad223da.setIcon(icon_59fdaca20197a09206d1af4a2af7a6ff);


            var marker_9579df7e2872157b05112a0a74207fff = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b5fe47b713a39ecd2c5016e4837d8f21 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9579df7e2872157b05112a0a74207fff.setIcon(icon_b5fe47b713a39ecd2c5016e4837d8f21);


            var marker_fe58589b434072a96e9ca818337e301e = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_22ef0459c3d7134064b56333a32c3a34 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fe58589b434072a96e9ca818337e301e.setIcon(icon_22ef0459c3d7134064b56333a32c3a34);


            var marker_2c2708b05946fddc4cb79d1f570c0411 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f227efc1cf97295470efc9ff7a5250ee = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c2708b05946fddc4cb79d1f570c0411.setIcon(icon_f227efc1cf97295470efc9ff7a5250ee);


            var marker_8dfdc79822d8532d024e26c0533b3528 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e214056aead57dde15d727a608736dbc = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8dfdc79822d8532d024e26c0533b3528.setIcon(icon_e214056aead57dde15d727a608736dbc);


            var marker_65762ab5b76ba27e40d107ea6d5662fc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6fa2242edc8c567ce471f952aadcff4f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_65762ab5b76ba27e40d107ea6d5662fc.setIcon(icon_6fa2242edc8c567ce471f952aadcff4f);


            var marker_b2f4dffaa3b8995e46edc01b5e231294 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_745981901199b63d593decaaf610049d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b2f4dffaa3b8995e46edc01b5e231294.setIcon(icon_745981901199b63d593decaaf610049d);


            var marker_a9308cfef5cd0c0e7a6434475f790736 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d908ab4d9700ce83efee476e886b97c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a9308cfef5cd0c0e7a6434475f790736.setIcon(icon_0d908ab4d9700ce83efee476e886b97c);


            var marker_d0cb20b9c2fbabc28c8bd6667c4d20f4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_01f7752eabf64318413fd39cb59b0d16 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d0cb20b9c2fbabc28c8bd6667c4d20f4.setIcon(icon_01f7752eabf64318413fd39cb59b0d16);


            var marker_37ff74361f6f1ed036dde3dc72a710d4 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6bfa6cc0bd3deb7ee1305998105d067c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_37ff74361f6f1ed036dde3dc72a710d4.setIcon(icon_6bfa6cc0bd3deb7ee1305998105d067c);


            var marker_348bd81104c9276a374aeee3bf631025 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5450a9a60d82142d1bde1659c90a9a6f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_348bd81104c9276a374aeee3bf631025.setIcon(icon_5450a9a60d82142d1bde1659c90a9a6f);


            var marker_656aa8c496ce988aa58b4365bae3cb48 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_32b90f4bd7cd6a319396722faff2939e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_656aa8c496ce988aa58b4365bae3cb48.setIcon(icon_32b90f4bd7cd6a319396722faff2939e);


            var marker_b880452991c33e3f8f68f6cb4d363db9 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1aaa4c50431786cd7394f328d450b0c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b880452991c33e3f8f68f6cb4d363db9.setIcon(icon_a1aaa4c50431786cd7394f328d450b0c);


            var marker_5c48f11769ffa2eafd0f12a25ca369de = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5599a959cd465cd28701318805cb19d1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5c48f11769ffa2eafd0f12a25ca369de.setIcon(icon_5599a959cd465cd28701318805cb19d1);


            var marker_f48cc39051e69a5069cbc8fb6f789f89 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_48b719408e4dce1f37c15725ffff8c1b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_f48cc39051e69a5069cbc8fb6f789f89.setIcon(icon_48b719408e4dce1f37c15725ffff8c1b);


            var marker_df2855f0ce1391655b901800e790a7b5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e71d9950cadbbc68538039b6a52b501 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_df2855f0ce1391655b901800e790a7b5.setIcon(icon_8e71d9950cadbbc68538039b6a52b501);


            var marker_cf7cac7da28106a1abbc95f56c8cad2f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e8913d5711160f297a4ed477f016f5d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cf7cac7da28106a1abbc95f56c8cad2f.setIcon(icon_e8913d5711160f297a4ed477f016f5d8);


            var marker_49aac07dc6d5f089b3f54172a7567169 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e5dcbd4cfc8bcfabd0f03262f16434c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_49aac07dc6d5f089b3f54172a7567169.setIcon(icon_8e5dcbd4cfc8bcfabd0f03262f16434c);


            var marker_efd69645b1c9090161b21ad65eb9da28 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_40cd0958be52c8dc2f6ebe70e1860f98 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_efd69645b1c9090161b21ad65eb9da28.setIcon(icon_40cd0958be52c8dc2f6ebe70e1860f98);


            var marker_ff3388e2d1de09848799c111c1d77c75 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_cdd9837aa5d8b7d9e4b337c560b75293 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff3388e2d1de09848799c111c1d77c75.setIcon(icon_cdd9837aa5d8b7d9e4b337c560b75293);


            var marker_541ba9984e25e0505a1ba82f62b55260 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f32393a6937917b2179d8524c8d64631 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_541ba9984e25e0505a1ba82f62b55260.setIcon(icon_f32393a6937917b2179d8524c8d64631);


            var marker_b4cff47336487516d8fd112d0fd46397 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e92e21cfdb36cedfc2d6fd7134c3a3a5 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b4cff47336487516d8fd112d0fd46397.setIcon(icon_e92e21cfdb36cedfc2d6fd7134c3a3a5);


            var marker_51f0e797a0669d5c678d7fe0f11a138e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7a6e41bff251b2c74a5729b43ea93875 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_51f0e797a0669d5c678d7fe0f11a138e.setIcon(icon_7a6e41bff251b2c74a5729b43ea93875);


            var marker_5225739e6f8da47c94b201312b4d50f0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a21b7dfed11f7d1b1b1a58585fca21b0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5225739e6f8da47c94b201312b4d50f0.setIcon(icon_a21b7dfed11f7d1b1b1a58585fca21b0);


            var marker_2935d90ec3ba45cd2543ef9a64eaeecf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5d334919024cba9d301f5a4679399559 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2935d90ec3ba45cd2543ef9a64eaeecf.setIcon(icon_5d334919024cba9d301f5a4679399559);


            var marker_ef2fa80060c883e930db51a7fb2399cf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f630b606af0150713437f16c0eba879 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ef2fa80060c883e930db51a7fb2399cf.setIcon(icon_0f630b606af0150713437f16c0eba879);


            var marker_95a789ff1c1867efc53540af8910ccdd = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e0c97543bf8755ade1f6249efd380bb9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_95a789ff1c1867efc53540af8910ccdd.setIcon(icon_e0c97543bf8755ade1f6249efd380bb9);


            var marker_3f077b03e537d128163b5c86927feadf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_030810f0e1972c7cbbfd2765fa19299c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_3f077b03e537d128163b5c86927feadf.setIcon(icon_030810f0e1972c7cbbfd2765fa19299c);


            var marker_8d8d8576bfb12f4c3f0ac3750d18e505 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d8d8576bfb12f4c3f0ac3750d18e505.setIcon(icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0);


            var marker_4968ba53f5d12369310ccf9994be42b8 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91a4b514ce940d93d1511ae5ebb60c47 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4968ba53f5d12369310ccf9994be42b8.setIcon(icon_91a4b514ce940d93d1511ae5ebb60c47);


            var marker_edf1b4d1c86b086042fdb78a6a50309c = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1add5061d056faaf5a3e799ba9ae555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_edf1b4d1c86b086042fdb78a6a50309c.setIcon(icon_a1add5061d056faaf5a3e799ba9ae555);


            var marker_aec1ac925773a73c70fb629ce7d03ad2 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91148a541330e5590009d961d5206cfe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aec1ac925773a73c70fb629ce7d03ad2.setIcon(icon_91148a541330e5590009d961d5206cfe);


            var marker_0b2d624e4a25e562d789b120d4f279da = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_559e6106fc82969e429355fd2bd8dfe8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0b2d624e4a25e562d789b120d4f279da.setIcon(icon_559e6106fc82969e429355fd2bd8dfe8);


            var marker_261c349094a575034ead3af7f7919807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_70ceb3c0a910779f13a144b4550f3670 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_261c349094a575034ead3af7f7919807.setIcon(icon_70ceb3c0a910779f13a144b4550f3670);


            var mouse_position_578c12ce1a7601adb7ba34cc116e3a90 = new L.Control.MousePosition(
                {&quot;emptyString&quot;: &quot;NaN&quot;, &quot;lngFirst&quot;: false, &quot;numDigits&quot;: 20, &quot;position&quot;: &quot;topright&quot;, &quot;prefix&quot;: &quot;Lat:&quot;, &quot;separator&quot;: &quot; Long: &quot;}
            );
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;latFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;lngFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            map_d368eaa66911ac76cdc66071a54e7a4a.addControl(mouse_position_578c12ce1a7601adb7ba34cc116e3a90);


            var marker_a69c351acdf14dc6d859390a75f1ffb5 = L.marker(
                [34.63617, -120.62503],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_637f720d8f3dcf7801e231070b2d59d5 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003e      1.38 KM\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_a69c351acdf14dc6d859390a75f1ffb5.setIcon(div_icon_637f720d8f3dcf7801e231070b2d59d5);


            var poly_line_5a43e3e10525cc8ef1a06b7f77be75fd = L.polyline(
                [[34.632093, -120.610829], [34.63617, -120.62503]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 1}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



Your updated map with distance line should look like the following screenshot:


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/launch_site_marker_distance.png" />
</center>


*TODO:* Similarly, you can draw a line betwee a launch site to its closest city, railway, highway, etc. You need to use `MousePosition` to find the their coordinates on the map first


A railway map symbol may look like this:


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/railway.png" />
</center>


A highway map symbol may look like this:


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/highway.png" />
</center>


A city map symbol may look like this:


<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_3/images/city.png" />
</center>



```python
# Create a marker with distance to a closest city, railway, highway, etc.
# Draw a line between the marker to the launch site
# find coordinate of the closet city
# e.g.,: Lat: 28.56367  Lon: -80.57163
launch_site_lat = launch_sites_df.iloc[2]["Latitude"]
launch_site_lon = launch_sites_df.iloc[2]["Longitude"]
lat_city = 33.91
long_city = -118.19
distance_city = calculate_distance(launch_site_lat, launch_site_lon, lat_city, long_city)
print(distance_city)
print(launch_site_lat)
print(launch_site_lon)
```

    236.5623564063504
    34.632093
    -120.610829



```python
# Create and add a folium.Marker on your selected closest city point on the map
# Display the distance between city point and launch site using the icon property 
# for example
coordinate = [lat_city, long_city]

distance_city_marker = folium.Marker(
    coordinate,
    icon=DivIcon(
        icon_size=(20,20),
        icon_anchor=(0,0),
        html='<div style="font-size: 12; color:#d35400;"><b>%s</b></div>' % "{:10.2f} KM".format(distance_city),
        )
    )
site_map.add_child(distance_city_marker)
# Create a `folium.PolyLine` object using the city coordinates and launch site coordinate
points = []
points.append([launch_site_lat, launch_site_lon])
points.append([lat_city, long_city])
lines=folium.PolyLine(locations=points, weight=1)
site_map.add_child(lines)
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
                #map_d368eaa66911ac76cdc66071a54e7a4a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
            &lt;/style&gt;

    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/leaflet.markercluster.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.1.0/MarkerCluster.Default.css&quot;/&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/ardhi/Leaflet.MousePosition/src/L.Control.MousePosition.min.css&quot;/&gt;
&lt;/head&gt;
&lt;body&gt;    

            &lt;div class=&quot;folium-map&quot; id=&quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;    

            var map_d368eaa66911ac76cdc66071a54e7a4a = L.map(
                &quot;map_d368eaa66911ac76cdc66071a54e7a4a&quot;,
                {
                    center: [29.559684888503615, -95.0830971930759],
                    crs: L.CRS.EPSG3857,
                    zoom: 5,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );





            var tile_layer_6a6dd63d3a16d33dbc7c6a5ce61a614f = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var circle_09e82ae54ea55eb70d02663e6d4fd807 = L.circle(
                [28.5618571, -80.577366],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_26200c74ecb8356af72e59c8de14bc0a = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_0b4d23af6deac36ab2a0e6ad9141e28a = $(`&lt;div id=&quot;html_0b4d23af6deac36ab2a0e6ad9141e28a&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;CCSFS SLC 40&lt;/div&gt;`)[0];
                popup_26200c74ecb8356af72e59c8de14bc0a.setContent(html_0b4d23af6deac36ab2a0e6ad9141e28a);



        circle_09e82ae54ea55eb70d02663e6d4fd807.bindPopup(popup_26200c74ecb8356af72e59c8de14bc0a)
        ;




            var marker_e171461b01d6b4700b99847473339ad4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_3f1d400bf8c012d2ef5764b75c6ab41a = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eCCSFS SLC 40\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_e171461b01d6b4700b99847473339ad4.setIcon(div_icon_3f1d400bf8c012d2ef5764b75c6ab41a);


            var circle_d98165e55558f9f5f11d0b869e76c55a = L.circle(
                [28.6080585, -80.6039558],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_777bb510b47559a11702bc169d967e63 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_6497d437dcb578c0f8c6bb7b5a3dbdd6 = $(`&lt;div id=&quot;html_6497d437dcb578c0f8c6bb7b5a3dbdd6&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;KSC LC 39A&lt;/div&gt;`)[0];
                popup_777bb510b47559a11702bc169d967e63.setContent(html_6497d437dcb578c0f8c6bb7b5a3dbdd6);



        circle_d98165e55558f9f5f11d0b869e76c55a.bindPopup(popup_777bb510b47559a11702bc169d967e63)
        ;




            var marker_f57fea6caa82c60177b0fd4e8ff11a09 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_33a1c124b806db61af88c3a05ba0a3a8 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eKSC LC 39A\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f57fea6caa82c60177b0fd4e8ff11a09.setIcon(div_icon_33a1c124b806db61af88c3a05ba0a3a8);


            var circle_836706b82952ca5ef3a3059d08a13d33 = L.circle(
                [34.632093, -120.610829],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#d35400&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#d35400&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 1000, &quot;stroke&quot;: true, &quot;weight&quot;: 3}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


        var popup_09f17b50bcfd8edb3350a1f00d21beb7 = L.popup({&quot;maxWidth&quot;: &quot;100%&quot;});



                var html_1281760bebe660c6f20743f8a44a4190 = $(`&lt;div id=&quot;html_1281760bebe660c6f20743f8a44a4190&quot; style=&quot;width: 100.0%; height: 100.0%;&quot;&gt;VAFB SLC 4E&lt;/div&gt;`)[0];
                popup_09f17b50bcfd8edb3350a1f00d21beb7.setContent(html_1281760bebe660c6f20743f8a44a4190);



        circle_836706b82952ca5ef3a3059d08a13d33.bindPopup(popup_09f17b50bcfd8edb3350a1f00d21beb7)
        ;




            var marker_f20a64e42fb3657c81fecabae2eef414 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ed87eb3ea7b7542002106081d21adce7 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003eVAFB SLC 4E\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_f20a64e42fb3657c81fecabae2eef414.setIcon(div_icon_ed87eb3ea7b7542002106081d21adce7);


            var marker_cluster_f116777a8f89526d71ccfa29da685556 = L.markerClusterGroup(
                {}
            );
            map_d368eaa66911ac76cdc66071a54e7a4a.addLayer(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var marker_8e3fbc734492c596f285a59bbd2828eb = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_be99b870932b60b5a059abc5d3eea43b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8e3fbc734492c596f285a59bbd2828eb.setIcon(icon_be99b870932b60b5a059abc5d3eea43b);


            var marker_b265ccc5f3ff2f93e4a59d019a33539a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6ce6675244c5eec8cea2da0f8e52b4c3 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b265ccc5f3ff2f93e4a59d019a33539a.setIcon(icon_6ce6675244c5eec8cea2da0f8e52b4c3);


            var marker_e7f8badc6b329032084deb57204bf78d = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_98be638d075e9c8b1753032889c91604 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e7f8badc6b329032084deb57204bf78d.setIcon(icon_98be638d075e9c8b1753032889c91604);


            var marker_9762b11ec5c9a7929169dd5226ab7a7a = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9762b11ec5c9a7929169dd5226ab7a7a.setIcon(icon_d9d13284f5bc0f9fa2cbdc80b9ecb10c);


            var marker_05496be246e112b1586148e09ca8255b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_39d29742c4a629c987f9a863222ac384 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_05496be246e112b1586148e09ca8255b.setIcon(icon_39d29742c4a629c987f9a863222ac384);


            var marker_26b020c8440b964ed52b8233103e3807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0a8961b079f2e64d955495c92596aebe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_26b020c8440b964ed52b8233103e3807.setIcon(icon_0a8961b079f2e64d955495c92596aebe);


            var marker_0c55aa8394bec09019f5fe3bf99655fa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_bbb9ec9636836fd1c13ef05a9691e2af = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c55aa8394bec09019f5fe3bf99655fa.setIcon(icon_bbb9ec9636836fd1c13ef05a9691e2af);


            var marker_af5595c2b48dff9c4425acccf92fc5aa = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3130e369bd0b97da036f4a7d8d5c916d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_af5595c2b48dff9c4425acccf92fc5aa.setIcon(icon_3130e369bd0b97da036f4a7d8d5c916d);


            var marker_2102710f13142fd96b2e8d017f15d45e = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3fb6ccb5eae44b18acb0b636f915d39a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2102710f13142fd96b2e8d017f15d45e.setIcon(icon_3fb6ccb5eae44b18acb0b636f915d39a);


            var marker_aaad09c638eba800a7811229eae101b1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d2a0ee20131bc6466a138caaca22529 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aaad09c638eba800a7811229eae101b1.setIcon(icon_0d2a0ee20131bc6466a138caaca22529);


            var marker_4683b9f2e56d38aa1acdf4ad7bbec9ec = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0494882a0773383ff6ab4f7b01c3855a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4683b9f2e56d38aa1acdf4ad7bbec9ec.setIcon(icon_0494882a0773383ff6ab4f7b01c3855a);


            var marker_2c87eb3a097f8f52b16674aceca0c4e0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_c03256aaa0fb0c018707714a45110c29 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c87eb3a097f8f52b16674aceca0c4e0.setIcon(icon_c03256aaa0fb0c018707714a45110c29);


            var marker_acd191db0be53bfd6645a49e1328dc14 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_10e4b136da16c6dae6f8747b86a009de = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_acd191db0be53bfd6645a49e1328dc14.setIcon(icon_10e4b136da16c6dae6f8747b86a009de);


            var marker_c90eed40cb69231d1ad1d5b1b14b2fd1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_4e3460ba0bea820a9b8811d9374d663b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c90eed40cb69231d1ad1d5b1b14b2fd1.setIcon(icon_4e3460ba0bea820a9b8811d9374d663b);


            var marker_280d50c0fecd2cf9a46560dbe6ef20f1 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b94714aae52c32397682f3a3aa734555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_280d50c0fecd2cf9a46560dbe6ef20f1.setIcon(icon_b94714aae52c32397682f3a3aa734555);


            var marker_533be051d47af44f20479dde1ed40de0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1df4fc9edee2cae23a865eaded820bf8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_533be051d47af44f20479dde1ed40de0.setIcon(icon_1df4fc9edee2cae23a865eaded820bf8);


            var marker_e19ccf9d8957d95c236100fd2a48623c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e93489884b4c7553766caae417e1a5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_e19ccf9d8957d95c236100fd2a48623c.setIcon(icon_e93489884b4c7553766caae417e1a5e6);


            var marker_0ee3df4535b0e25e208cec9a6d0d2c56 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b2d7914a31750a68d48b38d61d336ab9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ee3df4535b0e25e208cec9a6d0d2c56.setIcon(icon_b2d7914a31750a68d48b38d61d336ab9);


            var marker_510b2c2da7cea926b61216c9f4ad7f5b = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3315d1234c29a44ed5b3d22c92c20526 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_510b2c2da7cea926b61216c9f4ad7f5b.setIcon(icon_3315d1234c29a44ed5b3d22c92c20526);


            var marker_0c94063a2c7c0b18d2c25fb20f610644 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fde389ae32dedeeeb62bbe811db695eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c94063a2c7c0b18d2c25fb20f610644.setIcon(icon_fde389ae32dedeeeb62bbe811db695eb);


            var marker_0ca96a4682816125e07dbbba2696044c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_47bac55069c2de5ef303c417a2a220eb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0ca96a4682816125e07dbbba2696044c.setIcon(icon_47bac55069c2de5ef303c417a2a220eb);


            var marker_a3a2d8d01b7de1145acf689ee5bacec8 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_83635d34593afb3ca36a337417c6d55a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a3a2d8d01b7de1145acf689ee5bacec8.setIcon(icon_83635d34593afb3ca36a337417c6d55a);


            var marker_8c2f1eaf61c7e33d6893c83f9ad04f0f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6063d0c719d73692bb69610728fdfa41 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8c2f1eaf61c7e33d6893c83f9ad04f0f.setIcon(icon_6063d0c719d73692bb69610728fdfa41);


            var marker_823751abb97e42969d8e1ab7bb1338c0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_147445c198f6902c97d5ddabb2cbbe6e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_823751abb97e42969d8e1ab7bb1338c0.setIcon(icon_147445c198f6902c97d5ddabb2cbbe6e);


            var marker_dd5006505c03160f51c570ec351bb2e2 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b98939d141ba86ae0b85d89229220c0a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dd5006505c03160f51c570ec351bb2e2.setIcon(icon_b98939d141ba86ae0b85d89229220c0a);


            var marker_8d451c8bbf3b65058c32bfd171379021 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f96f31fc348a21e9883ec56e022eb5d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d451c8bbf3b65058c32bfd171379021.setIcon(icon_1f96f31fc348a21e9883ec56e022eb5d);


            var marker_708fcdb0eb1bee81a0676714a97c4b7e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8d0d02face42693df9cc809b6b1b8b88 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_708fcdb0eb1bee81a0676714a97c4b7e.setIcon(icon_8d0d02face42693df9cc809b6b1b8b88);


            var marker_b20e0cffdc1dcabd8977b8743bfdc23f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e05584755ae63037f94b6629a8a59059 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b20e0cffdc1dcabd8977b8743bfdc23f.setIcon(icon_e05584755ae63037f94b6629a8a59059);


            var marker_12c5ae814402608fe9fcaa0fb953baa3 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_298559faf2923df091deb8a400f32a3a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_12c5ae814402608fe9fcaa0fb953baa3.setIcon(icon_298559faf2923df091deb8a400f32a3a);


            var marker_c7099d8bd405dca8c5d799d78046d989 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8ea03eccae8270a9959977a4e83ad81c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_c7099d8bd405dca8c5d799d78046d989.setIcon(icon_8ea03eccae8270a9959977a4e83ad81c);


            var marker_2000a471ecc645745690b5601bf318b9 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_06a011613bb1dd586a10d8b689767a67 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2000a471ecc645745690b5601bf318b9.setIcon(icon_06a011613bb1dd586a10d8b689767a67);


            var marker_9add50083d8f937cdbf3268c42ea3458 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f1103989b51f2b44128d791224a47fb = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9add50083d8f937cdbf3268c42ea3458.setIcon(icon_0f1103989b51f2b44128d791224a47fb);


            var marker_fc40396968e3dbf3f0ac5f4e4d2b5a25 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_143a907a9fb195f8a49fc5e047916e95 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fc40396968e3dbf3f0ac5f4e4d2b5a25.setIcon(icon_143a907a9fb195f8a49fc5e047916e95);


            var marker_0191d721bf323854bc15826cdfe155dc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b1888b082353ed7ee63f02be42251844 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0191d721bf323854bc15826cdfe155dc.setIcon(icon_b1888b082353ed7ee63f02be42251844);


            var marker_519c51aeb2798288434522e9ac6c87c1 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2d0385ab07b5db8469e705e50c436347 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_519c51aeb2798288434522e9ac6c87c1.setIcon(icon_2d0385ab07b5db8469e705e50c436347);


            var marker_0c386873db0e06685e966e18238cd440 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5aee252c188ed86e6db7603935c81304 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0c386873db0e06685e966e18238cd440.setIcon(icon_5aee252c188ed86e6db7603935c81304);


            var marker_23ec039ad7d08965f7fe479f3ac68d16 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b8059df365b79a0aa6df0de16ac9b5e6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_23ec039ad7d08965f7fe479f3ac68d16.setIcon(icon_b8059df365b79a0aa6df0de16ac9b5e6);


            var marker_84633a4c74c61cb29d1203a61dd3c729 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8400de5dc2bd3806576abe981463b13f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_84633a4c74c61cb29d1203a61dd3c729.setIcon(icon_8400de5dc2bd3806576abe981463b13f);


            var marker_daf80663ebf4530b28f47594d7374072 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e9a05afd4d6a84c274fa4130eb78b10a = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_daf80663ebf4530b28f47594d7374072.setIcon(icon_e9a05afd4d6a84c274fa4130eb78b10a);


            var marker_d6bedb7815d6f288e1d6e598e9baf08f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d6bedb7815d6f288e1d6e598e9baf08f.setIcon(icon_626c23bc2f4ffe2a0e4c9249cb4cf9b6);


            var marker_2988ed8d1eb975d3e0289077e01561a6 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f8a0e6456d1d85ca04f373538d9dab31 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2988ed8d1eb975d3e0289077e01561a6.setIcon(icon_f8a0e6456d1d85ca04f373538d9dab31);


            var marker_ba02f01ffa1ca683ec3cd7b81f4eea0a = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_97fcd29be95d04c61837ac90161f99ea = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ba02f01ffa1ca683ec3cd7b81f4eea0a.setIcon(icon_97fcd29be95d04c61837ac90161f99ea);


            var marker_bc822aae3c14f72a8da810e9a99b6bac = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_fc9de103d81f6167149e720d5aa5aa2b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_bc822aae3c14f72a8da810e9a99b6bac.setIcon(icon_fc9de103d81f6167149e720d5aa5aa2b);


            var marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_838977de5280226a2bfa533389b76480 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8ad60f45b0e1fe9bc6c7d6dd26f29a20.setIcon(icon_838977de5280226a2bfa533389b76480);


            var marker_a52800e1677bceb4962f64c50390bb66 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f1dbbf3a3c33e7722e96b26b0e738ec6 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a52800e1677bceb4962f64c50390bb66.setIcon(icon_f1dbbf3a3c33e7722e96b26b0e738ec6);


            var marker_7d1155bf736c8cdb6c4901fc51d8fd38 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_ef825d97216fbe7e868ca772588987d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_7d1155bf736c8cdb6c4901fc51d8fd38.setIcon(icon_ef825d97216fbe7e868ca772588987d8);


            var marker_ca923c0327d25870f976c7f15784b537 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1f2b83e998d1e4caf4a9d56832f4743 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ca923c0327d25870f976c7f15784b537.setIcon(icon_a1f2b83e998d1e4caf4a9d56832f4743);


            var marker_ff790ea4e51733a9a60bccb92e9abafc = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_2ab4348c668a55fc9294e5960858a828 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff790ea4e51733a9a60bccb92e9abafc.setIcon(icon_2ab4348c668a55fc9294e5960858a828);


            var marker_dac712da8151aa914d0d499e9c23fae5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_706d6845d3884cf2f2193927191f7205 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_dac712da8151aa914d0d499e9c23fae5.setIcon(icon_706d6845d3884cf2f2193927191f7205);


            var marker_59d64bd4daa0e4c76bc0b7e96db776a5 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_71f216c61addb87f1116e9c737ad2a58 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_59d64bd4daa0e4c76bc0b7e96db776a5.setIcon(icon_71f216c61addb87f1116e9c737ad2a58);


            var marker_1ed241d8a0b5227618f2c00123a45520 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_1f6e90640e75add03cf3655d777cf298 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_1ed241d8a0b5227618f2c00123a45520.setIcon(icon_1f6e90640e75add03cf3655d777cf298);


            var marker_0fe5cf43ec2696ab17ee89435c81fa59 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7def62d3317a8bc6c77433d3754e21f1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0fe5cf43ec2696ab17ee89435c81fa59.setIcon(icon_7def62d3317a8bc6c77433d3754e21f1);


            var marker_6bb1d8ff9f26c26136f7a8d3130c7795 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_db713ff653ba3f3538a3e5cf442bee18 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_6bb1d8ff9f26c26136f7a8d3130c7795.setIcon(icon_db713ff653ba3f3538a3e5cf442bee18);


            var marker_40ad47531ee3347bc2b8307c3d482f84 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_056f353f0ab0f3940d0edeb5a62d9450 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_40ad47531ee3347bc2b8307c3d482f84.setIcon(icon_056f353f0ab0f3940d0edeb5a62d9450);


            var marker_2f5e28ca54928223f465ad1e9b2d1a2c = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5fe6f90fe798a441c8ef45f7d3ca4a83 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2f5e28ca54928223f465ad1e9b2d1a2c.setIcon(icon_5fe6f90fe798a441c8ef45f7d3ca4a83);


            var marker_9aa6387fdb95f9185c705393980f5d3f = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_603c180022e818d583932c7ef1743ed2 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9aa6387fdb95f9185c705393980f5d3f.setIcon(icon_603c180022e818d583932c7ef1743ed2);


            var marker_03999bb96d185e87146526d6aad223da = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_59fdaca20197a09206d1af4a2af7a6ff = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_03999bb96d185e87146526d6aad223da.setIcon(icon_59fdaca20197a09206d1af4a2af7a6ff);


            var marker_9579df7e2872157b05112a0a74207fff = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_b5fe47b713a39ecd2c5016e4837d8f21 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_9579df7e2872157b05112a0a74207fff.setIcon(icon_b5fe47b713a39ecd2c5016e4837d8f21);


            var marker_fe58589b434072a96e9ca818337e301e = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_22ef0459c3d7134064b56333a32c3a34 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_fe58589b434072a96e9ca818337e301e.setIcon(icon_22ef0459c3d7134064b56333a32c3a34);


            var marker_2c2708b05946fddc4cb79d1f570c0411 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f227efc1cf97295470efc9ff7a5250ee = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2c2708b05946fddc4cb79d1f570c0411.setIcon(icon_f227efc1cf97295470efc9ff7a5250ee);


            var marker_8dfdc79822d8532d024e26c0533b3528 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e214056aead57dde15d727a608736dbc = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8dfdc79822d8532d024e26c0533b3528.setIcon(icon_e214056aead57dde15d727a608736dbc);


            var marker_65762ab5b76ba27e40d107ea6d5662fc = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6fa2242edc8c567ce471f952aadcff4f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_65762ab5b76ba27e40d107ea6d5662fc.setIcon(icon_6fa2242edc8c567ce471f952aadcff4f);


            var marker_b2f4dffaa3b8995e46edc01b5e231294 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_745981901199b63d593decaaf610049d = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b2f4dffaa3b8995e46edc01b5e231294.setIcon(icon_745981901199b63d593decaaf610049d);


            var marker_a9308cfef5cd0c0e7a6434475f790736 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0d908ab4d9700ce83efee476e886b97c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_a9308cfef5cd0c0e7a6434475f790736.setIcon(icon_0d908ab4d9700ce83efee476e886b97c);


            var marker_d0cb20b9c2fbabc28c8bd6667c4d20f4 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_01f7752eabf64318413fd39cb59b0d16 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_d0cb20b9c2fbabc28c8bd6667c4d20f4.setIcon(icon_01f7752eabf64318413fd39cb59b0d16);


            var marker_37ff74361f6f1ed036dde3dc72a710d4 = L.marker(
                [34.632093, -120.610829],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_6bfa6cc0bd3deb7ee1305998105d067c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_37ff74361f6f1ed036dde3dc72a710d4.setIcon(icon_6bfa6cc0bd3deb7ee1305998105d067c);


            var marker_348bd81104c9276a374aeee3bf631025 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5450a9a60d82142d1bde1659c90a9a6f = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_348bd81104c9276a374aeee3bf631025.setIcon(icon_5450a9a60d82142d1bde1659c90a9a6f);


            var marker_656aa8c496ce988aa58b4365bae3cb48 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_32b90f4bd7cd6a319396722faff2939e = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_656aa8c496ce988aa58b4365bae3cb48.setIcon(icon_32b90f4bd7cd6a319396722faff2939e);


            var marker_b880452991c33e3f8f68f6cb4d363db9 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1aaa4c50431786cd7394f328d450b0c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b880452991c33e3f8f68f6cb4d363db9.setIcon(icon_a1aaa4c50431786cd7394f328d450b0c);


            var marker_5c48f11769ffa2eafd0f12a25ca369de = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5599a959cd465cd28701318805cb19d1 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5c48f11769ffa2eafd0f12a25ca369de.setIcon(icon_5599a959cd465cd28701318805cb19d1);


            var marker_f48cc39051e69a5069cbc8fb6f789f89 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_48b719408e4dce1f37c15725ffff8c1b = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_f48cc39051e69a5069cbc8fb6f789f89.setIcon(icon_48b719408e4dce1f37c15725ffff8c1b);


            var marker_df2855f0ce1391655b901800e790a7b5 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e71d9950cadbbc68538039b6a52b501 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_df2855f0ce1391655b901800e790a7b5.setIcon(icon_8e71d9950cadbbc68538039b6a52b501);


            var marker_cf7cac7da28106a1abbc95f56c8cad2f = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e8913d5711160f297a4ed477f016f5d8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_cf7cac7da28106a1abbc95f56c8cad2f.setIcon(icon_e8913d5711160f297a4ed477f016f5d8);


            var marker_49aac07dc6d5f089b3f54172a7567169 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_8e5dcbd4cfc8bcfabd0f03262f16434c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_49aac07dc6d5f089b3f54172a7567169.setIcon(icon_8e5dcbd4cfc8bcfabd0f03262f16434c);


            var marker_efd69645b1c9090161b21ad65eb9da28 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_40cd0958be52c8dc2f6ebe70e1860f98 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_efd69645b1c9090161b21ad65eb9da28.setIcon(icon_40cd0958be52c8dc2f6ebe70e1860f98);


            var marker_ff3388e2d1de09848799c111c1d77c75 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_cdd9837aa5d8b7d9e4b337c560b75293 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ff3388e2d1de09848799c111c1d77c75.setIcon(icon_cdd9837aa5d8b7d9e4b337c560b75293);


            var marker_541ba9984e25e0505a1ba82f62b55260 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_f32393a6937917b2179d8524c8d64631 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;red&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_541ba9984e25e0505a1ba82f62b55260.setIcon(icon_f32393a6937917b2179d8524c8d64631);


            var marker_b4cff47336487516d8fd112d0fd46397 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e92e21cfdb36cedfc2d6fd7134c3a3a5 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_b4cff47336487516d8fd112d0fd46397.setIcon(icon_e92e21cfdb36cedfc2d6fd7134c3a3a5);


            var marker_51f0e797a0669d5c678d7fe0f11a138e = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_7a6e41bff251b2c74a5729b43ea93875 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_51f0e797a0669d5c678d7fe0f11a138e.setIcon(icon_7a6e41bff251b2c74a5729b43ea93875);


            var marker_5225739e6f8da47c94b201312b4d50f0 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a21b7dfed11f7d1b1b1a58585fca21b0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_5225739e6f8da47c94b201312b4d50f0.setIcon(icon_a21b7dfed11f7d1b1b1a58585fca21b0);


            var marker_2935d90ec3ba45cd2543ef9a64eaeecf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_5d334919024cba9d301f5a4679399559 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_2935d90ec3ba45cd2543ef9a64eaeecf.setIcon(icon_5d334919024cba9d301f5a4679399559);


            var marker_ef2fa80060c883e930db51a7fb2399cf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_0f630b606af0150713437f16c0eba879 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_ef2fa80060c883e930db51a7fb2399cf.setIcon(icon_0f630b606af0150713437f16c0eba879);


            var marker_95a789ff1c1867efc53540af8910ccdd = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_e0c97543bf8755ade1f6249efd380bb9 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_95a789ff1c1867efc53540af8910ccdd.setIcon(icon_e0c97543bf8755ade1f6249efd380bb9);


            var marker_3f077b03e537d128163b5c86927feadf = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_030810f0e1972c7cbbfd2765fa19299c = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_3f077b03e537d128163b5c86927feadf.setIcon(icon_030810f0e1972c7cbbfd2765fa19299c);


            var marker_8d8d8576bfb12f4c3f0ac3750d18e505 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_8d8d8576bfb12f4c3f0ac3750d18e505.setIcon(icon_3af53ab08cd7ffa6ee13b2e2bb24ebf0);


            var marker_4968ba53f5d12369310ccf9994be42b8 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91a4b514ce940d93d1511ae5ebb60c47 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_4968ba53f5d12369310ccf9994be42b8.setIcon(icon_91a4b514ce940d93d1511ae5ebb60c47);


            var marker_edf1b4d1c86b086042fdb78a6a50309c = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_a1add5061d056faaf5a3e799ba9ae555 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_edf1b4d1c86b086042fdb78a6a50309c.setIcon(icon_a1add5061d056faaf5a3e799ba9ae555);


            var marker_aec1ac925773a73c70fb629ce7d03ad2 = L.marker(
                [28.6080585, -80.6039558],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_91148a541330e5590009d961d5206cfe = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_aec1ac925773a73c70fb629ce7d03ad2.setIcon(icon_91148a541330e5590009d961d5206cfe);


            var marker_0b2d624e4a25e562d789b120d4f279da = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_559e6106fc82969e429355fd2bd8dfe8 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_0b2d624e4a25e562d789b120d4f279da.setIcon(icon_559e6106fc82969e429355fd2bd8dfe8);


            var marker_261c349094a575034ead3af7f7919807 = L.marker(
                [28.5618571, -80.577366],
                {}
            ).addTo(marker_cluster_f116777a8f89526d71ccfa29da685556);


            var icon_70ceb3c0a910779f13a144b4550f3670 = L.AwesomeMarkers.icon(
                {&quot;extraClasses&quot;: &quot;fa-rotate-0&quot;, &quot;icon&quot;: &quot;info-sign&quot;, &quot;iconColor&quot;: &quot;green&quot;, &quot;markerColor&quot;: &quot;white&quot;, &quot;prefix&quot;: &quot;glyphicon&quot;}
            );
            marker_261c349094a575034ead3af7f7919807.setIcon(icon_70ceb3c0a910779f13a144b4550f3670);


            var mouse_position_578c12ce1a7601adb7ba34cc116e3a90 = new L.Control.MousePosition(
                {&quot;emptyString&quot;: &quot;NaN&quot;, &quot;lngFirst&quot;: false, &quot;numDigits&quot;: 20, &quot;position&quot;: &quot;topright&quot;, &quot;prefix&quot;: &quot;Lat:&quot;, &quot;separator&quot;: &quot; Long: &quot;}
            );
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;latFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            mouse_position_578c12ce1a7601adb7ba34cc116e3a90.options[&quot;lngFormatter&quot;] =
                function(num) {return L.Util.formatNum(num, 5);};;
            map_d368eaa66911ac76cdc66071a54e7a4a.addControl(mouse_position_578c12ce1a7601adb7ba34cc116e3a90);


            var marker_a69c351acdf14dc6d859390a75f1ffb5 = L.marker(
                [34.63617, -120.62503],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_637f720d8f3dcf7801e231070b2d59d5 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003e      1.38 KM\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_a69c351acdf14dc6d859390a75f1ffb5.setIcon(div_icon_637f720d8f3dcf7801e231070b2d59d5);


            var poly_line_5a43e3e10525cc8ef1a06b7f77be75fd = L.polyline(
                [[34.632093, -120.610829], [34.63617, -120.62503]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 1}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var marker_fde4ba4d82d5bbe403ecf13098aef8f9 = L.marker(
                [33.91, -118.19],
                {}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);


            var div_icon_ab5c885be371322271aeb45185109a79 = L.divIcon({&quot;className&quot;: &quot;empty&quot;, &quot;html&quot;: &quot;\u003cdiv style=\&quot;font-size: 12; color:#d35400;\&quot;\u003e\u003cb\u003e    236.56 KM\u003c/b\u003e\u003c/div\u003e&quot;, &quot;iconAnchor&quot;: [0, 0], &quot;iconSize&quot;: [20, 20]});
            marker_fde4ba4d82d5bbe403ecf13098aef8f9.setIcon(div_icon_ab5c885be371322271aeb45185109a79);


            var poly_line_30fb977050f7f4756b1ad2af3001904e = L.polyline(
                [[34.632093, -120.610829], [33.91, -118.19]],
                {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: false, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;noClip&quot;: false, &quot;opacity&quot;: 1.0, &quot;smoothFactor&quot;: 1.0, &quot;stroke&quot;: true, &quot;weight&quot;: 1}
            ).addTo(map_d368eaa66911ac76cdc66071a54e7a4a);

&lt;/script&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python

```

After you plot distance lines to the proximities, you can answer the following questions easily:

*   Are launch sites in close proximity to railways?
*   Are launch sites in close proximity to highways?
*   Are launch sites in close proximity to coastline?
*   Do launch sites keep certain distance away from cities?

Also please try to explain your findings.


# Next Steps:

Now you have discovered many interesting insights related to the launch sites' location using folium, in a very interactive way. Next, you will need to build a dashboard using Ploty Dash on detailed launch records.


## Authors


[Yan Luo](https://www.linkedin.com/in/yan-luo-96288783/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01)


### Other Contributors


Joseph Santarcangelo


## Change Log


| Date (YYYY-MM-DD) | Version | Changed By | Change Description          |
| ----------------- | ------- | ---------- | --------------------------- |
| 2021-05-26        | 1.0     | Yan        | Created the initial version |


Copyright © 2021 IBM Corporation. All rights reserved.


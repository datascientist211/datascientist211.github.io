```python
# andas is a software library written for the Python programming language for data manipulation and analysis.
import pandas as pd
#NumPy is a library for the Python programming language, adding support for large, multi-dimensional arrays and matrices, along with a large collection of high-level mathematical functions to operate on these arrays
import numpy as np
# Matplotlib is a plotting library for python and pyplot gives us a MatLab like plotting framework. We will use this in our plotter function to plot data.
import matplotlib.pyplot as plt
#Seaborn is a Python data visualization library based on matplotlib. It provides a high-level interface for drawing attractive and informative statistical graphics
import seaborn as sns

```


```python
spacex_df=pd.read_csv("spacex_launch_dash.csv")
spacex_df.head(5)
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
      <th>Unnamed: 0</th>
      <th>Flight Number</th>
      <th>Launch Site</th>
      <th>class</th>
      <th>Payload Mass (kg)</th>
      <th>Booster Version</th>
      <th>Booster Version Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>1</td>
      <td>CCAFS LC-40</td>
      <td>0</td>
      <td>0.0</td>
      <td>F9 v1.0  B0003</td>
      <td>v1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2</td>
      <td>CCAFS LC-40</td>
      <td>0</td>
      <td>0.0</td>
      <td>F9 v1.0  B0004</td>
      <td>v1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>3</td>
      <td>CCAFS LC-40</td>
      <td>0</td>
      <td>525.0</td>
      <td>F9 v1.0  B0005</td>
      <td>v1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4</td>
      <td>CCAFS LC-40</td>
      <td>0</td>
      <td>500.0</td>
      <td>F9 v1.0  B0006</td>
      <td>v1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>5</td>
      <td>CCAFS LC-40</td>
      <td>0</td>
      <td>677.0</td>
      <td>F9 v1.0  B0007</td>
      <td>v1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
launch_sites_df = spacex_df.groupby(['Launch Site'], as_index=False).first()
launch_sites_df = launch_sites_df[['Launch Site']]
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCAFS LC-40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CCAFS SLC-40</td>
    </tr>
    <tr>
      <th>2</th>
      <td>KSC LC-39A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VAFB SLC-4E</td>
    </tr>
  </tbody>
</table>
</div>




```python
dropdown_options = []
dropdown_options.append({'label': 'All Sites', 'value': 'ALL'})
for index, row in launch_sites_df.iterrows():
    dropdown_options.append({'label': row['Launch Site'], 'value': row['Launch Site']})
dropdown_options
```




    [{'label': 'All Sites', 'value': 'ALL'},
     {'label': 'CCAFS LC-40', 'value': 'CCAFS LC-40'},
     {'label': 'CCAFS SLC-40', 'value': 'CCAFS SLC-40'},
     {'label': 'KSC LC-39A', 'value': 'KSC LC-39A'},
     {'label': 'VAFB SLC-4E', 'value': 'VAFB SLC-4E'}]




```python
site_df = spacex_df[spacex_df['Launch Site']  == 'VAFB SLC-4E']
print(site_df.head())
site_df['class'].value_counts().values
```

        Unnamed: 0  Flight Number  Launch Site  class  Payload Mass (kg)  \
    26          26              6  VAFB SLC-4E      0              500.0   
    27          27             21  VAFB SLC-4E      0              553.0   
    28          28             29  VAFB SLC-4E      1             9600.0   
    29          29             37  VAFB SLC-4E      1             9600.0   
    30          30             40  VAFB SLC-4E      1              475.0   
    
       Booster Version Booster Version Category  
    26  F9 v1.1  B1003                     v1.1  
    27   F9 v1.1 B1017                     v1.1  
    28   F9 FT B1029.1                       FT  
    29   F9 FT B1036.1                       FT  
    30   F9 FT B1038.1                       FT  





    array([6, 4])




```python
max_payload_value = spacex_df['Payload Mass (kg)'].max()
min_payload_value = spacex_df['Payload Mass (kg)'].min()
print(min_payload_value)
print(max_payload_value)
```

    0.0
    9600.0



```python
import plotly.express as px
from jupyter_dash import JupyterDash
from dash import dcc
from dash import html
from dash.dependencies import Input, Output

# Build App
app = JupyterDash(__name__)
app.layout = html.Div([
    html.H1("SpaceX Launch Records Dashboard", style={'textAlign': 'center'}),
    html.Label([
        dcc.Dropdown(
            id='site-dropdown', 
            clearable=False,
            value='ALL', 
            options=dropdown_options
        )
    ]),
    html.Br(),
    html.Br(),
    html.Div(dcc.Graph(id='success-pie-chart')),
    html.Div(dcc.RangeSlider(id='payload-slider',
                             min=0, 
                             max=10000, 
                             step=1000,
                             marks={0: '0', 2500: '2500', 5000: '5000', 7500: '7500', 10000: '10000'},
                             value=[min_payload_value, max_payload_value])),
    
    html.Div(dcc.Graph(id='success-payload-scatter-chart'))
])

# Callback to update Pie chart
@app.callback(
    Output(component_id='success-pie-chart', component_property='figure'),
    Input(component_id='site-dropdown', component_property='value')
)
def get_pie_chart(entered_site):
    if entered_site == 'ALL':
        fig = px.pie(spacex_df, values='class', 
        names='Launch Site', 
        title='Total Success Launches By Site')
        return fig
    else:
        site_df = spacex_df[spacex_df['Launch Site']  == entered_site]
        fig = px.pie(values=site_df['class'].value_counts().values, 
                     names=['0', '1'], 
        title='Total Success Launches For Site' + entered_site)
        return fig

    
    
# Callback to update Scatter plot
@app.callback(
    Output(component_id='success-payload-scatter-chart', component_property='figure'),
    [Input(component_id='site-dropdown', component_property='value'), 
     Input(component_id='payload-slider', component_property='value')]
)
def get_scatter_plot(entered_site, entered_payload):
    title_all = 'Correlation between Payload and Success for '
    filtered_df = spacex_df[(spacex_df['Payload Mass (kg)'] > entered_payload[0]) & (spacex_df['Payload Mass (kg)'] < entered_payload[1])]
    if entered_site == 'ALL':
        fig = px.scatter(filtered_df, 
                         x='Payload Mass (kg)', 
                         y='class',
                         color='Booster Version Category', 
                         title = title_all + 'all Sites' + ' and ' + str(entered_payload[0]))
        return fig
    else:
        site_df = filtered_df[filtered_df['Launch Site']  == entered_site]
        fig = px.scatter(site_df, 
                         x='Payload Mass (kg)', 
                         y='class',
                         color='Booster Version Category', 
                         title = title_all + entered_site + ' and ' + str(entered_payload))
        return fig
    
# Run app and display result inline in the notebook

```


```python
app.run_server(mode='external')
```

    Dash app running on http://127.0.0.1:8050/



```python

```


```python

```


```python

```

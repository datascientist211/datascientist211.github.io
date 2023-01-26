<p style="text-align:center">
    <a href="https://skills.network/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01" target="_blank">
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/assets/logos/SN_web_lightmode.png" width="200" alt="Skills Network Logo"  />
    </a>
</p>

<h1 align=center><font size = 5>Assignment: SQL Notebook for Peer Assignment</font></h1>

Estimated time needed: **60** minutes.

## Introduction

Using this Python notebook you will:

1.  Understand the Spacex DataSet
2.  Load the dataset  into the corresponding table in a Db2 database
3.  Execute SQL queries to answer assignment questions


## Overview of the DataSet

SpaceX has gained worldwide attention for a series of historic milestones.

It is the only private company ever to return a spacecraft from low-earth orbit, which it first accomplished in December 2010.
SpaceX advertises Falcon 9 rocket launches on its website with a cost of 62 million dollars wheras other providers cost upward of 165 million dollars each, much of the savings is because Space X can reuse the first stage.

Therefore if we can determine if the first stage will land, we can determine the cost of a launch.

This information can be used if an alternate company wants to bid against SpaceX for a rocket launch.

This dataset includes a record for each payload carried during a SpaceX mission into outer space.


### Download the datasets

This assignment requires you to load the spacex dataset.

In many cases the dataset to be analyzed is available as a .CSV (comma separated values) file, perhaps on the internet. Click on the link below to download and save the dataset (.CSV file):

<a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/data/Spacex.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01" target="_blank">Spacex DataSet</a>


**Navigate to the Go to UI screen**

*   Refer to this insruction in this <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Sign%20up%20for%20IBM%20Cloud%20-%20Create%20Db2%20service%20instance%20-%20Get%20started%20with%20the%20Db2%20console/instructional-labs.md.html?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01">link</a> for viewing  the   Go to UI screen.

*   Later click on **Data link(below SQL)**  in the Go to UI screen  and click on **Load Data** tab.

*   Later browse for the downloaded spacex file.

<img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/images/browsefile.png" width="800"/>

*   Once done select the schema andload the file.

 <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/images/spacexload3.png" width="800"/>



```python
!pip install sqlalchemy==1.3.9
!pip install ibm_db_sa
!pip install ipython-sql
```

    Requirement already satisfied: sqlalchemy==1.3.9 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (1.3.9)
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip available: [0m[31;49m22.3[0m[39;49m -> [0m[32;49m22.3.1[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m
    Collecting ibm_db_sa
      Using cached ibm_db_sa-0.3.8-py3-none-any.whl (30 kB)
    Requirement already satisfied: sqlalchemy>=0.7.3 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ibm_db_sa) (1.3.9)
    Collecting ibm-db>=2.0.0
      Downloading ibm_db-3.1.4.tar.gz (1.4 MB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m1.4/1.4 MB[0m [31m7.5 MB/s[0m eta [36m0:00:00[0m00:01[0m00:01[0m
    [?25h  Installing build dependencies ... [?25ldone
    [?25h  Getting requirements to build wheel ... [?25lerror
      [1;31merror[0m: [1msubprocess-exited-with-error[0m
      
      [31m×[0m [32mGetting requirements to build wheel[0m did not run successfully.
      [31m│[0m exit code: [1;36m1[0m
      [31m╰─>[0m [31m[2 lines of output][0m
      [31m   [0m Detected 64-bit Python
      [31m   [0m Arm64 architecture is not supported. Please install intel-only (x64) version of python and then install ibm_db.
      [31m   [0m [31m[end of output][0m
      
      [1;35mnote[0m: This error originates from a subprocess, and is likely not a problem with pip.
    [1;31merror[0m: [1msubprocess-exited-with-error[0m
    
    [31m×[0m [32mGetting requirements to build wheel[0m did not run successfully.
    [31m│[0m exit code: [1;36m1[0m
    [31m╰─>[0m See above for output.
    
    [1;35mnote[0m: This error originates from a subprocess, and is likely not a problem with pip.
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip available: [0m[31;49m22.3[0m[39;49m -> [0m[32;49m22.3.1[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m
    [?25hRequirement already satisfied: ipython-sql in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (0.4.1)
    Requirement already satisfied: prettytable<1 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython-sql) (0.7.2)
    Requirement already satisfied: ipython>=1.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython-sql) (8.6.0)
    Requirement already satisfied: sqlalchemy>=0.6.7 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython-sql) (1.3.9)
    Requirement already satisfied: sqlparse in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython-sql) (0.4.3)
    Requirement already satisfied: six in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython-sql) (1.16.0)
    Requirement already satisfied: ipython-genutils>=0.1.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython-sql) (0.2.0)
    Requirement already satisfied: backcall in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (0.2.0)
    Requirement already satisfied: decorator in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (5.1.1)
    Requirement already satisfied: jedi>=0.16 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (0.18.1)
    Requirement already satisfied: matplotlib-inline in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (0.1.6)
    Requirement already satisfied: pickleshare in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (0.7.5)
    Requirement already satisfied: prompt-toolkit<3.1.0,>3.0.1 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (3.0.31)
    Requirement already satisfied: pygments>=2.4.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (2.13.0)
    Requirement already satisfied: stack-data in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (0.6.0)
    Requirement already satisfied: traitlets>=5 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (5.5.0)
    Requirement already satisfied: pexpect>4.3 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (4.8.0)
    Requirement already satisfied: appnope in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from ipython>=1.0->ipython-sql) (0.1.3)
    Requirement already satisfied: parso<0.9.0,>=0.8.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from jedi>=0.16->ipython>=1.0->ipython-sql) (0.8.3)
    Requirement already satisfied: ptyprocess>=0.5 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from pexpect>4.3->ipython>=1.0->ipython-sql) (0.7.0)
    Requirement already satisfied: wcwidth in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from prompt-toolkit<3.1.0,>3.0.1->ipython>=1.0->ipython-sql) (0.2.5)
    Requirement already satisfied: executing>=1.2.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from stack-data->ipython>=1.0->ipython-sql) (1.2.0)
    Requirement already satisfied: asttokens>=2.1.0 in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from stack-data->ipython>=1.0->ipython-sql) (2.1.0)
    Requirement already satisfied: pure-eval in /Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages (from stack-data->ipython>=1.0->ipython-sql) (0.2.2)
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip available: [0m[31;49m22.3[0m[39;49m -> [0m[32;49m22.3.1[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m


### Connect to the database

Let us first load the SQL extension and establish a connection with the database



```python
%load_ext sql
```

**DB2 magic in case of old UI service credentials.**

In the next cell enter your db2 connection string. Recall you created Service Credentials for your Db2 instance before. From the **uri** field of your Db2 service credentials copy everything after db2:// (except the double quote at the end) and paste it in the cell below after ibm_db_sa://

<img src ="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_edX/images/URI.jpg">

in the following format

**%sql ibm_db_sa://my-username:my-password\@my-hostname:my-port/my-db-name**

**DB2 magic in case of new UI service credentials.**

<img src ="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/images/servicecredentials.png" width=600>  

*   Use the following format.

*   Add security=SSL at the end

**%sql ibm_db_sa://my-username:my-password\@my-hostname:my-port/my-db-name?security=SSL**



```python

```


```python
import pandas as pd
import sqlite3

#connect to a database
conn = sqlite3.connect("Spacex.db") #if the db does not exist, this creates a Any_Database_Name.db file in the current directory

cursor = conn.cursor()
```


```python

```


```python

```

## Tasks

Now write and execute SQL queries to solve the assignment tasks.

### Task 1

##### Display the names of the unique launch sites  in the space mission



```python
cursor.execute('SELECT DISTINCT Launch_Site FROM Spacex')
cursor.fetchall()
```




    [('CCAFS LC-40',), ('VAFB SLC-4E',), ('KSC LC-39A',), ('CCAFS SLC-40',)]



### Task 2

##### Display 5 records where launch sites begin with the string 'CCA'



```python
sql_string = "SELECT * FROM Spacex WHERE Launch_Site LIKE 'CCA%' LIMIT 5"
df = pd.read_sql(sql_string, conn)
df
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
      <th>index</th>
      <th>Date</th>
      <th>Time (UTC)</th>
      <th>Booster_Version</th>
      <th>Launch_Site</th>
      <th>Payload</th>
      <th>PAYLOAD_MASS__KG_</th>
      <th>Orbit</th>
      <th>Customer</th>
      <th>Mission_Outcome</th>
      <th>Landing _Outcome</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>04-06-2010</td>
      <td>18:45:00</td>
      <td>F9 v1.0  B0003</td>
      <td>CCAFS LC-40</td>
      <td>Dragon Spacecraft Qualification Unit</td>
      <td>0</td>
      <td>LEO</td>
      <td>SpaceX</td>
      <td>Success</td>
      <td>Failure (parachute)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>08-12-2010</td>
      <td>15:43:00</td>
      <td>F9 v1.0  B0004</td>
      <td>CCAFS LC-40</td>
      <td>Dragon demo flight C1, two CubeSats, barrel of...</td>
      <td>0</td>
      <td>LEO (ISS)</td>
      <td>NASA (COTS) NRO</td>
      <td>Success</td>
      <td>Failure (parachute)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>22-05-2012</td>
      <td>07:44:00</td>
      <td>F9 v1.0  B0005</td>
      <td>CCAFS LC-40</td>
      <td>Dragon demo flight C2</td>
      <td>525</td>
      <td>LEO (ISS)</td>
      <td>NASA (COTS)</td>
      <td>Success</td>
      <td>No attempt</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>08-10-2012</td>
      <td>00:35:00</td>
      <td>F9 v1.0  B0006</td>
      <td>CCAFS LC-40</td>
      <td>SpaceX CRS-1</td>
      <td>500</td>
      <td>LEO (ISS)</td>
      <td>NASA (CRS)</td>
      <td>Success</td>
      <td>No attempt</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>01-03-2013</td>
      <td>15:10:00</td>
      <td>F9 v1.0  B0007</td>
      <td>CCAFS LC-40</td>
      <td>SpaceX CRS-2</td>
      <td>677</td>
      <td>LEO (ISS)</td>
      <td>NASA (CRS)</td>
      <td>Success</td>
      <td>No attempt</td>
    </tr>
  </tbody>
</table>
</div>



### Task 3

##### Display the total payload mass carried by boosters launched by NASA (CRS)



```python
cursor.execute("SELECT sum(PAYLOAD_MASS__KG_) FROM Spacex WHERE Customer LIKE 'NASA (CRS)%'")
cursor.fetchall()
```




    [(48213,)]



### Task 4

##### Display average payload mass carried by booster version F9 v1.1



```python
cursor.execute("SELECT avg(PAYLOAD_MASS__KG_) FROM Spacex WHERE Booster_Version LIKE 'F9 v1.1%'")
cursor.fetchall()
```




    [(2534.6666666666665,)]



### Task 5

##### List the date when the first successful landing outcome in ground pad was acheived.

*Hint:Use min function*



```python
sql_string = "SELECT min(Date) FROM Spacex WHERE [Landing _Outcome] LIKE 'Success (ground pad)'"
cursor.execute(sql_string)
cursor.fetchall()
```




    [('01-05-2017',)]



### Task 6

##### List the names of the boosters which have success in drone ship and have payload mass greater than 4000 but less than 6000



```python
sql_string = "SELECT DISTINCT Booster_Version FROM Spacex WHERE [Landing _Outcome] LIKE 'Success (drone ship)' AND PAYLOAD_MASS__KG_ > 4000 AND PAYLOAD_MASS__KG_ < 6000"

cursor.execute(sql_string)
cursor.fetchall()
```




    [('F9 FT B1022',), ('F9 FT B1026',), ('F9 FT  B1021.2',), ('F9 FT  B1031.2',)]



### Task 7

##### List the total number of successful and failure mission outcomes



```python
sql_string ="SELECT (SELECT count([Landing _Outcome]) FROM Spacex WHERE [Landing _Outcome] LIKE 'Success%') as Success, (SELECT count([Landing _Outcome]) FROM Spacex WHERE [Landing _Outcome] NOT LIKE 'Success%') as Failure FROM Spacex"
cursor.execute(sql_string)
cursor.fetchone()
```




    (61, 40)



### Task 8

##### List the   names of the booster_versions which have carried the maximum payload mass. Use a subquery



```python
sql_string = "SELECT DISTINCT Booster_Version FROM Spacex WHERE PAYLOAD_MASS__KG_ = (SELECT max(PAYLOAD_MASS__KG_) FROM Spacex)"

cursor.execute(sql_string)
cursor.fetchall()
```




    [('F9 B5 B1048.4',),
     ('F9 B5 B1049.4',),
     ('F9 B5 B1051.3',),
     ('F9 B5 B1056.4',),
     ('F9 B5 B1048.5',),
     ('F9 B5 B1051.4',),
     ('F9 B5 B1049.5',),
     ('F9 B5 B1060.2 ',),
     ('F9 B5 B1058.3 ',),
     ('F9 B5 B1051.6',),
     ('F9 B5 B1060.3',),
     ('F9 B5 B1049.7 ',)]



### Task 9

##### List the failed landing_outcomes in drone ship, their booster versions, and launch site names for in year 2015



```python
sql_string = "SELECT DISTINCT Booster_Version, Launch_Site FROM Spacex WHERE [Landing _Outcome] LIKE 'Failure (drone ship)%' AND Date LIKE '%2015'"
cursor.execute(sql_string)
cursor.fetchall()
```




    [('F9 v1.1 B1012', 'CCAFS LC-40'), ('F9 v1.1 B1015', 'CCAFS LC-40')]



### Task 10

##### Rank the count of landing outcomes (such as Failure (drone ship) or Success (ground pad)) between the date 2010-06-04 and 2017-03-20, in descending order



```python
sql_string = "SELECT [Landing _Outcome], COUNT([Landing _Outcome]) FROM Spacex WHERE Date BETWEEN '04-06-2010' AND '20-03-2017' GROUP BY [Landing _Outcome] ORDER BY [Landing _Outcome] DESC"

cursor.execute(sql_string)

cursor.fetchall()
```




    [('Success (ground pad)', 6),
     ('Success (drone ship)', 8),
     ('Success', 20),
     ('No attempt ', 1),
     ('No attempt', 10),
     ('Failure (parachute)', 2),
     ('Failure (drone ship)', 4),
     ('Failure', 3),
     ('Controlled (ocean)', 3)]




```python
# Be sure to close the connection
conn.close()
```

### Reference Links

*   <a href ="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20String%20Patterns%20-%20Sorting%20-%20Grouping/instructional-labs.md.html?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01&origin=www.coursera.org">Hands-on Lab : String Patterns, Sorting and Grouping</a>

*   <a  href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Built-in%20functions%20/Hands-on_Lab__Built-in_Functions.md.html?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01&origin=www.coursera.org">Hands-on Lab: Built-in functions</a>

*   <a  href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Sub-queries%20and%20Nested%20SELECTs%20/instructional-labs.md.html?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01&origin=www.coursera.org">Hands-on Lab : Sub-queries and Nested SELECT Statements</a>

*   <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/DB0201EN-Week3-1-3-SQLmagic.ipynb?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01">Hands-on Tutorial: Accessing Databases with SQL magic</a>

*   <a href= "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/DB0201EN-Week3-1-4-Analyzing.ipynb?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDS0321ENSkillsNetwork26802033-2022-01-01">Hands-on Lab: Analyzing a real World Data Set</a>


## Author(s)

<h4> Lakshmi Holla </h4>


## Other Contributors

<h4> Rav Ahuja </h4>


## Change log

| Date       | Version | Changed by    | Change Description        |
| ---------- | ------- | ------------- | ------------------------- |
| 2021-10-12 | 0.4     | Lakshmi Holla | Changed markdown          |
| 2021-08-24 | 0.3     | Lakshmi Holla | Added library update      |
| 2021-07-09 | 0.2     | Lakshmi Holla | Changes made in magic sql |
| 2021-05-20 | 0.1     | Lakshmi Holla | Created Initial Version   |


## <h3 align="center"> © IBM Corporation 2021. All rights reserved. <h3/>


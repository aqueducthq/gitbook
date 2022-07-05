# Workflow Parameters Tutorial


This is a quick tutorial that will demonstrate how workflows can be parameterized with Aqeuduct.



```python
import aqueduct 
from aqueduct.decorator import op

api_key = "<your_api_key>"
address = "<your_server_address>"
client = aqueduct.Client(api_key, address)
```

--- 

## The Basics

A parameter acts exactly like any other Aqueduct artifact. It can be fed as a typical artifact input to any operator. Every parameter must have both a name and a default value, which we will use if not overriding value is provided.

In the example below, we will attempt to filter a table based on the value of a specific column ("reviewer_nationality"). The value we filter on will be parameterized.


```python
db = client.integration("aqueduct_demo")
reviews_table = db.sql("select * from hotel_reviews;")
```


```python
reviews_table.get()
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
      <th>hotel_name</th>
      <th>review_date</th>
      <th>reviewer_nationality</th>
      <th>review</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>H10 Itaca</td>
      <td>2017-08-03</td>
      <td>Australia</td>
      <td>Damaged bathroom shower screen sealant and ti...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>De Vere Devonport House</td>
      <td>2016-03-28</td>
      <td>United Kingdom</td>
      <td>No Negative The location and the hotel was ver...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ramada Plaza Milano</td>
      <td>2016-05-15</td>
      <td>Kosovo</td>
      <td>No Negative Im a frequent traveler i visited m...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aloft London Excel</td>
      <td>2016-11-05</td>
      <td>Canada</td>
      <td>Only tepid water for morning shower They said ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Student Hotel Amsterdam City</td>
      <td>2016-07-31</td>
      <td>Australia</td>
      <td>No Negative The hotel had free gym table tenni...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>The Chesterfield Mayfair</td>
      <td>2015-08-25</td>
      <td>Denmark</td>
      <td>Bad Reading light And light in bathNo Positive</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Hotel V Nesplein</td>
      <td>2015-08-27</td>
      <td>Turkey</td>
      <td>Nothing except the construction going on the s...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Le Parisis Paris Tour Eiffel</td>
      <td>2015-10-20</td>
      <td>Australia</td>
      <td>When we arrived we had to bring our own baggag...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NH Amsterdam Museum Quarter</td>
      <td>2016-01-26</td>
      <td>Belgium</td>
      <td>No stairs even to go the first floor Restaura...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Barcel Raval</td>
      <td>2017-07-07</td>
      <td>United Kingdom</td>
      <td>Air conditioning a little zealous Nice atmosp...</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 4 columns</p>
</div>


```python
import pandas as pd

@op
def strip_whitespace_from_nationality(df: pd.DataFrame):
    df["reviewer_nationality"] = df["reviewer_nationality"].str.strip(" ")
    return df
    
@op
def filter_by_nationality(df: pd.DataFrame, nationality: str):
    return df[df["reviewer_nationality"] == nationality]
```

---
Here we filter the reviews table to only the rows where "reviewer_nationality" is equal to our parameter value, which currently defaults to "United Kingdom".

```python
# Every parameter must have a default value that we can use.
nationality_param = client.create_param("nationality", default="United Kingdom")

formatted_table = strip_whitespace_from_nationality(reviews_table)
filtered = filter_by_nationality(formatted_table, nationality_param)
filtered.get().head(10)
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
      <th>hotel_name</th>
      <th>review_date</th>
      <th>reviewer_nationality</th>
      <th>review</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>De Vere Devonport House</td>
      <td>2016-03-28</td>
      <td>United Kingdom</td>
      <td>No Negative The location and the hotel was ver...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Crowne Plaza London Docklands</td>
      <td>2017-01-23</td>
      <td>United Kingdom</td>
      <td>Lighting in hotel room wasn t the best was ve...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>London Marriott Hotel Marble Arch</td>
      <td>2016-02-23</td>
      <td>United Kingdom</td>
      <td>No Negative The whole experience was excellent...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Grand Royale London Hyde Park</td>
      <td>2017-01-04</td>
      <td>United Kingdom</td>
      <td>see above window looking out on the rough sid...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Cavendish London</td>
      <td>2016-02-02</td>
      <td>United Kingdom</td>
      <td>Poor pillows for sleeping good for watching t...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>San Domenico House</td>
      <td>2016-06-13</td>
      <td>United Kingdom</td>
      <td>The coffee and drinks are quite expensive but ...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Radisson Blu Edwardian Grafton</td>
      <td>2016-05-18</td>
      <td>United Kingdom</td>
      <td>Pillows hard Evening staff on desk could not ...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Holiday Inn London Kensington</td>
      <td>2016-10-04</td>
      <td>United Kingdom</td>
      <td>Put in a disabled room when we weren t disabl...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>The Hoxton Holborn</td>
      <td>2016-05-25</td>
      <td>United Kingdom</td>
      <td>The loud music in the bar area in the evening...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Park Plaza County Hall London</td>
      <td>2016-10-27</td>
      <td>United Kingdom</td>
      <td>Breakfast seating too cramped Not enough spac...</td>
    </tr>
  </tbody>
</table>
</div>

---
Since we've already parameterized the workflow, we can provide a different parameter value and see the new results immediately!

```python
filtered.get(parameters={"nationality": "Australia"}).head(10)
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
      <th>hotel_name</th>
      <th>review_date</th>
      <th>reviewer_nationality</th>
      <th>review</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>H10 Itaca</td>
      <td>2017-08-03</td>
      <td>Australia</td>
      <td>Damaged bathroom shower screen sealant and ti...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>The Student Hotel Amsterdam City</td>
      <td>2016-07-31</td>
      <td>Australia</td>
      <td>No Negative The hotel had free gym table tenni...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Les Jardins Du Marais</td>
      <td>2015-10-27</td>
      <td>Australia</td>
      <td>Bathroom is fine but could be improved with a...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Napoleon Paris</td>
      <td>2015-10-07</td>
      <td>Australia</td>
      <td>NOTHING EVERYTHING</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NH Milano Touring</td>
      <td>2015-08-09</td>
      <td>Australia</td>
      <td>Check in and check out were extemely slow wit...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Le Parisis Paris Tour Eiffel</td>
      <td>2015-10-20</td>
      <td>Australia</td>
      <td>When we arrived we had to bring our own baggag...</td>
    </tr>
  </tbody>
</table>
</div>



---
## Publishing a Parameterized Flow

When a flow is published on a schedule, the recurring run will continously execute with the default parameters. You may trigger additional runs with different parameters using the `client.trigger()` method. However, in order to change a parameter's value in perpetuity, you must rerun `create_param()` with an updated default value.



```python
flow = client.publish_flow("Parameter Example", artifacts = [filtered])
print(flow.id())
```


```python
client.trigger(flow.id(), parameters={"nationality": "Australia"})
```

---
### Parameterizing SQL Queries

SQL queries can also be parameterized. For queries, we'll use the double-bracket syntax to denote the presence of a parameter inline. As long as the name of the parameter matches a previously defined 

Here's a trivial example of parameterizing the table the query is selecting from.

NOTE: unlike parameters for other operators, parameters for SQL queries cannot be space-delimited.


```python
client.create_param("table_name", default="hotel_reviews")

table = db.sql("select * from {{ table_name }}")
table.get()
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
      <th>hotel_name</th>
      <th>review_date</th>
      <th>reviewer_nationality</th>
      <th>review</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>H10 Itaca</td>
      <td>2017-08-03</td>
      <td>Australia</td>
      <td>Damaged bathroom shower screen sealant and ti...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>De Vere Devonport House</td>
      <td>2016-03-28</td>
      <td>United Kingdom</td>
      <td>No Negative The location and the hotel was ver...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ramada Plaza Milano</td>
      <td>2016-05-15</td>
      <td>Kosovo</td>
      <td>No Negative Im a frequent traveler i visited m...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aloft London Excel</td>
      <td>2016-11-05</td>
      <td>Canada</td>
      <td>Only tepid water for morning shower They said ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Student Hotel Amsterdam City</td>
      <td>2016-07-31</td>
      <td>Australia</td>
      <td>No Negative The hotel had free gym table tenni...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>The Chesterfield Mayfair</td>
      <td>2015-08-25</td>
      <td>Denmark</td>
      <td>Bad Reading light And light in bathNo Positive</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Hotel V Nesplein</td>
      <td>2015-08-27</td>
      <td>Turkey</td>
      <td>Nothing except the construction going on the s...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Le Parisis Paris Tour Eiffel</td>
      <td>2015-10-20</td>
      <td>Australia</td>
      <td>When we arrived we had to bring our own baggag...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NH Amsterdam Museum Quarter</td>
      <td>2016-01-26</td>
      <td>Belgium</td>
      <td>No stairs even to go the first floor Restaura...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Barcel Raval</td>
      <td>2017-07-07</td>
      <td>United Kingdom</td>
      <td>Air conditioning a little zealous Nice atmosp...</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 4 columns</p>
</div>




```python
table.get(parameters={"table_name": "customer_activity"})
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
      <th>contract</th>
      <th>payment_method</th>
      <th>tenure</th>
      <th>monthly_charges</th>
      <th>total_charges</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Month-to-month</td>
      <td>Electronic check</td>
      <td>1</td>
      <td>29.85</td>
      <td>29.85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>One year</td>
      <td>Mailed check</td>
      <td>34</td>
      <td>56.95</td>
      <td>1889.50</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Month-to-month</td>
      <td>Mailed check</td>
      <td>2</td>
      <td>53.85</td>
      <td>108.15</td>
    </tr>
    <tr>
      <th>3</th>
      <td>One year</td>
      <td>Bank transfer (automatic)</td>
      <td>45</td>
      <td>42.30</td>
      <td>1840.75</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Month-to-month</td>
      <td>Electronic check</td>
      <td>2</td>
      <td>70.70</td>
      <td>151.65</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7027</th>
      <td>One year</td>
      <td>Mailed check</td>
      <td>24</td>
      <td>84.80</td>
      <td>1990.50</td>
    </tr>
    <tr>
      <th>7028</th>
      <td>One year</td>
      <td>Credit card (automatic)</td>
      <td>72</td>
      <td>103.20</td>
      <td>7362.90</td>
    </tr>
    <tr>
      <th>7029</th>
      <td>Month-to-month</td>
      <td>Electronic check</td>
      <td>11</td>
      <td>29.60</td>
      <td>346.45</td>
    </tr>
    <tr>
      <th>7030</th>
      <td>Month-to-month</td>
      <td>Mailed check</td>
      <td>4</td>
      <td>74.40</td>
      <td>306.60</td>
    </tr>
    <tr>
      <th>7031</th>
      <td>Two year</td>
      <td>Bank transfer (automatic)</td>
      <td>66</td>
      <td>105.65</td>
      <td>6844.50</td>
    </tr>
  </tbody>
</table>
<p>7032 rows × 5 columns</p>
</div>



---
### Builtin SQL Parameters

There are also a number of builtin parameter tags that we support for you! See <link> for a list of all of them.

Below is an example of the fairly self-explanatory "today" tag:


```python
# This will be empty because all records are historical.
reviews_after_today = db.sql("select * from hotel_reviews where review_date > {{ today }}")
reviews_after_today.get()
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
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
reviews_before_today = db.sql("select * from hotel_reviews where review_date < {{ today }}")
reviews_before_today.get()
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
      <th>hotel_name</th>
      <th>review_date</th>
      <th>reviewer_nationality</th>
      <th>review</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>H10 Itaca</td>
      <td>2017-08-03</td>
      <td>Australia</td>
      <td>Damaged bathroom shower screen sealant and ti...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>De Vere Devonport House</td>
      <td>2016-03-28</td>
      <td>United Kingdom</td>
      <td>No Negative The location and the hotel was ver...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ramada Plaza Milano</td>
      <td>2016-05-15</td>
      <td>Kosovo</td>
      <td>No Negative Im a frequent traveler i visited m...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aloft London Excel</td>
      <td>2016-11-05</td>
      <td>Canada</td>
      <td>Only tepid water for morning shower They said ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Student Hotel Amsterdam City</td>
      <td>2016-07-31</td>
      <td>Australia</td>
      <td>No Negative The hotel had free gym table tenni...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>The Chesterfield Mayfair</td>
      <td>2015-08-25</td>
      <td>Denmark</td>
      <td>Bad Reading light And light in bathNo Positive</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Hotel V Nesplein</td>
      <td>2015-08-27</td>
      <td>Turkey</td>
      <td>Nothing except the construction going on the s...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Le Parisis Paris Tour Eiffel</td>
      <td>2015-10-20</td>
      <td>Australia</td>
      <td>When we arrived we had to bring our own baggag...</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NH Amsterdam Museum Quarter</td>
      <td>2016-01-26</td>
      <td>Belgium</td>
      <td>No stairs even to go the first floor Restaura...</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Barcel Raval</td>
      <td>2017-07-07</td>
      <td>United Kingdom</td>
      <td>Air conditioning a little zealous Nice atmosp...</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 4 columns</p>
</div>






<!-- ------------- New Cell ------------ -->


# Sentiment Analysis using Deep Learning

This is a short example of how to use the Aqueduct Platform to deploy a sentiment analysis model.




<!-- ------------- New Cell ------------ -->


```python
import aqueduct 
from aqueduct.decorator import op, check
```




<!-- ------------- New Cell ------------ -->


```python
api_key = "<your_api_key>"

# You can use localhost if you're running this notebook on the same machine as your server.
address = "<your_server_address>"
```




<!-- ------------- New Cell ------------ -->


```python
client = aqueduct.Client(api_key, address)
```




<!-- ------------- New Cell ------------ -->


## Getting the Input Data




<!-- ------------- New Cell ------------ -->


```python
warehouse = client.integration("aqueduct_demo")
reviews_table = warehouse.sql("select * from hotel_reviews;")
```




<!-- ------------- New Cell ------------ -->


```python
reviews_table.get()
```
**Output:**
<div>

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




<!-- ------------- New Cell ------------ -->


## Applying the Model

Here we use the HuggingFace Transformers package to apply a standard sentiment analysis model to our data.




<!-- ------------- New Cell ------------ -->


```python
from transformers import pipeline
import pandas as pd
import torch # this is needed to ensure that pytorch is installed.

@op()
def sentiment_prediction(reviews):
    model = pipeline("sentiment-analysis")
    return reviews.join(pd.DataFrame(model(list(reviews['review']))))
```




<!-- ------------- New Cell ------------ -->


```python
sentiment_table = sentiment_prediction(reviews_table)
```




<!-- ------------- New Cell ------------ -->


Getting a preview of the results.




<!-- ------------- New Cell ------------ -->


```python
sentiment_table.get()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hotel_name</th>
      <th>review_date</th>
      <th>reviewer_nationality</th>
      <th>review</th>
      <th>label</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>H10 Itaca</td>
      <td>2017-08-03</td>
      <td>Australia</td>
      <td>Damaged bathroom shower screen sealant and ti...</td>
      <td>POSITIVE</td>
      <td>0.715812</td>
    </tr>
    <tr>
      <th>1</th>
      <td>De Vere Devonport House</td>
      <td>2016-03-28</td>
      <td>United Kingdom</td>
      <td>No Negative The location and the hotel was ver...</td>
      <td>POSITIVE</td>
      <td>0.999741</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ramada Plaza Milano</td>
      <td>2016-05-15</td>
      <td>Kosovo</td>
      <td>No Negative Im a frequent traveler i visited m...</td>
      <td>POSITIVE</td>
      <td>0.999773</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aloft London Excel</td>
      <td>2016-11-05</td>
      <td>Canada</td>
      <td>Only tepid water for morning shower They said ...</td>
      <td>NEGATIVE</td>
      <td>0.999169</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Student Hotel Amsterdam City</td>
      <td>2016-07-31</td>
      <td>Australia</td>
      <td>No Negative The hotel had free gym table tenni...</td>
      <td>NEGATIVE</td>
      <td>0.931378</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
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
      <td>NEGATIVE</td>
      <td>0.999340</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Hotel V Nesplein</td>
      <td>2015-08-27</td>
      <td>Turkey</td>
      <td>Nothing except the construction going on the s...</td>
      <td>POSITIVE</td>
      <td>0.999691</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Le Parisis Paris Tour Eiffel</td>
      <td>2015-10-20</td>
      <td>Australia</td>
      <td>When we arrived we had to bring our own baggag...</td>
      <td>NEGATIVE</td>
      <td>0.999032</td>
    </tr>
    <tr>
      <th>98</th>
      <td>NH Amsterdam Museum Quarter</td>
      <td>2016-01-26</td>
      <td>Belgium</td>
      <td>No stairs even to go the first floor Restaura...</td>
      <td>POSITIVE</td>
      <td>0.996806</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Barcel Raval</td>
      <td>2017-07-07</td>
      <td>United Kingdom</td>
      <td>Air conditioning a little zealous Nice atmosp...</td>
      <td>POSITIVE</td>
      <td>0.999748</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 6 columns</p>
</div>




<!-- ------------- New Cell ------------ -->


## Publishing the Workflow
Saving the predictions back to the data warehouse.




<!-- ------------- New Cell ------------ -->


```python
sentiment_table.save(warehouse.config(table='sentiment_pred', update_mode='replace'))
```




<!-- ------------- New Cell ------------ -->


Publishing the flow to run every hour.




<!-- ------------- New Cell ------------ -->


```python
sentiment_flow = client.publish_flow(name = "Demo Customer Sentiment", 
                                   artifacts = [sentiment_table],
                                   schedule = aqueduct.hourly())
print(sentiment_flow.id())
```




<!-- ------------- New Cell ------------ -->





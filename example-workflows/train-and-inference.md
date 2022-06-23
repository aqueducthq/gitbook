


<!-- ------------- New Cell ------------ -->


# Training and Inference in a Single Workflow

In this demo, we train and use multiple models to impute missing values.  We start with a dataset of wines consisting of key features like acidity. Some of the records are missing feature values. In addition, wine quality scores are given to some but not all of the wines. 

We will build a workflow that trains a linear model to impute the missing features from the other features and then train a decision tree model to rate the un-rated wines using the imputed features. 




<!-- ------------- New Cell ------------ -->


```python
import pandas as pd
import aqueduct 
from aqueduct import op, check, metric
```




<!-- ------------- New Cell ------------ -->


First we connect to the locally running Aqueduct server using our `api_key`.  If you forgot your `api_key`, the following command will get it for you:




<!-- ------------- New Cell ------------ -->


```python
%%capture output
!aqueduct apikey
```




<!-- ------------- New Cell ------------ -->


```python
# You can use `localhost` if you're running this notebook on the same machine as the server.
address = "localhost:8080" 
api_key = output.stdout.strip()
client = aqueduct.Client(api_key, address)
```




<!-- ------------- New Cell ------------ -->


## Getting the Demo Data 

In this demo, we will use the wine table in the demo data warehouse.




<!-- ------------- New Cell ------------ -->


```python
demodb = client.integration('aqueduct_demo')
wines = demodb.sql("select * from wine;")
```




<!-- ------------- New Cell ------------ -->


```python
wines.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>color</th>
      <th>fixed_acidity</th>
      <th>volatile_acidity</th>
      <th>citric_acid</th>
      <th>residual_sugar</th>
      <th>chlorides</th>
      <th>free_sulfur_dioxide</th>
      <th>total_sulfur_dioxide</th>
      <th>density</th>
      <th>ph</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>white</td>
      <td>7.0</td>
      <td>0.17</td>
      <td>0.74</td>
      <td>\N</td>
      <td>0.045</td>
      <td>24.0</td>
      <td>126.0</td>
      <td>0.99420</td>
      <td>3.26</td>
      <td>0.38</td>
      <td>12.2</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>red</td>
      <td>7.7</td>
      <td>0.64</td>
      <td>0.21</td>
      <td>2.2</td>
      <td>0.077</td>
      <td>32.0</td>
      <td>133.0</td>
      <td>0.99560</td>
      <td>3.27</td>
      <td>0.45</td>
      <td>9.9</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>white</td>
      <td>6.8</td>
      <td>0.39</td>
      <td>0.34</td>
      <td>7.4</td>
      <td>0.020</td>
      <td>38.0</td>
      <td>133.0</td>
      <td>0.99212</td>
      <td>3.18</td>
      <td>0.44</td>
      <td>12.0</td>
      <td>\N</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>white</td>
      <td>6.3</td>
      <td>0.28</td>
      <td>0.47</td>
      <td>\N</td>
      <td>0.040</td>
      <td>61.0</td>
      <td>183.0</td>
      <td>0.99592</td>
      <td>3.12</td>
      <td>0.51</td>
      <td>9.5</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>white</td>
      <td>7.4</td>
      <td>0.35</td>
      <td>0.20</td>
      <td>13.9</td>
      <td>0.054</td>
      <td>63.0</td>
      <td>229.0</td>
      <td>0.99888</td>
      <td>3.11</td>
      <td>0.50</td>
      <td>8.9</td>
      <td>\N</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


Notice that we are missing some values in the `residual_sugar` column as well as the `quality` column which we would ultimately like to predict.




<!-- ------------- New Cell ------------ -->


## Cleaning the Data
There are some missing values in the residula sugar column that we need to clean.  Here we will replace the residual sugar with a value predicted by other columns




<!-- ------------- New Cell ------------ -->


```python
@op()
def fix_residual_sugar(df):
    from sklearn.linear_model import LinearRegression
    # Convert back to numeric with missing values as NaN
    df['residual_sugar'] = pd.to_numeric(df['residual_sugar'], errors='coerce')
    print("missing residual sugar values:", df['residual_sugar'].isna().sum())
    # Replace the missing values by fitting a linear model to on other numeric columns
    imputer = LinearRegression()
    other_cols = df.columns[df.dtypes == 'float'].difference(['quality', 'residual_sugar', 'id'])
    imputer.fit(df.dropna()[other_cols], df.dropna()['residual_sugar'])
    predicted_sugar = imputer.predict(df[df['residual_sugar'].isna()][other_cols])
    df.loc[df['residual_sugar'].isna(), 'residual_sugar'] = predicted_sugar
    return df
```




<!-- ------------- New Cell ------------ -->


```python
wines_cleaned = fix_residual_sugar(wines)
wines_cleaned.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>color</th>
      <th>fixed_acidity</th>
      <th>volatile_acidity</th>
      <th>citric_acid</th>
      <th>residual_sugar</th>
      <th>chlorides</th>
      <th>free_sulfur_dioxide</th>
      <th>total_sulfur_dioxide</th>
      <th>density</th>
      <th>ph</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>white</td>
      <td>7.0</td>
      <td>0.17</td>
      <td>0.74</td>
      <td>10.027690</td>
      <td>0.045</td>
      <td>24.0</td>
      <td>126.0</td>
      <td>0.99420</td>
      <td>3.26</td>
      <td>0.38</td>
      <td>12.2</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>red</td>
      <td>7.7</td>
      <td>0.64</td>
      <td>0.21</td>
      <td>2.200000</td>
      <td>0.077</td>
      <td>32.0</td>
      <td>133.0</td>
      <td>0.99560</td>
      <td>3.27</td>
      <td>0.45</td>
      <td>9.9</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>white</td>
      <td>6.8</td>
      <td>0.39</td>
      <td>0.34</td>
      <td>7.400000</td>
      <td>0.020</td>
      <td>38.0</td>
      <td>133.0</td>
      <td>0.99212</td>
      <td>3.18</td>
      <td>0.44</td>
      <td>12.0</td>
      <td>\N</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>white</td>
      <td>6.3</td>
      <td>0.28</td>
      <td>0.47</td>
      <td>10.697218</td>
      <td>0.040</td>
      <td>61.0</td>
      <td>183.0</td>
      <td>0.99592</td>
      <td>3.12</td>
      <td>0.51</td>
      <td>9.5</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>white</td>
      <td>7.4</td>
      <td>0.35</td>
      <td>0.20</td>
      <td>13.900000</td>
      <td>0.054</td>
      <td>63.0</td>
      <td>229.0</td>
      <td>0.99888</td>
      <td>3.11</td>
      <td>0.50</td>
      <td>8.9</td>
      <td>\N</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


We also want to encode the color column as a boolean rather than a string. 




<!-- ------------- New Cell ------------ -->


```python
@op()
def encode_color(df):
    df['is_red'] = (df['color'] == 'red').astype('float')
    return df
```




<!-- ------------- New Cell ------------ -->


```python
featurized_wines = encode_color(wines_cleaned)
featurized_wines.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>color</th>
      <th>fixed_acidity</th>
      <th>volatile_acidity</th>
      <th>citric_acid</th>
      <th>residual_sugar</th>
      <th>chlorides</th>
      <th>free_sulfur_dioxide</th>
      <th>total_sulfur_dioxide</th>
      <th>density</th>
      <th>ph</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
      <th>is_red</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>white</td>
      <td>7.0</td>
      <td>0.17</td>
      <td>0.74</td>
      <td>10.027690</td>
      <td>0.045</td>
      <td>24.0</td>
      <td>126.0</td>
      <td>0.99420</td>
      <td>3.26</td>
      <td>0.38</td>
      <td>12.2</td>
      <td>8.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>red</td>
      <td>7.7</td>
      <td>0.64</td>
      <td>0.21</td>
      <td>2.200000</td>
      <td>0.077</td>
      <td>32.0</td>
      <td>133.0</td>
      <td>0.99560</td>
      <td>3.27</td>
      <td>0.45</td>
      <td>9.9</td>
      <td>5.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>white</td>
      <td>6.8</td>
      <td>0.39</td>
      <td>0.34</td>
      <td>7.400000</td>
      <td>0.020</td>
      <td>38.0</td>
      <td>133.0</td>
      <td>0.99212</td>
      <td>3.18</td>
      <td>0.44</td>
      <td>12.0</td>
      <td>\N</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>white</td>
      <td>6.3</td>
      <td>0.28</td>
      <td>0.47</td>
      <td>10.697218</td>
      <td>0.040</td>
      <td>61.0</td>
      <td>183.0</td>
      <td>0.99592</td>
      <td>3.12</td>
      <td>0.51</td>
      <td>9.5</td>
      <td>6.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>white</td>
      <td>7.4</td>
      <td>0.35</td>
      <td>0.20</td>
      <td>13.900000</td>
      <td>0.054</td>
      <td>63.0</td>
      <td>229.0</td>
      <td>0.99888</td>
      <td>3.11</td>
      <td>0.50</td>
      <td>8.9</td>
      <td>\N</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


## Tracking number of a Labeled wines 

As a sanity check, we want to make sure there are enough wines with quality scores to render reliable predictions.




<!-- ------------- New Cell ------------ -->


```python
@metric()
def get_number_labeled_wines(df):
    return (df['quality'] != "\\N").sum().astype(float)
```




<!-- ------------- New Cell ------------ -->


```python
num_labeled = get_number_labeled_wines(featurized_wines)
num_labeled.get()
```
**Output:**


```
5997.0
```






<!-- ------------- New Cell ------------ -->


To avoid making potentially risky predictions, we also don't want to render predictions if we have fewer than 1000 labeled wines. 




<!-- ------------- New Cell ------------ -->


```python
num_labeled.bound(lower=1000, severity="error")
```
**Output:**


```
<aqueduct.check_artifact.CheckArtifact at 0x7f91c1cc4910>
```






<!-- ------------- New Cell ------------ -->


## Predicting the Quality of Wines

In the following operator we:
1. Fit a decision tree model to the wines that do have quality ratings
2. Make quality rating predictions for all the wines in the table.




<!-- ------------- New Cell ------------ -->


```python
@op()
def predict_quality(df):
    from sklearn.tree import DecisionTreeRegressor
    # Convert the quality column to numerica and replace the "\N" with NaN 
    df['quality'] = pd.to_numeric(df['quality'], errors='coerce')
    
    # Fit a model to the columns that 
    quality_model = DecisionTreeRegressor(max_depth=3)
    feature_columns = df.columns[df.dtypes == 'float'].difference(['quality', 'id'])
    print("Feature Columns", feature_columns)
    quality_model.fit(df.dropna()[feature_columns], df.dropna()['quality'])
    df['pred_quality'] = quality_model.predict(df[feature_columns])
    return df
```




<!-- ------------- New Cell ------------ -->


```python
predicted_quality = predict_quality(featurized_wines)
predicted_quality.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>color</th>
      <th>fixed_acidity</th>
      <th>volatile_acidity</th>
      <th>citric_acid</th>
      <th>residual_sugar</th>
      <th>chlorides</th>
      <th>free_sulfur_dioxide</th>
      <th>total_sulfur_dioxide</th>
      <th>density</th>
      <th>ph</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
      <th>is_red</th>
      <th>pred_quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>white</td>
      <td>7.0</td>
      <td>0.17</td>
      <td>0.74</td>
      <td>10.027690</td>
      <td>0.045</td>
      <td>24.0</td>
      <td>126.0</td>
      <td>0.99420</td>
      <td>3.26</td>
      <td>0.38</td>
      <td>12.2</td>
      <td>8.0</td>
      <td>0.0</td>
      <td>6.653566</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>red</td>
      <td>7.7</td>
      <td>0.64</td>
      <td>0.21</td>
      <td>2.200000</td>
      <td>0.077</td>
      <td>32.0</td>
      <td>133.0</td>
      <td>0.99560</td>
      <td>3.27</td>
      <td>0.45</td>
      <td>9.9</td>
      <td>5.0</td>
      <td>1.0</td>
      <td>5.569721</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>white</td>
      <td>6.8</td>
      <td>0.39</td>
      <td>0.34</td>
      <td>7.400000</td>
      <td>0.020</td>
      <td>38.0</td>
      <td>133.0</td>
      <td>0.99212</td>
      <td>3.18</td>
      <td>0.44</td>
      <td>12.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>6.653566</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>white</td>
      <td>6.3</td>
      <td>0.28</td>
      <td>0.47</td>
      <td>10.697218</td>
      <td>0.040</td>
      <td>61.0</td>
      <td>183.0</td>
      <td>0.99592</td>
      <td>3.12</td>
      <td>0.51</td>
      <td>9.5</td>
      <td>6.0</td>
      <td>0.0</td>
      <td>5.255291</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>white</td>
      <td>7.4</td>
      <td>0.35</td>
      <td>0.20</td>
      <td>13.900000</td>
      <td>0.054</td>
      <td>63.0</td>
      <td>229.0</td>
      <td>0.99888</td>
      <td>3.11</td>
      <td>0.50</td>
      <td>8.9</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>5.255291</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


## Checking Our Predictions

As a sanity check, we also verify that the wine quality predictions are reasonable. 




<!-- ------------- New Cell ------------ -->


```python
@metric()
def get_rmse(df):
    import numpy as np
    residuals = (df['quality'] - df['pred_quality']).dropna()
    print("Computing error using:", len(residuals), "rows.")
    return np.sqrt((residuals * residuals).mean())
```




<!-- ------------- New Cell ------------ -->


```python
rmse = get_rmse(predicted_quality)
rmse.get()
```
**Output:**


```
0.7440468072891235
```






<!-- ------------- New Cell ------------ -->


Here we set two bounds on the error.  The first is a warning and the second we set to an error to avoid rendering potentially egregiously bad predictions.




<!-- ------------- New Cell ------------ -->


```python
rmse.bound(upper = 1.0)
rmse.bound(upper = 3.0, severity="error")
```
**Output:**


```
<aqueduct.check_artifact.CheckArtifact at 0x7f916006f7c0>
```






<!-- ------------- New Cell ------------ -->


## Saving the Predicted Wine Quality





<!-- ------------- New Cell ------------ -->


```python
predicted_quality.save(demodb.config("pred_wine_quality", update_mode="replace"))
```




<!-- ------------- New Cell ------------ -->


## Schedule Workflow to Run Daily





<!-- ------------- New Cell ------------ -->


```python
from textwrap import dedent
client.publish_flow(
    "Wine Ratings", 
    dedent(
        """
        This workflow builds a model to predict missing ratings for wines 
        and then uses that model to fill in missing ratings.
        """),
    schedule=aqueduct.daily(),
    artifacts=[predicted_quality, rmse, num_labeled]
)            
```
**Output:**


```
<aqueduct.flow.Flow at 0x7f91d0722490>
```




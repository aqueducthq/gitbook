


<!-- ------------- New Cell ------------ -->


# Customer Churn Tutorial

This is a quick tutorial that will walk you through creating your first workflow with Aqueduct. 

Every data scientist we’ve spoken to works in Python, so Aqueduct does too. The main way to deploy code into the cloud is via our Python SDK, which you can find [on GitHub](https://github.com/aqueducthq/aqueduct). 

The philosophy behind the Aqueduct SDK is that you should be able to connect to your data systems, transform your data and generate predictions, and publish your results once you’re happy with them. This guide will walk you through installing your SDK, setting up a client, transforming some data, and publishing a workflow to the cloud. 

**Outline:**
1. We will use standard Python libraries to transform our data and build a simple ensemble of churn models.
2. We will use the Aqueduct Python API to deploy our churn prediction pipeline
3. We will visit the Aqueduct web interface to view our deployed prediction pipeline




<!-- ------------- New Cell ------------ -->


--- 


## Step 1: Building the Churn Model

Before we do anything with Aqueduct, let’s first build some machine learning models. Aqueduct doesn’t have any opinions about where or how you do this.  In this example notebook, we will build a basic model churn prediction model using a customer churn dataset. 

Here is our training data:




<!-- ------------- New Cell ------------ -->


```python
import pandas as pd
customers_table = pd.read_csv("data/customers.csv")
churn_table = pd.read_csv("data/churn_data.csv")
pd.merge(customers_table, churn_table, on='cust_id').head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cust_id</th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
      <th>churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


### Feature Transformations

Most real-world machine learning relies on some form of feature transformation.  Because we have counts in our data we apply a log transformation to those counts.




<!-- ------------- New Cell ------------ -->


```python
import numpy as np 
def log_featurize(cust: pd.DataFrame) -> pd.DataFrame: 
    features = cust.copy()
    skip_cols = ['cust_id', 'using_deep_learning', 'using_dbt']
    for col in features.columns.difference(skip_cols):
        features["log_"+col] = np.log(features[col] + 1.0)
    return features.drop(columns="cust_id")
```




<!-- ------------- New Cell ------------ -->


```python
features_table = log_featurize(customers_table)
features_table.head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
      <th>log_company_size</th>
      <th>log_duration_months</th>
      <th>log_n_data_eng</th>
      <th>log_n_integrations</th>
      <th>log_n_rows</th>
      <th>log_n_support_tickets</th>
      <th>log_n_users</th>
      <th>log_n_workflows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>3.401197</td>
      <td>0.693147</td>
      <td>1.098612</td>
      <td>1.791759</td>
      <td>7.604894</td>
      <td>1.386294</td>
      <td>1.098612</td>
      <td>1.609438</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>3.465736</td>
      <td>0.693147</td>
      <td>1.386294</td>
      <td>1.609438</td>
      <td>9.052399</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.386294</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
      <td>3.401197</td>
      <td>1.386294</td>
      <td>0.693147</td>
      <td>1.386294</td>
      <td>8.929170</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.609438</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>3.526361</td>
      <td>1.609438</td>
      <td>1.386294</td>
      <td>1.609438</td>
      <td>8.363342</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.386294</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>3.367296</td>
      <td>0.693147</td>
      <td>1.098612</td>
      <td>1.386294</td>
      <td>7.667158</td>
      <td>0.000000</td>
      <td>0.693147</td>
      <td>1.098612</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


### Training the Model

In this example, we will train and ensemble two basic classifiers.  In practice, would probably do something more interesting but this will help illustrate post-processing logic (the ensemble function).




<!-- ------------- New Cell ------------ -->


```python
from sklearn.linear_model import LogisticRegression
linear_model = LogisticRegression(max_iter=10000)
linear_model.fit(features_table, churn_table['churn'])
```
**Output:**


```
LogisticRegression(max_iter=10000)
```






<!-- ------------- New Cell ------------ -->


```python
from sklearn.tree import DecisionTreeClassifier
decision_tree_model = DecisionTreeClassifier(max_depth = 10, min_samples_split = 3)
decision_tree_model.fit(features_table, churn_table['churn'])
```
**Output:**


```
DecisionTreeClassifier(max_depth=10, min_samples_split=3)
```






<!-- ------------- New Cell ------------ -->


### Making Predictions Locally 

Putting all the above pieces together we can define the **prediction workflow** as the following:




<!-- ------------- New Cell ------------ -->


```python
def predict_linear(features_table):
    return pd.DataFrame({'linear': linear_model.predict_proba(features_table)[:,1]})

def predict_tree(features_table):
    return pd.DataFrame({'tree': decision_tree_model.predict_proba(features_table)[:,1]})

def predict_ensemble(customers_table, linear_pred_table, tree_pred_table):
    return customers_table.assign(
        prob_churn = linear_pred_table.join(tree_pred_table).mean(axis=1))
```




<!-- ------------- New Cell ------------ -->


```python
features_table = log_featurize(customers_table)
linear_pred_table = predict_linear(features_table)
tree_pred_table = predict_tree(features_table)
churn_table = predict_ensemble(customers_table, linear_pred_table, tree_pred_table)
```




<!-- ------------- New Cell ------------ -->


If we look at the output table we see that the `prob_churn` column contains the churn predictions.




<!-- ------------- New Cell ------------ -->


```python
churn_table.head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cust_id</th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
      <th>prob_churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>0.076126</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>0.143632</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
      <td>0.161187</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>0.141960</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>0.161899</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


Using standard python libraries we have trained two models and developed a simple workflow that combines feature transformations with models and post-prediction logic to estimate churn.  In the remainder of this notebook we will deploy the above prediction workflow to the cloud, integrate it with production data sources, and publish predictions to multiple destinations.  




<!-- ------------- New Cell ------------ -->


---

## Step 2: Deploying a Prediction Workflow

We will now deploy the above prediction workflow to the cloud.  You will need the API key for your Aqueduct server, which you can find on the `/account` page of your UI.  




<!-- ------------- New Cell ------------ -->


```python
import aqueduct 
from aqueduct import op, check
```




<!-- ------------- New Cell ------------ -->


### Connecting the Aqueduct API Client

To interact with the Aqueduct cloud infrastructure you will need to use the API Key associated with your account. 




<!-- ------------- New Cell ------------ -->


```python
# You can use `localhost` if you're running this notebook on the same machine as the server.
address = "<your_server_address>" 
api_key = "<your_api_key>"
client = aqueduct.Client(api_key, address)
```




<!-- ------------- New Cell ------------ -->


### Connecting to Your Data

Workflows access and publish to data integrations that are configured on the Integrations Page.  In this demo we will connect to the demo data integration which is a Postgres database containing several standard [sample datasets](https://docs.aqueducthq.com/example-workflows/demo-data-warehouse) including some synthetic customer churn data.  Each kind of data integration may offer different functionality.  Here we are using a relational integration which support general SQL expressions.





<!-- ------------- New Cell ------------ -->


```python
warehouse = client.integration(name="aqueduct_demo")
customers_table = warehouse.sql(query="SELECT * FROM customers;")
print(type(customers_table))
```




<!-- ------------- New Cell ------------ -->


Here the `cust` table is a `TableArtifact` representing a virtual table living in the cloud.  For debugging purposes I can get the Pandas DataFrame corresponding to any `TableArtifact` by using the `get()` method:




<!-- ------------- New Cell ------------ -->


```python
customers_table.get()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cust_id</th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>495</th>
      <td>495</td>
      <td>2</td>
      <td>3108</td>
      <td>5</td>
      <td>30</td>
      <td>5</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>4.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>496</th>
      <td>496</td>
      <td>7</td>
      <td>4240</td>
      <td>1</td>
      <td>30</td>
      <td>1</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>497</th>
      <td>497</td>
      <td>3</td>
      <td>2520</td>
      <td>1</td>
      <td>27</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>498</th>
      <td>498</td>
      <td>16</td>
      <td>1972</td>
      <td>1</td>
      <td>33</td>
      <td>1</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>499</th>
      <td>499</td>
      <td>3</td>
      <td>7003</td>
      <td>1</td>
      <td>25</td>
      <td>2</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 11 columns</p>
</div>




<!-- ------------- New Cell ------------ -->


### Applying Operators to Tables

I can transform these `TableArtifacts` by applying *decorated* Python functions.  Here, we copied over our `log_featurize` function from our previous notebook. The only change we've made here is to add a decorator `@aqueduct.decorator.op()`.  Otherwise, it is exactly my `log_featurize` function from above.

All this decorator does is tell Aqueduct to run this function in the cloud whenever we ask for the results. In a few cells, when we publish our workflow, this will take our code, ship it off to the cloud, and we'll be ready to go in a matter of seconds.





<!-- ------------- New Cell ------------ -->


```python
@op()
def log_featurize(cust: pd.DataFrame) -> pd.DataFrame: 
    features = cust.copy()
    skip_cols = ['cust_id', 'using_deep_learning', 'using_dbt']
    for col in features.columns.difference(skip_cols):
        features["log_"+col] = np.log(features[col] + 1.0)
    return features.drop(columns="cust_id")
```




<!-- ------------- New Cell ------------ -->


Any function that is decorated with `@op` can be called on any data table that you retrieve through Aqueduct (e.g., via the SQL query above). The result of this function call will be *another* data table that you can then use for future function calls.




<!-- ------------- New Cell ------------ -->


```python
features_table = log_featurize(customers_table)
print(type(features_table))
```




<!-- ------------- New Cell ------------ -->


As before, we can always view the value of the table by getting the corresponding data frame. 




<!-- ------------- New Cell ------------ -->


```python
features_table.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
      <th>log_company_size</th>
      <th>log_duration_months</th>
      <th>log_n_data_eng</th>
      <th>log_n_integrations</th>
      <th>log_n_rows</th>
      <th>log_n_support_tickets</th>
      <th>log_n_users</th>
      <th>log_n_workflows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>3.401197</td>
      <td>0.693147</td>
      <td>1.098612</td>
      <td>1.791759</td>
      <td>7.604894</td>
      <td>1.386294</td>
      <td>1.098612</td>
      <td>1.609438</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>3.465736</td>
      <td>0.693147</td>
      <td>1.386294</td>
      <td>1.609438</td>
      <td>9.052399</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.386294</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
      <td>3.401197</td>
      <td>1.386294</td>
      <td>0.693147</td>
      <td>1.386294</td>
      <td>8.929170</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.609438</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>3.526361</td>
      <td>1.609438</td>
      <td>1.386294</td>
      <td>1.609438</td>
      <td>8.363342</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.386294</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>3.367296</td>
      <td>0.693147</td>
      <td>1.098612</td>
      <td>1.386294</td>
      <td>7.667158</td>
      <td>0.000000</td>
      <td>0.693147</td>
      <td>1.098612</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


### Deploying the Prediction Workflow

We can now copy, paste, and *decorate* our workflow from the previous section to define a workflow in the cloud.




<!-- ------------- New Cell ------------ -->


```python
@op()
def predict_linear(features_table):
    return pd.DataFrame({'linear': linear_model.predict_proba(features_table)[:,1]})

@op()
def predict_tree(features_table):
    return pd.DataFrame({'tree': decision_tree_model.predict_proba(features_table)[:,1]})

@op()
def predict_ensemble(customers_table, linear_pred_table, tree_pred_table):
    return customers_table.assign(
        prob_churn = linear_pred_table.join(tree_pred_table).mean(axis=1))
```




<!-- ------------- New Cell ------------ -->


That's it! Using the exact same sequence of steps as we used earlier we can now build a workflow in the cloud.




<!-- ------------- New Cell ------------ -->


```python
features_table = log_featurize(customers_table)
linear_pred_table = predict_linear(features_table)
tree_pred_table = predict_tree(features_table)
churn_table = predict_ensemble(customers_table, linear_pred_table, tree_pred_table)
```




<!-- ------------- New Cell ------------ -->


Note that our ensemble function can take in 3 data tables. Aqueduct will make sure that all three are correctly provided to the function when it's executing.

Examining the final table we see our predictions in the `prob_churn` column.




<!-- ------------- New Cell ------------ -->


```python
churn_table.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cust_id</th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
      <th>prob_churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>0.076126</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>0.143632</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
      <td>0.161187</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>0.141960</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>0.161899</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->


### Checking Your Tables

Debugging or even knowing when a prediction pipeline is failing can be challenging!  Input data is constantly change, features can drift, and even the choice of model may no longer make sense as labels shift.  It is therefore critical that we check our data before publishing predictions.  This can be done using `checks`.

A Aqueduct `check` takes in a DataFrame and returns a boolean indicating whether the check passes. You can also return a pandas series with a single boolean or a series of booleans. The test passes if everything in the Series is true. In this case, we're going to define a simple test that ensures that less than 40% of our customers are marked as likely to churn. 




<!-- ------------- New Cell ------------ -->


```python
@check(description="Checking reasonable churn average.")
def not_too_much_churn(df: pd.DataFrame): 
    return df['prob_churn'].mean() < 0.4
```




<!-- ------------- New Cell ------------ -->


Executing `not_too_much_churn` on `churn_table` will automatically attach the check to the table, but it will not run the test and until you call `get()` on the output.




<!-- ------------- New Cell ------------ -->


```python
check_result = not_too_much_churn(churn_table)

# Verify that the check passed
assert check_result.get()
```




<!-- ------------- New Cell ------------ -->


### Saving Tables Artifacts to Integrations

So far we have defined a workflow to build the `churn_table` containing our churn predictions.  We now want to publish this table where others can use it.  We do this by `saving` the table to various integrations.  

First we save the table back to the data warehouse that contains the original customer data. 




<!-- ------------- New Cell ------------ -->


```python
churn_table.save(warehouse.config(table='pred_churn', update_mode='replace'))
```




<!-- ------------- New Cell ------------ -->


We can also save these results in multiple integrations. We can see what integrations we are connected to via `list_integrations`. If we want to connect to more integrations, we can do so through the online UI. 




<!-- ------------- New Cell ------------ -->


```python
client.list_integrations()
```
**Output:**


```
{'salesforce/cgwu_test': IntegrationInfo(id=UUID('bb836d75-8025-494d-8957-cfb8b40e9b32'), name='salesforce/cgwu_test', service=<ServiceType.SALESFORCE: 'Salesforce'>, createdAt=1648232933, validated=True),
 'google_sheets/example_integration': IntegrationInfo(id=UUID('296a3528-1143-4c4a-abd7-f4ac03860b76'), name='google_sheets/example_integration', service=<ServiceType.GOOGLE_SHEETS: 'Google Sheets'>, createdAt=1650481564, validated=True),
 'spiral_demo/': IntegrationInfo(id=UUID('a6072a77-cb69-434e-846c-6d97a62e7fea'), name='spiral_demo/', service=<ServiceType.POSTGRES: 'Postgres'>, createdAt=1651017667, validated=True)}
```






<!-- ------------- New Cell ------------ -->


Here we are going to load `google_sheets_integration` and also write our results there.




<!-- ------------- New Cell ------------ -->


```python
google_sheets_integration = client.integration('google_sheets/example_integration')
churn_table.save(google_sheets_integration.config(filepath='predictions/churn_pred.csv', save_mode='overwrite'))
```




<!-- ------------- New Cell ------------ -->


## Scheduling the Workflow to Run Repeatedly

With all this hard work done, we're ready to deploy our workflow so that it runs repeatedly.  To do this, we define a workflow with a name and the artifacts that we want to compute and a cadence that we want to run the workflow.  The Aqueduct APIs will automatically capture and intermediate artifacts and code needed to produce the final artifacts.

When you call `publish_flow`, all of this will be shipped off to the cloud!




<!-- ------------- New Cell ------------ -->


```python
churn_flow = client.publish_flow(
    name = "Demo Churn Ensemble", 
    artifacts = [churn_table],
    schedule = aqueduct.hourly(),
)
print(churn_flow.id())
```




<!-- ------------- New Cell ------------ -->


---

## (Step 3) Checkout the UI




<!-- ------------- New Cell ------------ -->


Great, we're done! You can go on over to the Aqueduct UI now at `{your_server_address}:3000`.

and you should see a new workflow called `Demo Churn Ensemble`. It might take a minute or two to get up and running, but once that's done, you'll be able to click around the different stages of the workflow and see the intermediary results at each stage, trigger new runs, and see previous versions. 

Please let us know if you have any questions. We'd love to hear from you: hello@aqueducthq.com.




<!-- ------------- New Cell ------------ -->


<br/><br/><br/>

---

## Bonus Features

In some settings, you may want to use code (transformations) that are managed in a separate version controlled environment.  If you setup the Github integration then you can use the following code to create a local reference to code directly in a github repository.




<!-- ------------- New Cell ------------ -->


```python
gh = client.github(repo="aqueductai/aqueduct", branch="main")
```




<!-- ------------- New Cell ------------ -->


```python
log_op = gh.op("sdk/examples/churn_prediction/operators", entry_point="utils.py", method="log_featurize", class_name = "")
```




<!-- ------------- New Cell ------------ -->


```python
features_table = log_op(customers_table)
features_table.get().head()
```
**Output:**
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>n_workflows</th>
      <th>n_rows</th>
      <th>n_users</th>
      <th>company_size</th>
      <th>n_integrations</th>
      <th>n_support_tickets</th>
      <th>duration_months</th>
      <th>using_deep_learning</th>
      <th>n_data_eng</th>
      <th>using_dbt</th>
      <th>log_company_size</th>
      <th>log_duration_months</th>
      <th>log_n_data_eng</th>
      <th>log_n_integrations</th>
      <th>log_n_rows</th>
      <th>log_n_support_tickets</th>
      <th>log_n_users</th>
      <th>log_n_workflows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4</td>
      <td>2007</td>
      <td>2</td>
      <td>29</td>
      <td>5</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>3.401197</td>
      <td>0.693147</td>
      <td>1.098612</td>
      <td>1.791759</td>
      <td>7.604894</td>
      <td>1.386294</td>
      <td>1.098612</td>
      <td>1.609438</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>8538</td>
      <td>1</td>
      <td>31</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>3.465736</td>
      <td>0.693147</td>
      <td>1.386294</td>
      <td>1.609438</td>
      <td>9.052399</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.386294</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>7548</td>
      <td>1</td>
      <td>29</td>
      <td>3</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>False</td>
      <td>1.0</td>
      <td>True</td>
      <td>3.401197</td>
      <td>1.386294</td>
      <td>0.693147</td>
      <td>1.386294</td>
      <td>8.929170</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.609438</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4286</td>
      <td>1</td>
      <td>33</td>
      <td>4</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>False</td>
      <td>3.0</td>
      <td>True</td>
      <td>3.526361</td>
      <td>1.609438</td>
      <td>1.386294</td>
      <td>1.609438</td>
      <td>8.363342</td>
      <td>0.693147</td>
      <td>0.693147</td>
      <td>1.386294</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2136</td>
      <td>1</td>
      <td>28</td>
      <td>3</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>False</td>
      <td>2.0</td>
      <td>True</td>
      <td>3.367296</td>
      <td>0.693147</td>
      <td>1.098612</td>
      <td>1.386294</td>
      <td>7.667158</td>
      <td>0.000000</td>
      <td>0.693147</td>
      <td>1.098612</td>
    </tr>
  </tbody>
</table>
</div>




<!-- ------------- New Cell ------------ -->





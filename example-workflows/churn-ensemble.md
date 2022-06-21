# Churn Ensemble

## Customer Churn Tutorial

This is a quick tutorial that will walk you through creating your first workflow with Aqueduct.

Every data scientist we’ve spoken to works in Python, so Aqueduct does too. The main way to deploy code into the cloud is via our Python SDK, which you can find [on GitHub](https://github.com/aqueducthq/aqueduct).

The philosophy behind the Aqueduct SDK is that you should be able to connect to your data systems, transform your data and generate predictions, and publish your results once you’re happy with them. This guide will walk you through installing your SDK, setting up a client, transforming some data, and publishing a workflow to the cloud.

**Outline:**

1. We will use standard Python libraries to transform our data and build a simple ensemble of churn models.
2. We will use the Aqueduct Python API to deploy our churn prediction pipeline.
3. We will visit the Aqueduct web interface to view our deployed prediction pipeline.

***

### Step 1: Building the Churn Model

Before we do anything with Aqueduct, let’s first build some machine learning models. Aqueduct doesn’t have any opinions about where or how you do this. In this example notebook, we will build a basic model churn prediction model using a customer churn dataset.

Here is our training data:

```python
import pandas as pd
customers_table = pd.read_csv("data/customers.csv")
churn_table = pd.read_csv("data/churn_data.csv")
pd.merge(customers_table, churn_table, on='cust_id').head()
```

**Output:**

|   | cust\_id | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt | churn |
| - | -------- | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- | ----- |
| 0 | 0        | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       | False |
| 1 | 1        | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       | True  |
| 2 | 2        | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       | False |
| 3 | 3        | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       | False |
| 4 | 4        | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       | False |

#### Feature Transformations

Most real-world machine learning relies on some form of feature transformation. Because we have counts in our data we apply a log transformation to those counts.

```python
import numpy as np 
def log_featurize(cust: pd.DataFrame) -> pd.DataFrame: 
    features = cust.copy()
    skip_cols = ['cust_id', 'using_deep_learning', 'using_dbt']
    for col in features.columns.difference(skip_cols):
        features["log_"+col] = np.log(features[col] + 1.0)
    return features.drop(columns="cust_id")
```

```python
features_table = log_featurize(customers_table)
features_table.head()
```

**Output:**

|   | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt | log\_company\_size | log\_duration\_months | log\_n\_data\_eng | log\_n\_integrations | log\_n\_rows | log\_n\_support\_tickets | log\_n\_users | log\_n\_workflows |
| - | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- | ------------------ | --------------------- | ----------------- | -------------------- | ------------ | ------------------------ | ------------- | ----------------- |
| 0 | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       | 3.401197           | 0.693147              | 1.098612          | 1.791759             | 7.604894     | 1.386294                 | 1.098612      | 1.609438          |
| 1 | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       | 3.465736           | 0.693147              | 1.386294          | 1.609438             | 9.052399     | 0.693147                 | 0.693147      | 1.386294          |
| 2 | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       | 3.401197           | 1.386294              | 0.693147          | 1.386294             | 8.929170     | 0.693147                 | 0.693147      | 1.609438          |
| 3 | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       | 3.526361           | 1.609438              | 1.386294          | 1.609438             | 8.363342     | 0.693147                 | 0.693147      | 1.386294          |
| 4 | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       | 3.367296           | 0.693147              | 1.098612          | 1.386294             | 7.667158     | 0.000000                 | 0.693147      | 1.098612          |

#### Training the Model

In this example, we will train and ensemble two basic classifiers. In practice, would probably do something more interesting but this will help illustrate post-processing logic (the ensemble function).

```python
from sklearn.linear_model import LogisticRegression
linear_model = LogisticRegression(max_iter=10000)
linear_model.fit(features_table, churn_table['churn'])
```

**Output:**

```
LogisticRegression(max_iter=10000)
```

```python
from sklearn.tree import DecisionTreeClassifier
decision_tree_model = DecisionTreeClassifier(max_depth = 10, min_samples_split = 3)
decision_tree_model.fit(features_table, churn_table['churn'])
```

**Output:**

```
DecisionTreeClassifier(max_depth=10, min_samples_split=3)
```

#### Making Predictions Locally

Putting all the above pieces together we can define the **prediction workflow** as the following:

```python
def predict_linear(features_table):
    return pd.DataFrame({'linear': linear_model.predict_proba(features_table)[:,1]})

def predict_tree(features_table):
    return pd.DataFrame({'tree': decision_tree_model.predict_proba(features_table)[:,1]})

def predict_ensemble(customers_table, linear_pred_table, tree_pred_table):
    return customers_table.assign(
        prob_churn = linear_pred_table.join(tree_pred_table).mean(axis=1))
```

```python
features_table = log_featurize(customers_table)
linear_pred_table = predict_linear(features_table)
tree_pred_table = predict_tree(features_table)
churn_table = predict_ensemble(customers_table, linear_pred_table, tree_pred_table)
```

If we look at the output table we see that the `prob_churn` column contains the churn predictions.

```python
churn_table.head()
```

**Output:**

|   | cust\_id | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt | prob\_churn |
| - | -------- | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- | ----------- |
| 0 | 0        | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       | 0.076126    |
| 1 | 1        | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       | 0.143632    |
| 2 | 2        | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       | 0.161187    |
| 3 | 3        | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       | 0.141960    |
| 4 | 4        | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       | 0.161899    |

Using standard python libraries we have trained two models and developed a simple workflow that combines feature transformations with models and post-prediction logic to estimate churn. In the remainder of this notebook we will deploy the above prediction workflow to the cloud, integrate it with production data sources, and publish predictions to multiple destinations.

***

### Step 2: Deploying a Prediction Workflow

We will now deploy the above prediction workflow to the cloud. You will need the API key for your Aqueduct server, which you can find on the `/account` page of your UI.

```python
import aqueduct 
from aqueduct import op, check
```

#### Connecting the Aqueduct API Client

To interact with the Aqueduct cloud infrastructure you will need to use the API Key associated with your account.

```python
# You can use `localhost` if you're running this notebook on the same machine as the server.
address = "<your_server_address>" 
api_key = "<your_api_key>"
client = aqueduct.Client(api_key, address)
```

#### Connecting to Your Data

Workflows access and publish to data integrations that are configured on the Integrations Page. In this demo we will connect to the demo data integration which is a Postgres database containing several standard [sample datasets](https://docs.aqueducthq.com/example-workflows/demo-data-warehouse) including some synthetic customer churn data. Each kind of data integration may offer different functionality. Here we are using a relational integration which support general SQL expressions.

```python
warehouse = client.integration(name="aqueduct_demo")
customers_table = warehouse.sql(query="SELECT * FROM customers;")
print(type(customers_table))
```

Here the `cust` table is a `TableArtifact` representing a virtual table living in the cloud. For debugging purposes I can get the Pandas DataFrame corresponding to any `TableArtifact` by using the `get()` method:

```python
customers_table.get()
```

**Output:**

|     | cust\_id | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt |
| --- | -------- | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- |
| 0   | 0        | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       |
| 1   | 1        | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       |
| 2   | 2        | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       |
| 3   | 3        | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       |
| 4   | 4        | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       |
| ... | ...      | ...          | ...     | ...      | ...           | ...             | ...                 | ...              | ...                   | ...          | ...        |
| 495 | 495      | 2            | 3108    | 5        | 30            | 5               | 3.0                 | 3.0              | False                 | 4.0          | True       |
| 496 | 496      | 7            | 4240    | 1        | 30            | 1               | 2.0                 | 1.0              | False                 | 2.0          | False      |
| 497 | 497      | 3            | 2520    | 1        | 27            | 4               | 1.0                 | 1.0              | False                 | 2.0          | True       |
| 498 | 498      | 16           | 1972    | 1        | 33            | 1               | 3.0                 | 3.0              | False                 | 3.0          | True       |
| 499 | 499      | 3            | 7003    | 1        | 25            | 2               | 3.0                 | 1.0              | False                 | 1.0          | True       |

500 rows × 11 columns

#### Applying Operators to Tables

I can transform these `TableArtifacts` by applying _decorated_ Python functions. Here, we copied over our `log_featurize` function from our previous notebook. The only change we've made here is to add a decorator `@aqueduct.decorator.op()`. Otherwise, it is exactly my `log_featurize` function from above.

All this decorator does is tell Aqueduct to run this function in the cloud whenever we ask for the results. In a few cells, when we publish our workflow, this will take our code, ship it off to the cloud, and we'll be ready to go in a matter of seconds.

```python
@op()
def log_featurize(cust: pd.DataFrame) -> pd.DataFrame: 
    features = cust.copy()
    skip_cols = ['cust_id', 'using_deep_learning', 'using_dbt']
    for col in features.columns.difference(skip_cols):
        features["log_"+col] = np.log(features[col] + 1.0)
    return features.drop(columns="cust_id")
```

Any function that is decorated with `@op` can be called on any data table that you retrieve through Aqueduct (e.g., via the SQL query above). The result of this function call will be _another_ data table that you can then use for future function calls.

```python
features_table = log_featurize(customers_table)
print(type(features_table))
```

As before, we can always view the value of the table by getting the corresponding data frame.

```python
features_table.get().head()
```

**Output:**

|   | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt | log\_company\_size | log\_duration\_months | log\_n\_data\_eng | log\_n\_integrations | log\_n\_rows | log\_n\_support\_tickets | log\_n\_users | log\_n\_workflows |
| - | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- | ------------------ | --------------------- | ----------------- | -------------------- | ------------ | ------------------------ | ------------- | ----------------- |
| 0 | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       | 3.401197           | 0.693147              | 1.098612          | 1.791759             | 7.604894     | 1.386294                 | 1.098612      | 1.609438          |
| 1 | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       | 3.465736           | 0.693147              | 1.386294          | 1.609438             | 9.052399     | 0.693147                 | 0.693147      | 1.386294          |
| 2 | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       | 3.401197           | 1.386294              | 0.693147          | 1.386294             | 8.929170     | 0.693147                 | 0.693147      | 1.609438          |
| 3 | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       | 3.526361           | 1.609438              | 1.386294          | 1.609438             | 8.363342     | 0.693147                 | 0.693147      | 1.386294          |
| 4 | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       | 3.367296           | 0.693147              | 1.098612          | 1.386294             | 7.667158     | 0.000000                 | 0.693147      | 1.098612          |

#### Deploying the Prediction Workflow

We can now copy, paste, and _decorate_ our workflow from the previous section to define a workflow in the cloud.

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

That's it! Using the exact same sequence of steps as we used earlier we can now build a workflow in the cloud.

```python
features_table = log_featurize(customers_table)
linear_pred_table = predict_linear(features_table)
tree_pred_table = predict_tree(features_table)
churn_table = predict_ensemble(customers_table, linear_pred_table, tree_pred_table)
```

Note that our ensemble function can take in 3 data tables. Aqueduct will make sure that all three are correctly provided to the function when it's executing.

Examining the final table we see our predictions in the `prob_churn` column.

```python
churn_table.get().head()
```

**Output:**

|   | cust\_id | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt | prob\_churn |
| - | -------- | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- | ----------- |
| 0 | 0        | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       | 0.076126    |
| 1 | 1        | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       | 0.143632    |
| 2 | 2        | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       | 0.161187    |
| 3 | 3        | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       | 0.141960    |
| 4 | 4        | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       | 0.161899    |

#### Checking Your Tables

Debugging or even knowing when a prediction pipeline is failing can be challenging! Input data is constantly change, features can drift, and even the choice of model may no longer make sense as labels shift. It is therefore critical that we check our data before publishing predictions. This can be done using `checks`.

A Aqueduct `check` takes in a DataFrame and returns a boolean indicating whether the check passes. You can also return a pandas series with a single boolean or a series of booleans. The test passes if everything in the Series is true. In this case, we're going to define a simple test that ensures that less than 40% of our customers are marked as likely to churn.

```python
@check(description="Checking reasonable churn average.")
def not_too_much_churn(df: pd.DataFrame): 
    return df['prob_churn'].mean() < 0.4
```

Executing `not_too_much_churn` on `churn_table` will automatically attach the check to the table, but it will not run the test and until you call `get()` on the output.

```python
check_result = not_too_much_churn(churn_table)

# Verify that the check passed
assert check_result.get()
```

#### Saving Tables Artifacts to Integrations

So far we have defined a workflow to build the `churn_table` containing our churn predictions. We now want to publish this table where others can use it. We do this by `saving` the table to various integrations.

First we save the table back to the data warehouse that contains the original customer data.

```python
churn_table.save(warehouse.config(table='pred_churn', update_mode='replace'))
```

We can also save these results in multiple integrations. We can see what integrations we are connected to via `list_integrations`. If we want to connect to more integrations, we can do so through the online UI.

```python
client.list_integrations()
```

**Output:**

```
{'salesforce/cgwu_test': IntegrationInfo(id=UUID('bb836d75-8025-494d-8957-cfb8b40e9b32'), name='salesforce/cgwu_test', service=<ServiceType.SALESFORCE: 'Salesforce'>, createdAt=1648232933, validated=True),
 'google_sheets/example_integration': IntegrationInfo(id=UUID('296a3528-1143-4c4a-abd7-f4ac03860b76'), name='google_sheets/example_integration', service=<ServiceType.GOOGLE_SHEETS: 'Google Sheets'>, createdAt=1650481564, validated=True),
 'aqueduct_demo': IntegrationInfo(id=UUID('a6072a77-cb69-434e-846c-6d97a62e7fea'), name='aqueduct_demo, service=<ServiceType.POSTGRES: 'Postgres'>, createdAt=1651017667, validated=True)}
```

Here we are going to load `google_sheets_integration` and also write our results there.

```python
google_sheets_integration = client.integration('google_sheets/example_integration')
churn_table.save(google_sheets_integration.config(filepath='predictions/churn_pred.csv', save_mode='overwrite'))
```

### Scheduling the Workflow to Run Repeatedly

With all this hard work done, we're ready to deploy our workflow so that it runs repeatedly. To do this, we define a workflow with a name and the artifacts that we want to compute and a cadence that we want to run the workflow. The Aqueduct APIs will automatically capture and intermediate artifacts and code needed to produce the final artifacts.

When you call `publish_flow`, all of this will be shipped off to the cloud!

```python
churn_flow = client.publish_flow(
    name = "Demo Churn Ensemble", 
    artifacts = [churn_table],
    schedule = aqueduct.hourly(),
)
print(churn_flow.id())
```

***

### (Step 3) Checkout the UI

Great, we're done! You can go on over to the Aqueduct UI now at `{your_server_address}:3000`.

and you should see a new workflow called `Demo Churn Ensemble`. It might take a minute or two to get up and running, but once that's done, you'll be able to click around the different stages of the workflow and see the intermediary results at each stage, trigger new runs, and see previous versions.

Please let us know if you have any questions. We'd love to hear from you: [hello@aqueducthq.com](mailto:hello@aqueducthq.com).

\
\
\


***

### Bonus Features

In some settings, you may want to use code (transformations) that are managed in a separate version controlled environment. If you setup the Github integration then you can use the following code to create a local reference to code directly in a github repository.

```python
gh = client.github(repo="aqueductai/aqueduct", branch="main")
```

```python
log_op = gh.op("sdk/examples/churn_prediction/operators", entry_point="utils.py", method="log_featurize", class_name = "")
```

```python
features_table = log_op(customers_table)
features_table.get().head()
```

**Output:**

|   | n\_workflows | n\_rows | n\_users | company\_size | n\_integrations | n\_support\_tickets | duration\_months | using\_deep\_learning | n\_data\_eng | using\_dbt | log\_company\_size | log\_duration\_months | log\_n\_data\_eng | log\_n\_integrations | log\_n\_rows | log\_n\_support\_tickets | log\_n\_users | log\_n\_workflows |
| - | ------------ | ------- | -------- | ------------- | --------------- | ------------------- | ---------------- | --------------------- | ------------ | ---------- | ------------------ | --------------------- | ----------------- | -------------------- | ------------ | ------------------------ | ------------- | ----------------- |
| 0 | 4            | 2007    | 2        | 29            | 5               | 3.0                 | 1.0              | False                 | 2.0          | True       | 3.401197           | 0.693147              | 1.098612          | 1.791759             | 7.604894     | 1.386294                 | 1.098612      | 1.609438          |
| 1 | 3            | 8538    | 1        | 31            | 4               | 1.0                 | 1.0              | False                 | 3.0          | True       | 3.465736           | 0.693147              | 1.386294          | 1.609438             | 9.052399     | 0.693147                 | 0.693147      | 1.386294          |
| 2 | 4            | 7548    | 1        | 29            | 3               | 1.0                 | 3.0              | False                 | 1.0          | True       | 3.401197           | 1.386294              | 0.693147          | 1.386294             | 8.929170     | 0.693147                 | 0.693147      | 1.609438          |
| 3 | 3            | 4286    | 1        | 33            | 4               | 1.0                 | 4.0              | False                 | 3.0          | True       | 3.526361           | 1.609438              | 1.386294          | 1.609438             | 8.363342     | 0.693147                 | 0.693147      | 1.386294          |
| 4 | 2            | 2136    | 1        | 28            | 3               | 0.0                 | 1.0              | False                 | 2.0          | True       | 3.367296           | 0.693147              | 1.098612          | 1.386294             | 7.667158     | 0.000000                 | 0.693147      | 1.098612          |

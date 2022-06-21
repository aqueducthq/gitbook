# Metrics: Measuring your Predictions

**Metrics** are numerical measurements and summaries of a dataset in Aqueduct. Writing a metric is very similar to writing an [Operator](../operators.md) -- the only difference is that rather than returning a data Artifact, a Metric function returns a floating point number.&#x20;

Here's how you can create a Metric:

```python
from aqueduct import metric

db = client.integration('aqueduct_demo')
customers = db.sql('SELECT * FROM customers;')

@metric
def avg_customer_workflows(customers: pd.DataFrame) -> pd.DataFrame:
    return customers['num_workflows'].mean() 
    
avg_num_wflows = avg_customer_workflows(customers)
avg_num_wflows.get()
```

Here, we created a function called `avg_customer_workflows` that returns the mean of the `num_workflows` column in the customers table. When we apply it to the `customers` table, we get a Metric in response -- as with all computation in Aqeuduct, the results are lazily computed when you call `.get()`.

Metrics can also have bounds on them, which enforce minimum or maximum values of the metric. If a metric exceeds one of its bounds, Aqueduct will automatically detect this and raise an error for you. Bounds are, in practice, a form of [Checks](checks-ensuring-correctness.md), and we will cover creating bounds in that documentation.

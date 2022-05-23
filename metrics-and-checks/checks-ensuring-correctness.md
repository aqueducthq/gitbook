# Checks: Ensuring Correctness

**Checks** are correctness constraints on a workflow. A Check is a function that accepts a combination of TableArtifacts and Metrics and returns a boolean value indicating whether the workflow is correct or not.&#x20;

```python
from aqueduct import check, CheckSeverity

db = client.integration('aqueduct_demo')
customers = db.sql('SELECT * FROM customers;')

@check(severity=CheckSeverity.ERROR)
def non_negative_workflows(customers: pd.DataFrame) -> boolean:
    return (customers['num_workflows'] > 0).all()
    
check_non_neg_wflows = non_negative_workflows(customers)
check_non_neg_wflows.get()
```

Here, we created a function called `non_negative_workflows` and returns `True` is every value of in the `num_workflows` column is ≥ 0 and `False` otherwise -- obviously, our dataset is broken if there are a negative number of workflows listed for a particular customer.&#x20;

If this Check returns `False`, the workflow that contains it will stop execution and return an error.&#x20;

In this example, we applied a check to a single TableArtifact -- however, Checks can be applied to any combination of TableArtifacts and Metrics.

### Check Severity

Each Check can have one of two severity levels -- `WARNING` and `ERROR`:

* A warning Check will notify you when it fails, but it will not cause the execution of the workflow to stop. In other words, a workflow that only has a failed warning Check will execute to completion and persist its results.
* An error Check will fail catastrophically -- if an error Check fails, the execution of the workflow will cease, and nothing that is schedule to execute after the error Check will be scheduled or executed.

### Adding Bounds to Metrics

A [Metric](../metrics-and-checks.md) is a measurement of a workflow (see the Metrics documentation for more details):

{% content-ref url="metrics-measuring-your-predictions.md" %}
[metrics-measuring-your-predictions.md](metrics-measuring-your-predictions.md)
{% endcontent-ref %}

A common type of Check to create when writing a workflow is to enforce a bound on a `Metric` -- for example, we as a part of the [churn-ensemble.md](../example-workflows/churn-ensemble.md "mention") workflow, we might create a metric that measures what percentage of our customers are likely to churn off of our service.&#x20;

From talking to the sales team, we know that they expect that 20% of customers are considered "at risk" at any given time, so we'll set a bound at 25% -- if more than 25% of our customers are marked as likely to churn, we either have an issue with the data or with our business, but either way, we'll want to know immediately.

```python
from aqueduct import check, metric, CheckSeverity

# Create your churn workflow here.

@metric
def calc_churn_ratio(churn_table: pd.DataFrame) -> float:
    # A customer is "at risk" if their churn likelihood is > 50%.
    churn_table['pred_churn'] = churn_table['prob_churn'] > .5
    return churn_table['pred_churn'].mean()
    
at_risk_ratio = calc_churn_ratio(churn_table)
at_risk_ratio.bound(upper=.25, severity=CheckSeverity.ERROR)
```

The `.bound()` call on the last line creates a Check for us that guarantees that if `at_risk_ratio` exceeds 25%, we will see an error.

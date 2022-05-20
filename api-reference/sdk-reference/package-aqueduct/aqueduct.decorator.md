<a id="aqueduct.decorator"></a>

# aqueduct.decorator

<a id="aqueduct.decorator.wrap_spec"></a>

#### wrap\_spec

```python
def wrap_spec(spec: OperatorSpec,
              *input_artifacts: InputArtifact,
              op_name: str,
              description: str = "") -> OutputArtifact
```

Applies a python function to existing artifacts.
The function must be named predict() on a class named "Function",
in a file named "model.py":

>>> class Function:
>>>     def predict(self, *args):
>>>         ...

**Arguments**:

  spec:
  The spec of the operator.
  *input_artifacts:
  All the artifacts that will serve as input to the python function.
  The function must have the same number of parameters as input
  artifacts.
  op_name:
  The name of the operator that generated this artifact.
  description:
  The description for this operator.

<a id="aqueduct.decorator.op"></a>

#### op

```python
def op(
    name: Optional[Union[str, UserFunction]] = None,
    description: Optional[str] = None,
    file_dependencies: Optional[List[str]] = None
) -> Union[DecoratedFunction, OutputArtifactFunction]
```

Decorator that converts regular python functions into an operator.

Calling the decorated function returns a TableArtifact. The decorated function
can take any number of artifact inputs.

The requirements.txt file in the current directory is used. If no such file exists,
we will infer the requirements that the function needs.

**Arguments**:

  name:
  Operator name.
  description:
  A description for the operator.
  file_dependencies:
  A list of relative paths to files that the function needs to access.
  Python classes/methods already imported within the function's file
  need not be included.
  

**Examples**:

  The op name is inferred from the function name. The description is pulled from the function
  docstring or can be explicitly set in the decorator.
  
  >>> @op
  ... def compute_recommendations(customer_profiles, recent_clicks):
  ...     return recommendations
  >>> customer_profiles = db.sql("SELECT * from user_profile", db="postgres")
  >>> recent_clicks = db.sql("SELECT * recent_clicks", db="google_analytics/shopping")
  >>> recommendations = compute_recommendations(customer_profiles, recent_clicks)
  
  `recommendations` is a TableArtifact representing the result of `compute_recommendations()`.
  
  >>> recommendations.get()

<a id="aqueduct.decorator.metric"></a>

#### metric

```python
def metric(
    name: Optional[Union[str, MetricFunction]] = None,
    description: Optional[str] = None
) -> Union[DecoratedMetricFunction, OutputArtifactFunction]
```

Decorator that converts regular python functions into a metric.

Calling the decorated function returns a MetricArtifact. The decorated function
can take any number of artifact inputs.

The requirements.txt file in the current directory is used. If no such file exists,
we will infer the requirements that the function needs.

**Arguments**:

  name:
  Operator name.
  description:
  A description for the metric.
  

**Examples**:

  The metric name is inferred from the function name. The description is pulled from the function
  docstring or can be explicitly set in the decorator.
  
  >>> @metric()
  ... def avg_churn(churn_table):
  ...     return churn_table['pred_churn'].mean()
  >>> churn_table = db.sql("SELECT * from churn_table")
  >>> churn_metric = avg_churn(churn_table)
  
  `churn_metric` is a MetricArtifact representing the result of `avg_churn()`.
  
  >>> churn_metric.get()

<a id="aqueduct.decorator.check"></a>

#### check

```python
def check(
    name: Optional[Union[str, CheckFunction]] = None,
    description: Optional[str] = None,
    severity: CheckSeverity = CheckSeverity.WARNING
) -> Union[DecoratedCheckFunction, OutputArtifactFunction]
```

Decorator that converts a regular python function into a check.

Calling the decorated function returns a CheckArtifact. The decorated python function
can have any number of artifact inputs.

The requirements.txt file in the current directory is used. If no such file exists,
we will infer the requirements that the function needs.

A check can be set with either WARNING or ERROR severity. A failing check with ERROR severity
will fail the workflow when run in our system.

**Arguments**:

  name:
  Operator name.
  description:
  A description for the check.
  severity:
  The severity level of the check if it fails.
  

**Examples**:

  The check name is inferred from the function name. The description is pulled from the function
  docstring or can be explicitly set in the decorator.
  
  >>> @check(
  ... description="The average predicted churn is below 0.1",
  ... severity=CheckSeverity.ERROR,
  ... )
  ... def avg_churn_is_low(churn_table):
  ...     return churn_table['pred_churn'].mean() < 0.1
  >>> churn_is_low_check = avg_churn_is_low(churn_table_artifact)
  
  `churn_is_low_check` is a CheckArtifact representing the result of `avg_churn_is_low()`.
  
  >>> churn_is_low_check.get()


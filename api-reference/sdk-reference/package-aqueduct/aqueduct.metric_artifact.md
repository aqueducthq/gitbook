# Table of Contents

* [aqueduct.metric\_artifact](#aqueduct.metric_artifact)
  * [MetricArtifact](#aqueduct.metric_artifact.MetricArtifact)
    * [get](#aqueduct.metric_artifact.MetricArtifact.get)
    * [list\_preset\_checks](#aqueduct.metric_artifact.MetricArtifact.list_preset_checks)
    * [bound](#aqueduct.metric_artifact.MetricArtifact.bound)
    * [describe](#aqueduct.metric_artifact.MetricArtifact.describe)

<a id="aqueduct.metric_artifact"></a>

# aqueduct.metric\_artifact

<a id="aqueduct.metric_artifact.MetricArtifact"></a>

## MetricArtifact Objects

```python
class MetricArtifact(Artifact)
```

This class represents a computed metric within the flow's DAG.

Any `@metric`-annotated python function that returns a float will
return this class when that function is called.

**Examples**:

  >>> @metric
  >>> def compute_metric(df):
  >>>     return metric
  >>> metric_artifact = compute_metric(input_artifact)
  
  The contents of this artifact can be manifested locally.
  
  >>> val = metric_artifact.get()

<a id="aqueduct.metric_artifact.MetricArtifact.get"></a>

#### get

```python
def get(parameters: Optional[Dict[str, Any]] = None) -> float
```

Materializes a MetricArtifact into its immediate float value.

**Returns**:

  The evaluated metric as a float.
  

**Raises**:

  InvalidRequestError:
  An error occurred because of an issue with the user's code or inputs.
  InternalServerError:
  An unexpected error occurred within the Aqueduct cluster.

<a id="aqueduct.metric_artifact.MetricArtifact.list_preset_checks"></a>

#### list\_preset\_checks

```python
def list_preset_checks() -> List[str]
```

Returns a list of all preset checks available on the metric artifact.
These preset checks can be set via the bound() method on a artifact.

**Returns**:

  A list of available preset checks on a metric

<a id="aqueduct.metric_artifact.MetricArtifact.bound"></a>

#### bound

```python
def bound(upper: Optional[float] = None,
          lower: Optional[float] = None,
          equal: Optional[float] = None,
          notequal: Optional[float] = None,
          severity: CheckSeverity = CheckSeverity.WARNING) -> CheckArtifact
```

Computes a bounds check on this metric with the specified boundary condition.

Only one of `upper` and `lower` can be set.

>>> metric_artifact.bound(upper = 0.9, severity = CheckSeverity.Error)

If the metric ever exceeds 0.9, the flow will fail.

**Arguments**:

  upper:
  Sets an upper bound on the value of the metric.
  lower:
  Sets a lower bound on the value of the metric.
  severity:
  If specified, will set the severity of this check as specified. Defaults to CheckSeverity.WARNING
  

**Returns**:

  A check artifact bound to this metric.

<a id="aqueduct.metric_artifact.MetricArtifact.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the metric artifact.


# Table of Contents

* [aqueduct.check\_artifact](#aqueduct.check_artifact)
  * [CheckArtifact](#aqueduct.check_artifact.CheckArtifact)
    * [get](#aqueduct.check_artifact.CheckArtifact.get)
    * [describe](#aqueduct.check_artifact.CheckArtifact.describe)

<a id="aqueduct.check_artifact"></a>

# aqueduct.check\_artifact

<a id="aqueduct.check_artifact.CheckArtifact"></a>

## CheckArtifact Objects

```python
class CheckArtifact(Artifact)
```

This class represents a check within the flow's DAG.

Any `@check`-annotated python function that returns a boolean will
return this class when that function is called called. This also
is returned from pre-defined functions like metric.bound(...).

**Examples**:

  >>> @check
  >>> def check_something(df1, metric) -> bool:
  >>> return check_result
  >>>
  >>> check_artifact = check_something(table_artifact, metric_artifact)
  
  The contents of the check artifact can be manifested locally:
  
  >>> assert check_artifact.get()

<a id="aqueduct.check_artifact.CheckArtifact.get"></a>

#### get

```python
def get(parameters: Optional[Dict[str, Any]] = None) -> bool
```

Materializes a CheckArtifact into a boolean.

**Returns**:

  A boolean representing whether the check passed or not.
  

**Raises**:

  InvalidRequestError:
  An error occurred because of an issue with the user's code or inputs.
  InternalServerError:
  An unexpected error occurred in the server.

<a id="aqueduct.check_artifact.CheckArtifact.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the check artifact.


<a id="aqueduct.table_artifact"></a>

# aqueduct.table\_artifact

<a id="aqueduct.table_artifact.TableArtifact"></a>

## TableArtifact Objects

```python
class TableArtifact(Artifact)
```

This class represents a computed table within the flow's DAG.

Any `@op`-annotated python function that returns a dataframe will
return this class when that function is called called.

**Examples**:

  >>> @op
  >>> def predict(df):
  >>>     return predictions
  >>>
  >>> output_artifact = predict(input_artifact)
  
  The contents of these artifacts can be manifested locally or written to an
  integration:
  
  >>> df = output_artifact.get()
  >>> print(df.head())
  >>> output_artifact.save(warehouse.config(table_name="output_table"))

<a id="aqueduct.table_artifact.TableArtifact.get"></a>

#### get

```python
def get() -> pd.DataFrame
```

Materializes TableArtifact into an actual dataframe.

**Returns**:

  A dataframe containing the tabular contents of this artifact.
  

**Raises**:

  InvalidRequestError:
  An error occurred because of an issue with the user's code or inputs.
  InternalServerError:
  An unexpected error occurred within the Aqueduct cluster.

<a id="aqueduct.table_artifact.TableArtifact.save"></a>

#### save

```python
def save(config: SaveConfig) -> None
```

Configure this artifact to be written to a specific integration after its computed.

>>> db = client.integration(name="demo/")
>>> customer_data = db.sql("SELECT * from customers")
>>> churn_predictions = predict_churn(customer_data)
>>> churn_predictions.save(config=db.config(table="churn_predictions"))

**Arguments**:

  config:
  SaveConfig object generated from integration using
  the <integration>.config(...) method.

**Raises**:

  InvalidIntegrationException:
  An error occurred because the requested integration could not be
  found.

<a id="aqueduct.table_artifact.TableArtifact.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the table artifact.

<a id="aqueduct.table_artifact.TableArtifact.remove_check"></a>

#### remove\_check

```python
def remove_check(name: str) -> None
```

Remove a check on this artifact by name.

**Raises**:

- `InvalidUserActionException` - if a matching check operator could not be found.


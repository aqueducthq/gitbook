<a id="aqueduct.integrations.sql_integration"></a>

# aqueduct.integrations.sql\_integration

<a id="aqueduct.integrations.sql_integration.RelationalDBIntegration"></a>

## RelationalDBIntegration Objects

```python
class RelationalDBIntegration(Integration)
```

Class for RealtionalDB integrations.

<a id="aqueduct.integrations.sql_integration.RelationalDBIntegration.list_tables"></a>

#### list\_tables

```python
def list_tables() -> pd.DataFrame
```

Lists the tables available in the RealtionalDB integration.

**Returns**:

  pd.DataFrame of available tables.

<a id="aqueduct.integrations.sql_integration.RelationalDBIntegration.table"></a>

#### table

```python
def table(name: str) -> pd.DataFrame
```

Retrieves a table from a RealtionalDB integration.

**Arguments**:

  name:
  The name of the table to retrieve.
  

**Returns**:

  pd.DataFrame of the table to retrieve.

<a id="aqueduct.integrations.sql_integration.RelationalDBIntegration.sql"></a>

#### sql

```python
def sql(query: Union[str, RelationalDBExtractParams],
        name: Optional[str] = None,
        description: str = "") -> TableArtifact
```

Runs a SQL query against the RealtionalDB integration.

**Arguments**:

  query:
  The query to run.
  name:
  Name of the query.
  description:
  Description of the query.
  

**Returns**:

  TableArtifact representing result of the SQL query.

<a id="aqueduct.integrations.sql_integration.RelationalDBIntegration.config"></a>

#### config

```python
def config(table: str, update_mode: LoadUpdateMode) -> SaveConfig
```

Configuration for saving to RelationalDB Integration.

**Arguments**:

  table:
  Table to save to.
  update_mode:
  The update mode to use when saving the artifact as a relational table.
  Possible values are: APPEND, REPLACE, or FAIL.

**Returns**:

  SaveConfig object to use in TableArtifact.save()

<a id="aqueduct.integrations.sql_integration.RelationalDBIntegration.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the SQL integration.


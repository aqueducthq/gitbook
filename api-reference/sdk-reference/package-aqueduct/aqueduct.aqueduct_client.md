# Table of Contents

* [aqueduct.aqueduct\_client](#aqueduct.aqueduct_client)
  * [Client](#aqueduct.aqueduct_client.Client)
    * [\_\_init\_\_](#aqueduct.aqueduct_client.Client.__init__)
    * [github](#aqueduct.aqueduct_client.Client.github)
    * [list\_integrations](#aqueduct.aqueduct_client.Client.list_integrations)
    * [integration](#aqueduct.aqueduct_client.Client.integration)
    * [publish\_flow](#aqueduct.aqueduct_client.Client.publish_flow)
    * [trigger](#aqueduct.aqueduct_client.Client.trigger)
    * [delete\_flow](#aqueduct.aqueduct_client.Client.delete_flow)
    * [show\_dag](#aqueduct.aqueduct_client.Client.show_dag)
    * [describe](#aqueduct.aqueduct_client.Client.describe)

<a id="aqueduct.aqueduct_client"></a>

# aqueduct.aqueduct\_client

<a id="aqueduct.aqueduct_client.Client"></a>

## Client Objects

```python
class Client()
```

This class allows users to interact with flows on their Aqueduct cluster.

<a id="aqueduct.aqueduct_client.Client.__init__"></a>

#### \_\_init\_\_

```python
def __init__(api_key: str,
             aqueduct_address: str,
             log_level: int = logging.ERROR)
```

Creates an instance of Client.

**Arguments**:

  api_key:
  The user unique API key provided by Aqueduct.
  aqueduct_address:
  The address of the Aqueduct Gateway service.
  log_level:
  A indication of what level and above to print logs from the sdk.
  Defaults to printing error and above only. Types defined in: https://docs.python.org/3/howto/logging.html
  

**Returns**:

  A Client instance.

<a id="aqueduct.aqueduct_client.Client.github"></a>

#### github

```python
def github(repo: str, branch: str = "") -> Github
```

Retrieves a Github object connecting to specified repos and branch.

You can only connect to a public repo if you didn't already connect a
Github account to your Aqueduct account.

**Arguments**:

  repo:
  The full github repo URL, e.g. "my_organization/my_repo"
  branch:
  Optional branch name. The default main branch will be used if not specified.
  

**Returns**:

  A github integration object linked to the repo and branch.

<a id="aqueduct.aqueduct_client.Client.list_integrations"></a>

#### list\_integrations

```python
def list_integrations() -> Dict[str, IntegrationInfo]
```

Retrieves a dictionary of integrations the client can use.

**Returns**:

  A dictionary mapping from integration name to additional info.

<a id="aqueduct.aqueduct_client.Client.integration"></a>

#### integration

```python
def integration(
    name: str
) -> Union[SalesforceIntegration, S3Integration, GoogleSheetsIntegration,
           RelationalDBIntegration, ]
```

Retrieves a connected integration object.

**Arguments**:

  name:
  The name of the integration
  

**Returns**:

  The integration object with the given name.
  

**Raises**:

  InvalidIntegrationException:
  An error occurred because the cluster is not connected to the
  provided integration or the provided integration is of an
  incompatible type.

<a id="aqueduct.aqueduct_client.Client.publish_flow"></a>

#### publish\_flow

```python
def publish_flow(name: str,
                 description: str = "",
                 schedule: str = "",
                 k_latest_runs: int = -1,
                 artifacts: Optional[List[GenericArtifact]] = None) -> Flow
```

Uploads and kicks off the given flow in the system.

If a flow already exists with the same name, the existing flow will be updated
to this new state.

**Arguments**:

  name:
  The name of the newly created flow.
  description:
  A description for the new flow.
- `schedule` - A cron expression specifying the cadence that this flow
  will run on. If empty, the flow will only execute manually.
  For example, to run at the top of every hour:
  
  >> schedule = aqueduct.hourly(minute: 0)
  
  k_latest_runs:
  Number of most-recent runs of this flow that Aqueduct should store.
  Runs outside of this bound are deleted. Defaults to persisting all runs.
  artifacts:
  All the artifacts that you care about computing. These artifacts are guaranteed
  to be computed. Additional artifacts may also be included as intermediate
  computation steps. All checks are on the resulting flow are also included.

**Raises**:

  InvalidCronStringException:
  An error occurred because the supplied schedule is invalid.
  IncompleteFlowException:
  An error occurred because you are missing some required fields or operators.
  

**Returns**:

  A flow object handle to be used to fetch information about this productionized flow.

<a id="aqueduct.aqueduct_client.Client.trigger"></a>

#### trigger

```python
def trigger(flow_id: Union[str, uuid.UUID]) -> None
```

Immediately triggers another run of the provided flow.

**Arguments**:

  flow_id:
  The id of the workflow to delete (not the name)
  

**Raises**:

  InvalidRequestError:
  An error occurred when attempting to fetch the workflow to
  delete. The provided `flow_id` may be malformed.
  InternalServerError:
  An unexpected error occurred within the Aqueduct cluster.

<a id="aqueduct.aqueduct_client.Client.delete_flow"></a>

#### delete\_flow

```python
def delete_flow(flow_id: Union[str, uuid.UUID]) -> None
```

Deletes a flow object.

**Arguments**:

  flow_id:
  The id of the workflow to delete (not the name)
  

**Raises**:

  InvalidRequestError:
  An error occurred when attempting to fetch the workflow to
  delete. The provided `flow_id` may be malformed.
  InternalServerError:
  An unexpected error occurred within the Aqueduct cluster.

<a id="aqueduct.aqueduct_client.Client.show_dag"></a>

#### show\_dag

```python
def show_dag(artifacts: Optional[List[GenericArtifact]] = None) -> None
```

Prints out the flow as a pyplot graph.

A user outside the notebook environment will be redirected to a page in their browser
containing the graph.

**Arguments**:

  artifacts:
  If specified, the subgraph terminating at these artifacts will be specified.
  Otherwise, the entire graph is printed.

<a id="aqueduct.aqueduct_client.Client.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out info about this client in a human-readable format.


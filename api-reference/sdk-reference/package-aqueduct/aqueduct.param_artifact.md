# Table of Contents

* [aqueduct.param\_artifact](#aqueduct.param_artifact)
  * [ParamArtifact](#aqueduct.param_artifact.ParamArtifact)
    * [\_\_init\_\_](#aqueduct.param_artifact.ParamArtifact.__init__)

<a id="aqueduct.param_artifact"></a>

# aqueduct.param\_artifact

<a id="aqueduct.param_artifact.ParamArtifact"></a>

## ParamArtifact Objects

```python
class ParamArtifact(Artifact)
```

<a id="aqueduct.param_artifact.ParamArtifact.__init__"></a>

#### \_\_init\_\_

```python
def __init__(api_client: APIClient, dag: DAG, artifact_id: uuid.UUID)
```

The APIClient is only included because decorated functions operators acting on this parameter
will need a handle to an API client.


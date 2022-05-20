# Table of Contents

* [aqueduct.generic\_artifact](#aqueduct.generic_artifact)
  * [Artifact](#aqueduct.generic_artifact.Artifact)
    * [id](#aqueduct.generic_artifact.Artifact.id)
    * [name](#aqueduct.generic_artifact.Artifact.name)

<a id="aqueduct.generic_artifact"></a>

# aqueduct.generic\_artifact

<a id="aqueduct.generic_artifact.Artifact"></a>

## Artifact Objects

```python
class Artifact(ABC)
```

<a id="aqueduct.generic_artifact.Artifact.id"></a>

#### id

```python
def id() -> uuid.UUID
```

Fetch the id associated with this artifact.

This id will not exist in the system if the artifact has not yet been published.

<a id="aqueduct.generic_artifact.Artifact.name"></a>

#### name

```python
def name() -> str
```

Fetch the name of this artifact.


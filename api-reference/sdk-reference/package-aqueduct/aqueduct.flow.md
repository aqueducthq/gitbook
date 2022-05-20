<a id="aqueduct.flow"></a>

# aqueduct.flow

<a id="aqueduct.flow.Flow"></a>

## Flow Objects

```python
class Flow()
```

This class is a read-only handle to a workflow object that in the system.

<a id="aqueduct.flow.Flow.id"></a>

#### id

```python
def id() -> uuid.UUID
```

Returns the id of the flow.

<a id="aqueduct.flow.Flow.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the flow.

**Raises**:

  ArtifactNotFoundException:
  An error occurred because the artifact spec is not a supported type.


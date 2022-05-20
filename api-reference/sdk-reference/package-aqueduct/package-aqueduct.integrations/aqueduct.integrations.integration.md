# Table of Contents

* [aqueduct.integrations.integration](#aqueduct.integrations.integration)
  * [IntegrationInfo](#aqueduct.integrations.integration.IntegrationInfo)
    * [describe](#aqueduct.integrations.integration.IntegrationInfo.describe)
  * [Integration](#aqueduct.integrations.integration.Integration)

<a id="aqueduct.integrations.integration"></a>

# aqueduct.integrations.integration

<a id="aqueduct.integrations.integration.IntegrationInfo"></a>

## IntegrationInfo Objects

```python
class IntegrationInfo(BaseModel)
```

<a id="aqueduct.integrations.integration.IntegrationInfo.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the integration.

<a id="aqueduct.integrations.integration.Integration"></a>

## Integration Objects

```python
class Integration(ABC)
```

Abstract class for the various integrations Aqueduct interacts with.


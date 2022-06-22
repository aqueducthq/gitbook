# Table of Contents

* [aqueduct.operators](#aqueduct.operators)
  * [GithubMetadata](#aqueduct.operators.GithubMetadata)
  * [serialize\_parameter\_value](#aqueduct.operators.serialize_parameter_value)

<a id="aqueduct.operators"></a>

# aqueduct.operators

<a id="aqueduct.operators.GithubMetadata"></a>

## GithubMetadata Objects

```python
class GithubMetadata(BaseModel)
```

Specifies a destination in github integration.
There are two ways to specify the content:
-   by `path`, which points to a file or dir in the github repo.
-   from `repo_config_content_type` and `repo_config_content_name`, which points to
    information stored in the repo's `.aqconfig`.
If using `repo_config` content, backend will ignore `path` and overwrite it with
the `path` specified in `.aqconfig`.

<a id="aqueduct.operators.serialize_parameter_value"></a>

#### serialize\_parameter\_value

```python
def serialize_parameter_value(name: str, val: Any) -> str
```

A parameter must be JSON serializable.


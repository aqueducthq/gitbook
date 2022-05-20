# Table of Contents

* [aqueduct.enums](#aqueduct.enums)
  * [MetaEnum](#aqueduct.enums.MetaEnum)
  * [CheckSeverity](#aqueduct.enums.CheckSeverity)
  * [GithubRepoConfigContentType](#aqueduct.enums.GithubRepoConfigContentType)

<a id="aqueduct.enums"></a>

# aqueduct.enums

<a id="aqueduct.enums.MetaEnum"></a>

## MetaEnum Objects

```python
class MetaEnum(EnumMeta)
```

Allows to very easily check if strings are present in the enum, without a helper.

Eg.
    if "Postgres" in ServiceType:
        ...

<a id="aqueduct.enums.CheckSeverity"></a>

## CheckSeverity Objects

```python
class CheckSeverity(str, Enum, metaclass=MetaEnum)
```

An ERROR severity will fail the flow.

<a id="aqueduct.enums.GithubRepoConfigContentType"></a>

## GithubRepoConfigContentType Objects

```python
class GithubRepoConfigContentType(str, Enum, metaclass=MetaEnum)
```

Github repo config (.aqconfig) content type.


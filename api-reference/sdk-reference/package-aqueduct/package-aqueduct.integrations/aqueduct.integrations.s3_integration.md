# Table of Contents

* [aqueduct.integrations.s3\_integration](#aqueduct.integrations.s3_integration)
  * [S3Integration](#aqueduct.integrations.s3_integration.S3Integration)
    * [file](#aqueduct.integrations.s3_integration.S3Integration.file)
    * [config](#aqueduct.integrations.s3_integration.S3Integration.config)
    * [describe](#aqueduct.integrations.s3_integration.S3Integration.describe)

<a id="aqueduct.integrations.s3_integration"></a>

# aqueduct.integrations.s3\_integration

<a id="aqueduct.integrations.s3_integration.S3Integration"></a>

## S3Integration Objects

```python
class S3Integration(Integration)
```

Class for S3 integration.

<a id="aqueduct.integrations.s3_integration.S3Integration.file"></a>

#### file

```python
def file(filepath: str,
         format: S3FileFormat,
         name: Optional[str] = None,
         description: str = "") -> TableArtifact
```

Retrieves a file from the S3 integration.

**Arguments**:

  filepath:
  Filepath to retrieve from.
  name:
  Name of the query.
  description:
  Description of the query.
  

**Returns**:

  TableArtifact representing the S3 File.

<a id="aqueduct.integrations.s3_integration.S3Integration.config"></a>

#### config

```python
def config(filepath: str, format: S3FileFormat) -> SaveConfig
```

Configuration for saving to S3 Integration.

**Arguments**:

  filepath:
  S3 Filepath to save to.
  format:
  S3 Fileformat to save as. Can be CSV, JSON, or Parquet.

**Returns**:

  SaveConfig object to use in TableArtifact.save()

<a id="aqueduct.integrations.s3_integration.S3Integration.describe"></a>

#### describe

```python
def describe() -> None
```

Prints out a human-readable description of the S3 integration.


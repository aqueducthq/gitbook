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
def file(filepaths: Union[List[str], str],
         format: S3FileFormat,
         name: Optional[str] = None,
         description: str = "") -> TableArtifact
```

Reads multiple files from the S3 integration into a single TableArtifact.

**Arguments**:

  filepaths:
  Filepath to retrieve from. The filepaths can either be:
  1) a single string that represents a file name or a directory name. The directory
  name must ends with a `/`. In case of a file name, we attempt to retrieve that file,
  and in case of a directory name, we do a prefix search on the directory and retrieve
  all matched files and concatenate them into one.
  2) a list of strings representing the file name. Note that in this case, we do not
  accept directory names in the list.

  format:
  The format of the S3 files. We currently support JSON, CSV, and Parquet. Note that currently,
  when multiple files are retrieved, these files must have the same format.

  name:
  Name of the query.

  description:
  Description of the query.
  

**Returns**:

  TableArtifact representing the concatenated S3 Files.

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


---
description: Encapsulating data in Aqueduct
---

# Artifacts

_Artifacts_ are thin wrappers around data objects in Aqueduct. Wrapping data in an Artifact allows Aqueduct to track that artifact across workflow runs help you understand how your data is changing over time.&#x20;

Aqueduct currently only supports `TableArtifact`s, which are encapsulations of tabular or relational data. We plan to add support for other data types in the future.

There are three operations that can be done on artifacts:

* [Saving an Artifact](saving-an-artifact.md)
* Transforming an Artifact -- this is done by passing an Artifact as an argument to an [Operator](../operators.md)
* Attaching a [Metric or a Check](../metrics-and-checks.md) to an artifact -- again, this can be done by passing an Artifact as an argument to the relevant Metric or Check

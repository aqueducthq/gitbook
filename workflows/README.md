---
description: All code in Aqueduct runs as a part of a workflow.
---

# Workflows

The workflow is the abstraction at the center of Aqueduct's prediction infrastructure.&#x20;

_A **workflow** is a sequence of transformations, correctness checks, and metrics over one or more pieces of data and is run either on-demand or on a fixed scheduled._&#x20;

A workflow is created from the Aqueduct Python API and is composed of one or more _**operator**_(s), each of which takes in one or more _**artifact**_(s) and returns one or more _**artifact**_(s).
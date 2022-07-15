# Metrics & Checks

In addition to providing a simple API to define and publish workflows, Aqueduct makes it easy for you to monitor your workflows once they have been created. There are two key mechanisms by which you can monitor your workflows:

* [**Metrics**](metrics-and-checks/metrics-measuring-your-predictions/):allow you to measure your workflows by computing a numerical value over an Artifact ( or other metrics).&#x20;
* [**Checks**](metrics-and-checks/checks-ensuring-correctness.md):allow you to ensure the correctness of your workflow by taking in one or more Artifacts and Metrics and returning a boolean value indicating the correctness of the workflow.

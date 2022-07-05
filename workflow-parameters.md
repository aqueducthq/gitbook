# Workflow Parameters

Oftentimes, arguments to workflows might not be known until runtime. A workflow might even need to be run multiple times with different parameters. 

We allow you to parameterize your workflows with parameter artifacts, which are just a type of artifact that wraps a mutable, jsonable value. 
They are built into your workflow just like any other artifact. However, their values can be adjusted and workflows triggered with the new values.

See this [**Workflow Tutorial**](example-workflows/using-workflow-parameters.md) for a walkthrough of the API.



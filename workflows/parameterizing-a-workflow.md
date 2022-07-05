# Workflow Parameters

Oftentimes, arguments to workflows might not be known until runtime. A workflow might even need to be run multiple times with different parameters.

We allow you to parameterize your workflows with parameter artifacts, which are just a type of artifact that wraps a mutable, jsonable value.
They are built into your workflow just like any other artifact. However, their values can be adjusted and workflows re-run with the new values.

See this [**Workflow Tutorial**](example-workflows/using-workflow-parameters.md) for a walkthrough of the API.


## Built-in SQL Parameters

We provide the following predefined parameters to make your life easier, available only to SQL queries. These are overridden by any user-defined parameters with the same name.

- `today`: expands to a string date in the format `'%Y-%m-%d'`. Eg. `SELECT * from hotel_reviews WHERE review_date={{ today }};`


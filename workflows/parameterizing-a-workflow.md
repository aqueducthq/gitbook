# Workflow Parameters

See this [**Workflow Parameters Tutorial**](../example-workflows/using-workflow-parameters.md) notebook for a walkthrough of the API.

Oftentimes, arguments to workflows might not be known until runtime. A workflow might even need to be run multiple times with different parameters.

We allow you to parameterize your workflows with a special type of artifact called a Parameter artifact. You can create such an artifact through the Aqueduct 
client with `client.create_param(<name>, default=<default_val>)`. The returned artifact can then be consumed as an argument to an operator, just like any other
artifact (eg. `predict_op(param_artifact)`). The parameter can also be used in SQL query strings with the `{{<param_name>}}` syntax, where the double-bracket pattern
is expanded automatically into the parameter value (which must be a string). Note that if you need quotes in your SQL query, you'll have to insert them yourself
either around the brackets or in the parameter string itself. An example of the latter case: inserting parameter `"'my_col'"` into `select ... where col_name={{param_name}}"` 
becomes `select .. where col_name='my_col'`.

Every parameter must have a default value. This is so that when a workflow is run without explicitly defined parameters, it always has a value it can use.
The value of a parameter can be any type, as long as Aqueduct can convert it into a json string with `JSON.dumps()`. The exception is SQL query parameters,
which must be a string type.

The value of a parameter in the workflow can be changed by supplying a dictionary mapping the parameter name to its new value. To trigger a workflow with different parameters, call `client.trigger(parameters={<param_name>: <param_val>})`. Similarly, to realize an artifact using different parameters, use
`artifact.get(parameters={<param_name>: <param_val>)`. The new parameter value does not have to be the same type as its default value.

Note that triggering a workflow with non-default parameters is a one-off operation. It will not change the default parameters for future runs of the workflow. 
That is to say, if a workflow is running on a schedule, every scheduled run will use the same default parameters. To change the parameters for future runs,
you must edit the default value of the parameter itself and re-publish the workflow. This should be as simple as re-running your notebook with the edits.

Parameters do not need to passed into the artifacts list in `client.publish_flow()`. Including any downstream artifacts that rely on these parameters 
will automatically pull the parameters in as well.

## Built-in SQL Parameters

We provide the following predefined parameters to make your life easier, available only to SQL queries. These are overridden by any user-defined parameters with the same name.

- `today`: expands to a string date in the format `'%Y-%m-%d'`. Eg. `SELECT * from hotel_reviews WHERE review_date={{ today }};`


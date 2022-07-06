# Editing a Workflow

Editing a workflow is very similar to [creating-a-workflow.md](creating-a-workflow.md "mention"). After defining your workflow from the SDK, you can simply call `publish_flow` with the same workflow name, and Aqueduct will automatically update your previous workflow spec to reflect the latest change.

Let's say for example that we wanted to update the workflow we created in the [creating-a-workflow.md](creating-a-workflow.md "mention") guide. We decided we didn't care about `fixed_acidity` anymore but only want to consider the `volatile_acidity`. We could run the following code snippet to define the updated workflow and publish it:

```python
db = client.integration('aqueduct_demo')
wine_data = db.sql('SELECT * FROM wine;')

@op
def get_average_acidity(wine_data: pd.DataFrame) -> pd.DataFrame:
    return wine_data.groupby('color').mean('volatile_acidity')
    
acidity_by_group = get_average_acidity(wine_data)
acidity_by_group.save(db.config(table="acidity_by_group"))

flow = client.publish_flow(name='average_acidity', 
                           artifacts=[acidity_by_group],
                           schedule=aqueduct.hourly())
print(flow.id())
```

This would overwrite the previous version of the workflow -- which returned the average by group of the sum of `fixed_acidity` and `volatile_acidity` -- with the updated version of the workflow shown here.

### Editing Workflow Metadata

If you simply want to edit the name or description of a workflow, that can be done from the settings view on the UI. Simply enter the new workflow name and description and hit `Save`.

![](<../.gitbook/assets/image (3) (1) (1) (1).png>)

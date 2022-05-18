# Creating a Workflow

{% hint style="info" %}
This guide assumes you've already created an Aqueduct API client. If you haven't done that, please see the [quickstart-guide.md](../quickstart-guide.md "mention") for more details.
{% endhint %}

### Defining a Workflow

At the root of every workflow is some data. Aqueduct supports loading input data from a variety of sources, but we've found the most common use case is a workflow that starts from a SQL database. We'll use Aqueduct's [demo-data-warehouse.md](../example-workflows/demo-data-warehouse.md "mention") in this example.

```python
db = client.integration('aqueduct_demo/')
wine_data = db.sql('SELECT * FROM wine;')
```

In the snippet above, we've done two things -- first, we've loaded a connection to the `aqueduct_demo` database. This allows us to access any of the data stored in that database iva SQL queries, which is what we do on line 2 -- execute a simple SQL query against `aqueduct_demo`.&#x20;

Once we have some data, now we can start transforming it. Here, we're going to do something really simple -- we'll take the total acidity of each wine (`volatile_acidity` and `fixed_acidity`) and then we'll calculate the average acidity for each `color` of wine. (If this is a _faux pas_ for wine enthusiasts, forgive us -- we're just trying to make up fun examples with interesting data).&#x20;

```python
@op
def get_average_acidity(wine_data: pd.DataFrame) -> pd.DataFrame:
    wine_data['acidity'] = wine_data['fixed_acidity'] + wine_data['volatile_acidity']
    return wine_data.groupby('color').mean('acidity')
```

All of the code we've written here is simple Pandas code. The only change we've made is that we've added the `@op` decorator from the Aqueduct SDK. This tells Aqueduct that when we call the function, as below, to execute it as a part of the workflow that we're currently defining.&#x20;

```python
acidity_by_group = get_average_acidity(wine_data)
```

Let's say that `acidity_by_group` is a piece of data we care about and will reuse -- for that purpose, we want to save it back to our database. We can specify using the Aqueduct SDK where that data should be saved:

```python
acidity_by_group.save(db.config(table="acidity_by_group"))
```

This tells Aqueduct to save `acidity_by_group` to a table of the same name in the demo database (remember that `db` was a connection we loaded to the Aqueduct demo above) whenever the workflow is run.

For this example, we'll stop here -- we know your workflows are likely much more complex than this, but one operator will do for our purposes. To see real-world examples, check out [example-workflows](../example-workflows/ "mention").&#x20;

### Previewing a Workflow

You might've noticed that until this point, we haven't actually executed any of the code that you've written. You've defined a SQL query and a function over the results of that SQL query, but you haven't seen the output of anything. This is because workflows in Aqueduct are _lazily defined_ -- you define your workflow, and we execute it when you ask us to.

However, it would be incredibly frustrating if you defined a beautiful workflow and published it... only to find out that you made a typo. To avoid that disappointment, Aqueduct allows you to preview your workflow at every stage of the pipeline by adding a `.get()` method to any data object. This isn't restricted to just the last operator. If you want a view of any intermediary piece of data, you can simply call `.get()`:

```python
wine_data.get() # Shows you a preview of the input wine table.
acidity_by_group.get() # Shows a preview of the results of `get_average_acidity`
```

### Publishing a Workflow

Once we've defined our whole workflow, the final step is to publish it to Aqueduct. Intuitively, the name of the method we'll use for this is `publish_flow`.&#x20;

```python
flow = client.publish_flow(name='average_acidity', 
                           artifacts=[acidity_by_group],
                           schedule=aqueduct.hourly())
print(flow.id())
```

There are a few key arguments here, and we'll go through the one by one:

* `name`: This is probably self-explanatory, but every workflow is given a unique name.&#x20;
* `artifacts`: This tells Aqueduct what all should be included in the workflow. Here, we specify `acidity_by_group`, which tells Aqueduct to also include everything that was used to create that piece of data -- in this case, the input data `wine_data` and the operator `get_average_acidity`.&#x20;
  * You're, of course, welcome to list out all of the data artifacts in your workflow, but we figured it would be easier to list the outputs you care about.
* `schedule`: This tells us how often you'd like to run your workflow. If you leave this empty, no schedule will be set, and you can set a schedule that executes as quickly as every minute or as rarely as every month. (See [managing-workflow-schedules.md](managing-workflow-schedules.md "mention") for more details.)

Finally, you'll notice that we print `flow.id()` at the end of our workflow. This shows you the UUID assigned to your workflow, which you can use to access the workflow from the Python SDK In the future.

### Viewing a Workflow

Once your workflow's been published, you can go to the Aqueduct UI to see a preview of the workflow. The workflow we've published here will look pretty simple -- a load operator from our demo database, the `wine_data` table, the `get_average_acidity` operator, the resulting `acidity_by_group` table, and the resulting write operator back to the demo database.

**TODO: ADD A SCREENSHOT**

For more on using the UI, check out [aqueduct-ui.md](../aqueduct-ui.md "mention").&#x20;

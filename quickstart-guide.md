---
description: The quickest way to get your first workflow deployed on Aqueduct
---

# Quickstart Guide

### Installation and Setup

First things first, we'll install the Aqueduct pip package and start the Aqueduct server and UI:

```bash
pip3 install aqueduct-ml torch transformers
aqueduct start
```

This will start the backend and UI servers for Aqueduct -- if you have questions or issues with the installation process, please check out our [Installation Guide](installation-and-deployment.md).

Once your server is up and running, you can start building your first workflow. We'll use the workflow that we introduced on the [welcome page](./). We prefer to write our workflows in Jupyter notebooks, but this should work fine from a regular Python interpreter as well.

First, we'll import everything we need:

```python
from aqueduct import Client, get_apikey, op, metric

import pandas as pd
from transformers import pipeline
import torch
```

Note that the `transformers` package uses `torch`, so even though we won't explicitly use `torch` here, we include it to make sure that `transformers` functions correctly.

Next, we'll create our API client:

```python
client = Client.(get_apikey(), "localhost:8080")
```

Note that the API key associated with the server can also be found in the output of the `aqueduct start` command.

And access the base data for our workflow, which is the [hotel reviews dataset](example-workflows/demo-data-warehouse.md). This code does two things -- (1) it loads a connection to a database (in this case the pre-build `aqueduct_demo` DB that comes with the Aqueduct server), and (2) it runs a SQL query against that DB and returns a pointer to the resulting dataset.

```python
demo_db = client.integration("aqueduct_demo")
reviews_table = demo_db.sql("select * from hotel_reviews;")
```

`reviews_table` is an [Artifact](artifacts.md) -- simply a wrapper around some data -- in Aqueduct terminology and will now serve as the base data for our workflow. We can apply Python functions to it in order to transform it.

A piece of Python code that transforms an Artifact is called an [Operator](operators.md), which is simply just a decorated Python function. Here, we'll write an Operator that uses [HuggingFace's Transformers package](https://huggingface.co/docs/transformers/index) to apply a sentiment analysis model to the hotel reviews in our dataset. We can call this function on our `reviews_table` Artifact like a regular Python function.

```python
@op()
def sentiment_prediction(reviews):
    model = pipeline("sentiment-analysis")
    return reviews.join(pd.DataFrame(model(list(reviews['review']))))

sentiment_table = sentiment_prediction(reviews_table)
```

Now that we have a the sentiment analysis models, we can get a preview of our data by calling `.get()`:

```python
sentiment_table.get()
```

Next, we're going to apply a [Metric](metrics-and-checks/metrics-measuring-your-predictions/) to our `sentiment_table`, which will calculate a numerical summary of our predictions (in this case, just the mean sentiment). We will also a apply a bound (which is a type of [Check](metrics-and-checks/checks-ensuring-correctness.md)) to our Metric -- if the average sentiment drops below 0.5, we will raise an error:

```python
@metric
def average_sentiment(reviews_with_sent):
    return (reviews_with_sent["label"] == "POSITIVE").mean()

avg_sent = average_sentiment(sentiment_table)
avg_sent.bound(lower=0.5)
```

Finally, we're going to save `sentiment_table` back to the Aqueduct demo database by calling `.save()` and passing in a configuration for the demo database which indicates the name of the table we will use (see [here](integrations/using-integrations/) for more details), and we save the workflow to the Aqueduct server by calling `publish_flow`.

```python
sentiment_table.save(demo_db.config(table='sentiment_pred', update_mode='replace'))
client.publish_flow(name="hotel_sentiment", artifacts=[sentiment_table])
```

And we're done! 🎉

We've created our first workflow together, and you're off to the races. Here are some next steps:

* Check out our [example workflows](example-workflows/) for some more Aqueduct workflows.
* Check out our guide on [Workflows](workflows/) for a deep dive on how to define, preview, and create workflows.
* Check out our documentation on [Operators](operators.md), [Artifacts](artifacts.md), and [Metrics & Checks](metrics-and-checks.md) for deep dives into creating and interacting with each.

---
description: Open-source prediction infrastructure for data scientists
---

# Welcome to Aqueduct

Aqueduct is open-source prediction infrastructure built for data scientists, by data scientists. With Aqueduct, data scientists can instantaneously deploy machine learning models to the cloud, connect those models to data and business systems, and gain visibility into the performance of their prediction pipelines -- all from the comfort of a Python notebook.

For more on why we're building prediction infrastructure for data scientists see [the-aqueduct-philosophy.md](the-aqueduct-philosophy.md "mention").

The core abstraction in Aqueduct is a [Workflow](workflows/), which is a sequence of [Artifacts](artifacts.md) (data) that are transformed by [Operators](operators.md) (compute). The input Artifact(s) for a Workflow is typically loaded from a database, and the output Artifact(s) are typically persisted back to a database. Each Workflow can either be run on a fixed schedule or triggered on-demand.

The 25-line code snippet below is all you need to create your first prediction pipeline:

```python
import aqueduct as aq
from aqueduct import op, metric
import pandas as pd
# Need to install torch and transformers
#!pip install torch transformers
from transformers import pipeline
import torch

client = aq.Client("YOUR_API_KEY", "localhost:8080")

# This function takes in a DataFrame with the text of user review of
# hotels and returns a DataFrame that has the sentiment of the review.
# This function users the `pipeline` interface from HuggingFace's 
# Transformers package. 
@op()
def sentiment_prediction(reviews):
    model = pipeline("sentiment-analysis")
    predicted_sentiment = model(list(reviews["review"]))
    return reviews.join(pd.DataFrame(predicted_sentiment))

# Load a connection to a database -- here, we use the `aqueduct_demo`
# database, for which you can find the documentation here:
# https://docs.aqueducthq.com/example-workflows/demo-data-warehouse
demo_db = client.integration("aqueduct_demo")

# Once we have a connection to a database, we can run a SQL query against it.
reviews_table = demo_db.sql("select * from hotel_reviews;")

# Next, we apply our annotated function to our data -- this tells Aqueduct 
# to create a workflow spec that applied `sentiment_prediction` to `reviews_table`.
sentiment_table = sentiment_prediction(reviews_table)

# When we call `.save()`, Aqueduct will take the data in `sentiment_table` and 
# write the results back to any database you specify -- in this case, back to the 
# `aqueduct_demo` DB.
sentiment_table.save(demo_db.config(table="sentiment_pred", update_mode="replace"))

# In Aqueduct, a metric is a numerical measurement of a some predictions. Here, 
# we calculate the average sentiment score returned by our machine learning 
# model, which is something we can track over time.
@metric
def average_sentiment(reviews_with_sent):
    return (reviews_with_sent["label"] == "POSITIVE").mean()

avg_sent = average_sentiment(sentiment_table)

# Once we compute a metric, we can set upper and lower bounds on it -- if 
# the metric exceeds one of those bounds, an error will be raised.
avg_sent.bound(lower=0.5)

# We can also request system level metrics such as runtime.
# These can be instiated from a table artifact and represent the runtime of the previous @op that ran on it
sentiment_runtime_metric = sentiment_table.system_metric("runtime")

# Now we can request for the runtime.
# We can also apply bounds on this metric just as any other.
sentiment_runtime_metric.get()


# And we're done! With a call to `publish_flow`, we've created a full workflow
# that calculates the sentiment of hotel reviews, creates a metric over those
# predictions, and sets a bound on that metric.
client.publish_flow(name="hotel_sentiment", artifacts=[sentiment_table, avg_sent])
```

For more on this pipeline, check our [Quickstart Guide](quickstart-guide.md).

### Core Concepts

* [Workflows](workflows/)
* [Integrations](integrations/)
* [Operators](operators.md)
* [Artifacts](artifacts.md)
* [Metrics & Checks](metrics-and-checks.md)

### Example Workflows

* [The Demo Data Warehouse](example-workflows/demo-data-warehouse.md)
* [Churn Ensemble](example-workflows/churn-ensemble.md)
* [Wine Quality Prediction](example-workflows/train-and-inference.md)
* [Sentiment Analysis](example-workflows/sentiment-analysis.md)
* [Using Workflow Parameters](example-workflows/using-workflow-parameters.md)
* Product Recommendation (coming soon!)

### Guides

* [Debugging a Prediction Pipeline](guides/debugging-a-failed-workflow.md)
* Running with Airflow (coming soon!)

### API Reference

* [Python SDK](api-reference/sdk-reference/)
* [Aqueduct CLI](api-reference/aqueduct-cli.md)

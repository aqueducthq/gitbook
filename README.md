---
description: Open-source prediction infrastructure for data scientists
---

# Welcome to Aqueduct

Aqueduct is open-source prediction infrastructure built for data scientists, by data scientists. With Aqueduct, data scientists can instantaneously deploy machine learning models to the cloud, connect those models to data and business systems, and gain visibility into the performance of their prediction pipelines -- all from the comfort of a Python notebook.&#x20;

For more on why we're building prediction infrastructure for data scientists see [the-aqueduct-philosophy.md](the-aqueduct-philosophy.md "mention").

The 25-line code snippet below is all you need to create your first prediction pipeline:

```python
import aqueduct as aq
from aqueduct import op, metric
from transformers import pipeline
import torch

@op()
def sentiment_prediction(reviews):
    model = pipeline("sentiment-analysis")
    return reviews.join(pd.DataFrame(model(list(reviews['review']))))

client = aq.AqueductClient("YOUR_API_KEY")

demo_db = client.integration("aqueduct_demo/")
reviews_table = demo_db.sql("select * from hotel_reviews;")

sentiment_table = sentiment_prediction(reviews_table)
sentiment_table.save(demo_db.config(table='sentiment_pred', update_mode='replace'))

@metric
def average_sentiment(reviews_with_sent):
    return reviews_with_sent['review'].mean()
avg_sent = average_sentiment(sentiment_table)
avg_sent.bound(lower=0.5)

client.publish_flow(name="hotel_sentiment", artifacts=[sentiment_table])
```

For more on this pipeline, check our [Quickstart Guide](quickstart-guide.md).&#x20;

### Core Concepts

* [Workflows](workflows/)
* [Integrations](integrations/)&#x20;
* [Operators](operators.md)
* [Artifacts](artifacts.md)
* [Metrics & Checks](metrics-and-checks.md)

### Example Workflows

* [The Demo Data Warehouse](example-workflows/demo-data-warehouse.md)
* [Churn Ensemble](example-workflows/churn-ensemble.md)
* Wine Quality Prediction (coming soon!)
* [Sentiment Analysis](example-workflows/sentiment-analysis.md)
* Missing Data Imputation (coming soon!)
* Product Recommendation (coming soon!)

### Guides

* Running with Airflow (coming soon!)
* Debugging a Prediction Pipeline (coming soon!)

### API Reference

* [Python SDK](api-reference/sdk-reference/)
* Aqueduct CLI (coming soon!)

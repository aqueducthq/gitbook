# AWS S3

{% hint style="info" %}
Before starting, make sure you've added your S3 bucket as an integration to Aqueduct (see [adding-an-integration](../adding-an-integration/ "mention")).
{% endhint %}

First, we're going to get a connection to your S3 bucket by calling `.integration()` on the Aqueduct Client.&#x20;

```python
my_bucket = client.integration('s3/my_data_bucket')
```

Once we've loaded a connection to our S3 bucket, we can access data by using the `.file()` method. `.file()` takes as an argument the path within your S3 bucket to the file that you'd like to access:

```python
customers = my_bucket.file('data/sales/customers.csv')
```

This returns what we call an [**Artifact**](../../artifacts.md) **** in Aqueduct terminology. An artifact is simply just a wrapper around some data that we'll track a part of your workflow. You can call [Operators](../../operators.md) on your artifacts -- for more on this, see the guide on [Operators](../../operators.md) or [Workflows](../../workflows/page-4.md).

{% hint style="info" %}
Note that Aqueduct currently only supports loading tabular data either in CSVs or in Parquet files from AWS S3. Other file formats aren't yet supported.
{% endhint %}

{% hint style="warning" %}
Aqueduct does not eagerly validate whether or not your file exists. In order to ensure that your file exists, call `.get()` on the resulting Artifact. If the file does not exist or cannot be loaded, you will be notified immediately.
{% endhint %}

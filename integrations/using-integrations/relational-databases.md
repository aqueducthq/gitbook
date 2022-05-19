# Relational Databases

{% hint style="info" %}
Before starting, make sure you've added a connection to your database (see [adding-an-integration](../adding-an-integration/ "mention")).

In this guide, we'll use the Aqueduct Demo DB, but this guide will work for any relational database.
{% endhint %}

Most data scientists we've worked with start with some data in a relational database, so that's where we'll start too.&#x20;

First, we're going to get a connection to your database by calling `.integration()` on the Aqueduct Client.&#x20;

```python
demo_db = client.integration('aqueduct_demo')
```

Once we have a connection to our database, we can simply call `.sql()` on the connection to execute a SQL query:

```python
customers = demo_db.sql('SELECT * FROM customers;')
```

This returns what we call an [**Artifact**](../../artifacts.md) **** in Aqueduct terminology. An artifact is simply just a wrapper around some data that we'll track a part of your workflow. You can call [Operators](../../operators.md) on your artifacts -- for more on this, see the guide on [Operators](../../operators.md) or [Workflows](../../workflows/page-4.md).

{% hint style="warning" %}
Every SQL database has a slightly different dialect of SQL, and Aqueduct does not check for the correctness of your query.&#x20;

In order to validate that your query works correctly, call `.get()` on the artifact Aqueduct returns to you. If there's an error, you will be notified immediately.
{% endhint %}

# Using Integrations

Once you've connected an integration, you can access it from the Python SDK. Every data system is a little different, so we'll walk you through accessing data in each type of integration separately.&#x20;

To get a connection to an integration, you can simply call `.integration()` on the Aqueduct Client:

```python
postgres = client.integration('postgres/analytics')
```

Aqueduct currently supports the following integrations:

* [Relational Databases](relational-databases.md): Postgres, MySQL, Snowflake, Redshift, BigQuery, MariaDB&#x20;
* [AWS S3](aws-s3.md)

# Connecting to Google BigQuery

Connecting Aqueduct to Google BigQuery requires creating a seprate `ServiceAccount` that Aqueduct can use to connect to your BigQuery project.&#x20;

Google Cloud has documentation [here](https://cloud.google.com/docs/authentication/getting-started#creating\_a\_service\_account) that will walk you through creating a `ServiceAccount` and then creating a corresponding key for that `ServiceAccount`. Once you've finished these steps, you should have a JSON file with the key for your `ServiceAccount`.

On the Aqueduct UI, click on the BigQuery logo and enter the following information:

* A unique name for your BigQuery connection.
* The project ID for your BigQuery project, which can be found on the Google Cloud BigQuery UI.
* Upload the JSON key file for your `ServiceAccount`.

You should be good to go! Let us know if you run into any issues with your BigQuery integrations on our community Slack or on [GitHub](https://github.com/aqueducthq/aqueduct/issues/new).

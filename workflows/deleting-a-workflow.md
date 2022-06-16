# Deleting a Workflow

Deleting a workflow can be done from both the UI and SDK. Deleting a workflow will delete the following:

* The code associated with the workflow.
* The snapshots of the intermediary data (see [workflow-versions.md](workflow-versions.md "mention")).&#x20;
* All the metadata associated with the workflow.

#### Aqueduct UI

On the Aqueduct UI, navigate to the settings pane by clicking on the gear icon on the top right. At the bottom of the settings pane, you will see the option to delete the workflow. This will prompt you to confirm the workflow deletion.&#x20;

![](<../.gitbook/assets/image (6) (1).png>)

After workflow deletion has succeeded, you will be redirected to the workflows page.

#### Aqueduct SDK

From the SDK, you can use the `delete_flow` method on the Aqueduct client and pass in the workflow's UUID in order to delete a workflow:

```python
workflow_id = "0c007eff-6ae0-4a1a-a114-5f16164ffcdf" # Set your workflow ID here.
client.delete_flow(workflow_id)
```


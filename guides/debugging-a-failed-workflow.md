# Debugging a Failed Workflow

Despite our best efforts, our prediction pipelines often fail. To help avoid going on wild goose chases, Aqueduct gives you significant insight into understanding when and where workflows failed and resolving things quickly.&#x20;

### Notifications: Knowing a Workflow Failed

Half the battle is often knowing a workflow failed. Aqueduct sends notifications on the success or failure of every workflow execution, which you can see under the notifications tab on the left-hand side of the UI:

&#x20;

![Aqueduct's Notifications pane](<../.gitbook/assets/image (6).png>)

### Workflow Status: Finding Your Errors

The workflow DAG visualization on the Aqueduct UI should give you a status at a glance -- nodes that are green executed successfully, nodes that are yellow had warnings, nodes that are red failed or had errors, and nodes that are grey didn't execute (because an upstream operator failed or errored).&#x20;

In order to find out what exactly went wrong, you can click on a node that failed or you can look at the list of errors on the workflow status bar on the right hand side:

![The Workflow Status Bar shows you errors, warnings, log messages, and successful operators at a glance.](<../.gitbook/assets/image (5).png>)

Once you've clicked on a node, you will see a pane pop up from the bottom that will give you an overview of the operator's execution. You can click on the logs tab on this pane to see the full stack trace for the error that occurred or the test that failed.

### Debugging Your Data

We're working on adding support for retrieving the code and data executed during a particular workflow run, so you can recreate and debug errors locally -- stay tuned!

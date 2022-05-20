# Managing Workflow Schedules

Workflows can be executed on a set schedule or triggered manually.&#x20;

### Schedules

A workflow's schedule can either be set from the Python SDK when calling `publish_flow` (see [creating-a-workflow.md](creating-a-workflow.md "mention")) or from the web UI.&#x20;

#### Aqueduct SDK

The Aqueduct SDK exposes four helper functions `hourly`, `daily`, `weekly`, and `monthly`, which each allow you to create a schedule of the corresponding frequency. The resulting schedule can be passed into the `schedule` argument of the `publish_flow` call.

```python
import hourly, daily, weekly, monthly, DayOfWeek from aqueduct

# Execute at the 10th minute of every hour
hourly_schedule = hourly(minute=10) 

# Execute at 16:00 (or 4:00pm) every day.
daily_schedule = daily(hour=16, minute=0)

# Execute at 08:00 (or 8:00am) every Monday.
weekly_schedule = weekly(day=DayOfWeek.Monday, hour=8, minute=0)

# Execute at 11:30 (or 11:30am) on the 3rd day of every month.
monthly_schedule = monthly(day=3, hour=11, minute=30)
```

#### Aqueduct UI

On the Aqueduct UI, navigate to the page for any given workflow and open the settings view by clicking the gear icon in the top right. Under the schedule section, you can select whether the workflow should update hourly, daily, weekly, or monthly. For each selection, you'll see the corresponding configuration -- for example, if you select weekly, you'll be asked to select the day of the week and the time on that day.&#x20;

![](<../.gitbook/assets/image (1) (1).png>)

Once you've made your selection, you can hit `Save` to finalize your changes.

### Manual Triggers

Workflows can always be manually triggered (both from the UI and SDK), whether or not they have a set schedule.&#x20;

#### Aqueduct SDK

From the SDK, you can use the `trigger` method on the Aqueduct Client and pass in the workflow's UUID to trigger a workflow:

```python
workflow_id = "0c007eff-6ae0-4a1a-a114-5f16164ffcdf" # Set your workflow ID here.
client.trigger(id=workflow_id)
```

The workflow's ID is printed out when the workflow is created and can also be found in the URL For the workflows page. **NOTE**: We will soon be adding the workflow ID to the settings tab of the UI.

#### Aqueduct UI

On the Aqueduct UI, navigate to the page for your workflow. Under the title, you should see the a button titled `Run Workflow`, which will prompt you to confirm a new workflow run.

![](<../.gitbook/assets/image (5).png>)

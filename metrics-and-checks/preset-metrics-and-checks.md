# Preset Metrics & Checks

Many metrics and checks that you might want to compute on your data are common
statistical measures, so Aqueduct provides a set of preset metrics and checks
that you can take off-the-shelf and apply to your data. 

## Preset Metrics

Aqueduct supports the following preset metrics. Each of the preset metrics
listed below is a function that you can apply automatically apply to an
artifact. For example, you can call `my_tbl.number_of_rows()` or
`my_tbl.mean(number_col)`.

* `number_of_missing_values`: Calculates the number of `null` values in a
    particular column.
* `number_of_rows`: Calculates the total number of rows in the table.
* `max`: Calculates the maximum value of a _numerical_ calumn.
* `min`: Calculates the minumum value of a _numerical_ column.
* `mean`: Calculates the mean of a _numerical_ column.
* `std`: Calculates the standard deviation of a _numerical_ column.

## Preset Checks

Once you've created a metric — either one of the preset metrics above or a
custom metric of your own — you can apply a preset check to that metric. We
support the following preset checks:

* `lower`: Set the minimum value for a metric.
* `upper`: Set the maximum value for a metric.
* `equal`: Set a constraint that the metric must be equal to the provided value.
* `not_equal`: Set a constraint that the metric must _not_ be equal to the provided value.

Preset checks can be set on a metric by calling the `bound` function on a
metric. As with all Checks, the severity can either be an `error` or a
`warning`. If a check with `error` severity fails, the whole workflow fails and
no results are published; if a check with `warning` severity fails, a warning
will be raised, but the workflow will continue to execute. For example:

```python
mean_val = my_tbl.mean(number_col)

# The workflow will fail if the mean of `number_col` is 0.
mean_val.bound(not_equal=0, severity='error')

# The workflow will raise an error but finish executing if the mean of
# `number_col` is > 100.
mean_val.bound(upper=100, severity='warning')
```


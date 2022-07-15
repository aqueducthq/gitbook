# System Metrics

In addition to metrics measuring the data that your workflows generate,
Aqueduct also allows you to measure lower-level metrics about the actual
generation of your data, which we call "System Metrics." System metrics are
attached to the result of a function, and they will measure the resource
consumption of the function that was used to generate this data. 

Aqueduct currently supports two system metrics:
* `runtime`: Measures the runtime for the operator that generated this table in seconds.
* `max_memory`: Measures the maximum memmmory that the function which generated this table used in MB.

Here's an example that creates a system metric:

```python
artifact = my_fn(some_data)

# This measures the amount of time `my_fn` took to compute `artifact`.
runtime = artifact.system_metric('runtime')
```


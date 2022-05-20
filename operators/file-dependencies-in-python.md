# File Dependencies in Python

Python functions are not often completely isolated, especially in data science. For example, a prediction function might depend on having access to a serialized model in the local filesystem.&#x20;

Aqueduct allows you to specify this context as a part of your function by passing in a `file_dependencies` argument when annotating your Python function with `@op`:

```python
import scikit
import cloudpickle as cp
import pandas as pd
from aqueduct import op

@op(file_dependencies=['scikit_logistic.pkl'])
def predict(df: pd.DataFrame) -> pd.DataFrame:
    with open('scikit_logistic.pkl') as f:
        model = cp.loads(f)
        
    return model.predict(df)
```

Here, we've written a simple prediction function that takes in a Pandas DataFrame and executes a SciKit-Learn model over that DataFrame. That SciKit-Learn model happens to be stored in a local file called `scikit_logistic.pkl`.

When Aqueduct sees the `file_dependencies` argument in `@op`, it automatically captures the relevant files from the local filesystem and gives your function the appropriates files at execution time -- this way, the execution environment exactly mirrors your development environment.&#x20;

You can specify as many files as you would like in the `file_dependencies` argument.&#x20;

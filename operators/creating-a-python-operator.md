# Creating a Python Operator

Python Operators are simply Python functions annotated with the `@op` decorator, which is provided by the Aqueduct SDK.&#x20;

```python
import pandas as pd
from aqueduct.decorators import op

@op
def my_prediction_function(input: pd.DataFrame) -> pd.DataFrame:
    # Make your predictions here.
    return predictions
```

{% hint style="info" %}
All Python functions in Aqueduct by default take in Pandas DataFrames and return Pandas DataFrames. We plan to add support for other data types in the future.
{% endhint %}

When a Python function is annotated with `@op`, two things happen:

1. Aqueduct packages up this code and infers any library dependencies that it might require, and prepares the code to run as a part of a workflow.&#x20;
2. The annotated function will accept Aqueduct [Artifacts](../artifacts/) as inputs and return Artifacts as outputs -- the return values can in turn by used by other operators.&#x20;

{% hint style="info" %}
Note that once an operator has been annotated with `@op`, it cannot be used as a "regular" Python function -- the inputs to the function must be Artifacts.
{% endhint %}

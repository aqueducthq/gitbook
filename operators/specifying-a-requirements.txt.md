# Specifying a requirements.txt

The simplest way to specify a `requirements.txt` is to create one in the directory where you're running the Aqueduct SDK. When Aqueduct detects that a `requirements.txt` is present, it will automatically use this file to determine the requirements.

### Using Function-Specific Requirements

In some rare cases, individual operators might have different and conflicting requirements. Aqueduct supports specifying requirements on a function-by-function basis. This is simple: in the `@op` decorator, set `reqs_path` to the path to the function-specific `requirements.txt`:

```python
@op(reqs_path="requirements/requirements.txt")
def valid_sentiment_prediction(reviews: pd.DataFrame) -> pd.DataFrame:
    import transformers

    model = transformers.pipeline("sentiment-analysis")
    return reviews.join(pd.DataFrame(model(list(reviews["review"]))))
```

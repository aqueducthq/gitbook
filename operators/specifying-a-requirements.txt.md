# Specifying a requirements.txt

When a function is annotated with `@op`, Aqueduct automatically attempts to infer the Python library requirements for that function by looking at your development environment. We do this by using a tool called [pipreqsnb](https://pypi.org/project/pipreqsnb/).

For a variety of reasons, this might not always be sufficient. If you'd like to specify your own custom `requirements.txt`, Aqueduct will prioritize those requirements over the default inferred requirements.&#x20;

The simplest way to specify a `requirements.txt` is to create one in the directory where you're running the Aqueduct SDK. When Aqueduct detects that a `requirements.txt` is present, it will automatically use this file instead of the inferred requirements.

### Using Function-Specific Requirements

In some rare cases, individual operators might have different and conflicting requirements. We're working on support for specifying requirements on a function-by-function basis, but this isn't quite ready yet -- stay tuned!

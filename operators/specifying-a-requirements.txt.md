# Specifying a requirements.txt

The simplest way to specify a `requirements.txt` is to create one in the directory where you're running the Aqueduct SDK. When Aqueduct detects that a `requirements.txt` is present, it will automatically use this file to determine the requirements.

### Using Function-Specific Requirements

In some rare cases, individual operators might have different and conflicting requirements. We're working on support for specifying requirements on a function-by-function basis, but this isn't quite ready yet -- stay tuned!

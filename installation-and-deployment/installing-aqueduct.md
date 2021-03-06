# Installing Aqueduct

### Prerequisites

Please make sure you have the requirements before installing Aqueduct:

* A Unix-based operating system: Aqueduct supports Debian and CentOS-based Linux distributions and macOS (both Intel and ARM-based Macs)
* Python3: Aqueduct supports Python3.7-Python3.10
* `pip3`
  * Note that older versions of `pip` may or may not be compatible with the latest versions of library dependencies such as `numpy` or `pandas` -- if your installation process fails, please update `pip` by running the following command:

```bash
python3 -m pip install --upgrade pip
```

### Installing Aqueduct

Aqueduct is packaged as a [Python pip package](https://pypi.org/project/aqueduct-ml/) that you can download and install:

```bash
pip3 install aqueduct-ml
```

This will download and install three components for you:

* the Aqueduct CLI -- this is a command line tool that will allow you to interact with the Aqueduct server
* the Aqueduct server -- this is implemented in Golang, and we install a precompiled binary based on your operating system
* the Aqueduct UI -- this is implemented in Typescript
* the Aqueduct SDK -- a Python package that allows you to create Aqueduct workflows

{% hint style="info" %}
By default, the Aqueduct CLI will be installed into the `bin` directory within your Python installation. If you type `which aqueduct` and do not find the package, please locate your Python installation and add `<PATH_TO_PYTHON>/bin` to your `$PATH` variable.

By default, here is where Python packages are installed:

* macOS: `$HOME/Library/Python/3.8/bin`
* Ubuntu: `$HOME/.local/bin`
{% endhint %}

### Installing the SDK

To install only the SDK with `pip`, you can run:

```python
pip3 install aqueduct-sdk
```

This will install only the Aqueduct SDK without the server -- this package is useful if you're running the Aqueduct server on a different machine from your client machine.

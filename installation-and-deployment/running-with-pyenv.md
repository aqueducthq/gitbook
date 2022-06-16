# Running with pyenv

If you use `pyenv` to manage multiple versions of Python, you can run Aqueduct as a part of your `pyenv` version management.&#x20;

* Aqueduct supports Python versions 3.7-3.10, so please use one of those versions of Python when installing Aqueduct. **Aqueduct will not work with Python versions 3.6 or older.**
* Note that because the Aqueduct backend server spins off separate Python processes on a periodic basis, you will need to configure your `pyenv` versions in one of two ways:
  * Option 1: Your global `pyenv` version is a valid Python version for Aqueduct -- in other words `pyenv global` is between Python 3.7 and 3.10.&#x20;
  * Option 2: Set the `pyenv local` version for `~/.aqueduct` to a valid Python version. You can do this by running:

```
cd ~/.aqueduct
pyenv local <VALID PYTHON VERSION>
```

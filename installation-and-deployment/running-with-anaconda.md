# Running with Anaconda

We have done our best to make Aqueduct as cross python version as compatible as possible but sometimes python environments can be "too unique".

Miniconda provides a simple mechanism to manage your Python environment and version together. Here is how to install Miniconda on several platforms.

### Installing Miniconda

{% tabs %}
{% tab title="Linux" %}
```bash
curl https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-Linux-x86_64.sh --output conda_installer.sh
bash conda_installer.sh
```
{% endtab %}

{% tab title="Mac (M1)" %}
```bash
curl https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-MacOSX-arm64.sh --output conda_installer.sh
bash conda_installer.sh
```
{% endtab %}

{% tab title="Mac (Intel)" %}
```bash
curl https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-MacOSX-x86_64.sh --output conda_installer.sh
bash conda_installer.sh
```
{% endtab %}
{% endtabs %}

### Creating a Clean Aqueduct Environment

To create a clean aqueduct environment use the following command:

```bash
conda create -n aqueduct python=3.9
```

You can now activate this environment by running:

```bash
conda activate aqueduct
```

You will need to activate the environment every time you start a new shell.

### Installing Aqueduct

Then, you can can install Aqueduct:

```bash
pip install aqueduct-ml
```

Start the backend server:

```bash
aqueduct server &
```

Start the UI server:

```bash
aqueduct ui &
```

And retrieve your API key:

```bash
aqueduct apikey
```

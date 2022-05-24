# Aqueduct CLI

This page provide a detailed walkthrough of the Aqueduct CLI.&#x20;

* ``[`server`](aqueduct-cli.md#server)``
* ``[`ui`](aqueduct-cli.md#ui)``
* ``[`install`](aqueduct-cli.md#install)``
* ``[`apikey`](aqueduct-cli.md#apikey)``
* ``[`clear`](aqueduct-cli.md#undefined)``

#### server

`aqueduct server` downloads the latest version of the Aqueduct server and starts an instance of the server on your machine.&#x20;

`aqueduct server` is a blocking command, meaning that it will start a the server in a process that takes over your terminal window.

#### ui

`aqueduct ui` downloads the latest version of the Aqueduct UI and start an  instance of the UI server on your machine. The Aqueduct UI is written and Typescript and requires `npm` in order to run.&#x20;

`aqueduct ui` is a blocking command, meaning that it will start a the server in a process that takes over your terminal window.

#### install

`aqueduct install <connector>` installs the dependencies required for `<connector>` on your machine. In most cases, these are `pip` packages on a system-by-system basis, but certain connectors (MySQL & Microsoft SQL Server) require special configuration -- see [configuring-integrations.md](../installation-and-deployment/configuring-integrations.md "mention") for more details.

#### apikey

`aqueduct apikey` reports the API key associated with the server running on this instance.

#### clear

`aqueduct clear` removes the local Aqueduct installation by deleting the server, UI code, and any associated metadata.

# Aqueduct CLI

This page provide a detailed walkthrough of the Aqueduct CLI.&#x20;

* ``[`start`](aqueduct-cli.md#undefined)``
* ``[`install`](aqueduct-cli.md#install)``
* ``[`apikey`](aqueduct-cli.md#apikey)``
* ``[`clear`](aqueduct-cli.md#undefined)``
* \[DEPRECATED] [`server`](aqueduct-cli.md#server)
* \[DEPRECATED] [`ui`](aqueduct-cli.md#ui-deprecated)``

#### start

`aqueduct start` starts the Aqueduct server, which includes both the Aqueduct API layer as well as the Aqueduct UI. If this is your first time starting Aqueduct or if you've recently upgraded to a newer version of Aqueduct, we will download the latest versions of the backend and UI code before starting the server.

`aqueduct start` is a blocking command, meaning that it will start the server in a process that takes over your terminal window.

#### server \[DEPRECATED]

`aqueduct server` downloads the latest version of the Aqueduct server and starts an instance of the server on your machine.&#x20;

`aqueduct server` is a blocking command, meaning that it will start the server in a process that takes over your terminal window.

#### ui \[DEPRECATED]&#x20;

`aqueduct ui` downloads the latest version of the Aqueduct UI and start an  instance of the UI server on your machine. The Aqueduct UI is written and Typescript and requires `npm` in order to run.&#x20;

`aqueduct ui` is a blocking command, meaning that it will start a the server in a process that takes over your terminal window.

#### install

`aqueduct install <connector>` installs the dependencies required for `<connector>` on your machine. In most cases, these are `pip` packages on a system-by-system basis, but certain connectors (MySQL & Microsoft SQL Server) require special configuration -- see [configuring-integrations.md](../installation-and-deployment/configuring-integrations.md "mention") for more details.

#### apikey

`aqueduct apikey` reports the API key associated with the server running on this instance.

#### clear

`aqueduct clear` removes the local Aqueduct installation by deleting the server, UI code, and any associated metadata.

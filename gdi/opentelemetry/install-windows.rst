.. _otel-install-windows:

****************************************************************
Install the Collector for Windows with the installer script
****************************************************************

.. meta::
      :description: Describes how to install the Splunk Distribution of OpenTelemetry Collector for Windows.

.. toctree::
  :maxdepth: 4
  :titlesonly:

  /gdi/opentelemetry/deployments/deployments-windows-ansible.rst
  /gdi/opentelemetry/deployments/deployments-windows-puppet.rst

The Splunk Distribution of OpenTelemetry Collector for Windows is a package that provides integrated collection and
forwarding for all data types. Install the package using one of these methods:

* :ref:`Installer script <windows-script>`
* :ref:`Deployments <windows-deployments>`

Alternatively, you can manually install the Collector. To learn how, see :ref:`otel-install-windows-manual`.

.. _windows-otel-requirements:

Prerequisites
==========================

The Splunk Distribution of OpenTelemetry Collector for Windows has the following requirements
depending on the installation method:

.. list-table::
  :header-rows: 1
  :widths: 40 60
  :width: 100

  * - Install method
    - Supported versions (64-bit)
  * - Installer script
    - Windows 2012, 2016, 2019, 2022
  * - Windows installer (MSI)
    - Windows 2012, 2016, 2019, 2022
  * - Ansible
    - Windows 2012, 2016, 2019, 2022
  * - Chef
    - Windows 2019, 2022
  * - Nomad
    - Windows 2012, 2016, 2019
  * - Puppet
    - Windows 2012, 2016, 2019
  * - Docker
    - Windows 2019, 2022

.. _windows-script:

Installer script
==========================

The installer script is available for Windows 64-bit environments, and deploys and configures the Splunk Distribution of OpenTelemetry Collector for Windows and Fluentd (using the td-agent).

To install the package using the installer script, follow these steps:

#. Ensure that you have Administrator access on your host.
#. Run the following PowerShell command on your host, replacing the following variables for your environment:

   * ``SPLUNK_REALM``: This is the realm to send data to. The default is ``us0``. See :new-page:`realms <https://dev.splunk.com/observability/docs/realms_in_endpoints/>`.
   * ``SPLUNK_ACCESS_TOKEN``: This is the base64-encoded access token for authenticating data ingest requests. See :ref:`admin-org-tokens`.

.. code-block:: PowerShell

  & {Set-ExecutionPolicy Bypass -Scope Process -Force; $script = ((New-Object System.Net.WebClient).DownloadString('https://dl.signalfx.com/splunk-otel-collector.ps1')); $params = @{access_token = "SPLUNK_ACCESS_TOKEN"; realm = "SPLUNK_REALM"}; Invoke-Command -ScriptBlock ([scriptblock]::Create(". {$script} $(&{$args} @params)"))}

.. note:: If needed, activate TLS in PowerShell using the following command: 
  
   ``[Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12``

Configure memory allocation
----------------------------------

To configure memory allocation, use the ``memory`` parameter. The default value for this parameter is 512 MiB, or
500 x 2^20 bytes, of memory. Increase this setting to allocate more memory, as shown in the following example.

.. code-block:: PowerShell

  & {Set-ExecutionPolicy Bypass -Scope Process -Force; $script = ((New-Object System.Net.WebClient).DownloadString('https://dl.signalfx.com/splunk-otel-collector.ps1')); $params = @{access_token = "SPLUNK_ACCESS_TOKEN"; realm = "SPLUNK_REALM"; memory = "SPLUNK_MEMORY_TOTAL_MIB"}; Invoke-Command -ScriptBlock ([scriptblock]::Create(". {$script} $(&{$args} @params)"))}

Replace ``SPLUNK_MEMORY_TOTAL_MIB`` with the desired integer value.

Configure proxy settings
----------------------------------

If you need to use a proxy, set one of the following environment variables according to your needs:

- ``HTTP_PROXY``: Address of the proxy for HTTP request. Port is optional.
- ``HTTPS_PROXY``: Address of the proxy for HTTPS request. Port is optional.
- ``NO_PROXY``: If you use a proxy, sets addresses that don't use the proxy.

Restart the Collector after adding these environment variables to your configuration.

.. note:: For more information on proxy settings, see :ref:`configure-proxy-collector`.

.. _fluentd-manual-config-windows:

Configure fluentd for log collection
-------------------------------------------

By default, the installation configures the fluentd service to forward log events with the ``@SPLUNK`` label and
send these events to the HEC ingest endpoint determined by the ``--realm <SPLUNK_REALM>`` option.
For example, ``https://ingest.<SPLUNK_REALM>.signalfx.com/v1/log``.

To configure the package to send log events to a custom HTTP Event Collector (HEC) endpoint URL, you can specify the
following parameters for the installer script:

* ``hec-url = "<URL>"``
* ``hec-token = "<TOKEN>"``

The installation creates the main fluentd configuration file  ``<drive>\opt\td-agent\etc\td-agent\td-agent.conf``.
``<drive>`` is the drive letter for the fluentd installation directory.
You can add custom fluentd source configuration files to the ``<drive>\opt\td-agent\etc\td-agent\conf.d``
directory after installation.

Note the following:

* In this directory, fluentd includes all files with the .conf extension.
* By default, fluentd collects from the Windows Event Log. See ``<drive>\opt\td-agent\etc\td-agent\conf.d\eventlog.conf``
  for the default configuration.

After any configuration modification, apply the changes by restarting the system or running the following PowerShell commands:

.. code-block:: PowerShell

  Stop-Service fluentdwinsvc
  Start-Service fluentdwinsvc

Start the Collector executable manually 
-------------------------------------------

If you experience unexpected start failures, try to start the Collector executable manually. 

To do so, run the following PowerShell command as an Admin:  

.. code-block:: PowerShell

  & 'C:\Program Files\Splunk\OpenTelemetry Collector\otelcol.exe' --config 'C:\ProgramData\Splunk\OpenTelemetry Collector\agent_config.yaml'

.. _windows-deployments:

Deployments
===============================
Splunk offers the configuration management options described in this section.

.. _windows-ansible:

Ansible
--------------------------
Splunk provides an Ansible role that installs the package configured to collect data (metrics, traces, and logs) from Windows machines and send that data to Observability Cloud. See :ref:`deployment-windows-ansible` for the instructions to download and customize the role.

.. _windows-chef:

Chef 
----------------
Splunk provides a cookbook to install the Collector using Chef. See :ref:`deployments-chef` for the installation instructions.

.. _windows-nomad:

Nomad 
-----------------
Use Nomad to deploy the Collector. To learn how to install Nomad, see :ref:`deployments-nomad`.

.. _windows-puppet:

Puppet
-------------------------------
Splunk provides a Puppet module to install and configure the package. A module is a collection of resources, classes, files, definition, and templates. To learn how to download and customize the module, see :ref:`deployment-windows-puppet`.

Next steps
==================================

Once you have installed the package, you can perform these actions:

* :ref:`use-navigators-imm`.
* View logs and errors in the Windows Event Viewer. Search for "view logs and errors" on :new-page:`Microsoft documentation site <https://docs.microsoft.com/en-us/>` for more information.
* :ref:`apm`.

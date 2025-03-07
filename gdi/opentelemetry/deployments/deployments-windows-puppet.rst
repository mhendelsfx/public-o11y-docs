.. _deployment-windows-puppet:

**********************
Puppet for Windows
**********************

.. meta::
      :description: Describes how to install the Splunk Observability Cloud OpenTelemetry Collector Puppet module on Windows.

Use this module to install and configure the Collector on Windows. Download and install the module from :new-page:`Puppet Forge <https://forge.puppet.com/modules/signalfx/splunk_otel_collector>`. After downloading the module, you can add customizations using the ``class`` parameter.

On Windows systems, the :new-page:`puppetlabs/registry module <https://forge.puppet.com/modules/puppetlabs/registry>` is required to set the registry key/values, and the :new-page:`puppetlabs/powershell module <https://forge.puppet.com/modules/puppetlabs/powershell>` is required to run Powershell commands.

Using the module
============================

To use this module, include the ``splunk_otel_collector`` class in your manifests with the supported parameters (see :ref:`modify-class-parameters-windows` for descriptions of the available parameters). For example, the following deployment definition is the simplest deployment definition with the default parameters (replace ``VERSION`` with the desired Collector version, ``SPLUNK_ACCESS_TOKEN`` with your Splunk access token to authenticate requests, and ``SPLUNK_REALM`` for the realm to send the data to):

.. code-block:: ruby

   class { splunk_otel_collector:
     collector_version => 'VERSION'
     splunk_access_token => 'SPLUNK_ACCESS_TOKEN',
     splunk_realm => 'SPLUNK_REALM',
   }

.. _modify-class-parameters-windows:

Modify the class parameters
=======================================

The class accepts the parameters described in the following table:

.. list-table:: 
   :widths: 25 45 30
   :header-rows: 1

   * - Name
     - Description
     - Default value
   * - ``collector_version``
     - Version of the Collector package to install, for example, ``0.25.0``. The version must correspond to :new-page:`Github Releases <https://github.com/signalfx/splunk-otel-collector/releases>` without the preceding ``v``. This attribute is required.
     - None
   * - ``splunk_access_token``
     - The Splunk access token to authenticate requests. This attribute is required.
     - None
   * - ``splunk_realm``
     - Which realm to send the data to, for example, ``us0``. The Splunk ingest and API URLs are inferred by this value. The ``SPLUNK_REALM`` environment variable is set with this value for the collector service. This attribute is required.
     - None
   * - ``splunk_ingest_url``
     - Sets the Splunk ingest URL explicitly instead of the URL inferred by the ``$splunk_realm`` parameter. The ``SPLUNK_INGEST_URL`` environment variable is set with this value for the Collector service.
     - ``https://ingest.${splunk_realm}.signalfx.com``
   * - ``splunk_api_url``
     - Sets the Splunk API URL explicitly instead of the URL inferred by the ``$splunk_realm`` parameter. The ``SPLUNK_API_URL`` environment variable is set with this value for the Collector service.
     - ``https://api.${splunk_realm}.signalfx.com``
   * - ``splunk_trace_url``
     - Sets the Splunk trace endpoint URL explicitly instead of the URL inferred by the ``$splunk_ingest_url`` parameter. The ``SPLUNK_TRACE_URL`` environment variable is set with this value for the Collector service.
     - ``${splunk_ingest_url}/v2/trace``
   * - ``splunk_hec_url``
     - Sets the Splunk HEC endpoint URL explicitly instead of the URL inferred by the ``$splunk_ingest_url`` parameter. The ``SPLUNK_HEC_URL`` environment variable is set with this value for the Collector service.
     - ``${splunk_ingest_url}/v1/log``
   * - ``splunk_hec_token``
     - Sets the Splunk HEC authentication token if different than ``$splunk_access_token``. The ``SPLUNK_HEC_TOKEN`` environment variable is set with this value for the Collector service.    
     - ``$splunk_access_token``
   * - ``splunk_bundle_dir``
     - The path to the Smart Agent bundle directory. The default path is provided by the Collector package. If the specified path is changed from the default value, the path should be an existing directory on the node. The ``SPLUNK_BUNDLE_DIR`` environment variable is set to this value for the Collector service. 
     - ``%PROGRAMFILES%\Splunk\OpenTelemetry Collector\agent-bundle``
   * - ``splunk_collectd_dir``
     - The path to the collectd configuration directory for the Smart Agent bundle. The default path is provided by the Collector package. If the specified path is changed from the default value, the path should be an existing directory on the node. The ``SPLUNK_COLLECTD_DIR`` environment variable is set to this value for the Collector service. 
     - ``${splunk_bundle_dir}\run\collectd``
   * - ``splunk_memory_total_mib``
     - Total memory in MIB to allocate to the Collector; automatically calculates the ballast size. The ``SPLUNK_MEMORY_TOTAL_MIB`` environment variable is set to this value for the Collector service. 
     - ``512``
   * - ``splunk_ballast_size_mib``
     - Sets the ballast size for the Collector explicitly instead of the value calculated from the ``$splunk_memory_total_mib`` parameter. This should be set to 1/3 to 1/2 of the configured memory. The ``SPLUNK_BALLAST_SIZE_MIB`` environment variable is set to this value for the Collector service. 
     - None
   * - ``collector_config_source``
     - The source path to the Collector configuration YAML file. This file is copied to the ``$collector_config_dest`` path on the node. See the :new-page:`source attribute <https://puppet.com/docs/puppet/latest/types/file.html#file-attribute-source>` of the file resource for the supported value types. The default source file is provided by the Collector package.
     - ``%PROGRAMFILES\Splunk\OpenTelemetry Collector\agent_config.yaml``
   * - ``collector_config_dest``
     - Destination path of the Collector configuration file on the node. The ``SPLUNK_CONFIG`` environment variable is set with this value for the Collector service.
     - ``%PROGRAMDATA%\Splunk\OpenTelemetry Collector\agent_config.yaml``
   * - ``with_fluentd``
     - Whether to install or manage Fluentd and dependencies for log collection.
     - ``true``
   * - ``fluentd_config_source``
     - Source path to the Fluentd configuration file. This file is copied to the ``$fluentd_config_dest`` path on the node. See the :new-page:`source attribute <https://puppet.com/docs/puppet/latest/types/file.html#file-attribute-source>` of the file resource for the supported value types. The default source file is provided by the Collector package. Only applicable if ``$with_fluentd`` is set to ``true``.
     - ``%PROGRAMFILES\Splunk\OpenTelemetry Collector\fluentd\td-agent.conf``

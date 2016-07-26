=========
Userguide
=========

************
Key concepts
************

The Nmon Performance application implements the nmon/sarmon binaries to generates rich and accurate performance data for your AIX, Linux and Solaris systems.

**Keys concepts of the app can be summarize as the following:**

* The nmon core application is deployed to the Splunk search head level
* The TA-nmon package available in the resources directory of the core app is deployed to *nix clients running the Splunk Universal Forwarder or full Splunk instance
* On search head / standalone instances, the core app can generate the nmon data without having to deploy the TA-nmon
* When the nmon_helper input script starts, it attempts find the best suitable binary for your system or can fallback to locally nmon binary available
* Once the nmon binary process has been started, the data collection begins until the current process ends. (each nmon process has a time to live)
* Every time the nmon data gets updated, Splunk read the nmon files and calls the nmon2csv parsers, the data gets structured and indexed in Splunk
* Performance metrics once indexed are immediately available in Splunk for analysis
* Every time a new nmon process is started, new configuration data will be generated and indexed in Splunk
* The nmon_inventory data (stored in the nmon_inventory lookup table) is generated every hour using efficient data model, it is being used to enrich the performance data and provide inventory interfaces

***********************
Data Types (sourcetype)
***********************

--------------------
sourcetype=nmon_data
--------------------

The "**nmon_data**" sourcetype available in the **eventtype=nmon:performance** contains all the data related to performance metrics of your systems.

In the nmon:performance data, the "key" is the **type** field.

This field contains the monitor identifier that matches a category of metrics, such as "type=CPU_ALL". (global CPU usage in percentage)

----------------------
sourcetype=nmon_config
----------------------

The "**nmon_config**" sourcetype available in the **eventtype=nmon:config** contains all the data related to the configuration of your systems.

These are the AAA and BBB* sections of nmon raw data, generated during the nmon binary startup.
The events are long multi-lines events stored per host, in default configuration these data will be extracted almost every 2 hours as the data will not change unless a new nmon process gets launched.

The nmon:config data is associated with the generation of the nmon_inventory lookup and the Nmon_Config data model.

-----------------------
sourcetype=nmon_collect
-----------------------

The "**nmon_collect**" sourcetype available in the **eventtype=nmon:collect** contains all the data related to the nmon processes generation on your systems.

These are the ouput of the input script "nmon_helper.sh" thats gets automatically launched by Splunk when deploying the Nmon App.
By default, the nmon_helper.sh script gets started every minute, its main job is to verify the status of the current nmon process, and start a new one if conditions requires it.

Many nmon starting options can be controlled through the "nmon.conf" configuration file during the deployment of the App.

--------------------------
sourcetype=nmon_processing
--------------------------

The "**nmon_processing**" sourceytpe available in the **eventtype=nmon:collect** contains all the data related to the nmon processing steps that converts nmon data into usable data for Splunk.

These are the ouput of nmon2csv Python and Perl parsers provided within the App.
Every time an existing raw nmon file is updated, or a new one gets created, Splunk will call parsers scripts and generate appropriated data.

---------------------
sourcetype=nmon_clean
---------------------

The "**nmon_clean**" sourceytpe available in the **eventtype=nmon:clean** contains all the data related to various cleaning steps operated by nmon_cleaner scripts.

These scripts are responsible in cleaning raw nmon data file periodically, and also cleaning csv raw data in case of an unexpected Splunk failure to prevent from filling the file system with unconsumed csv data.

********************
Lookups and KV Store
********************

--------------
nmon_inventory
--------------

**Nmon Inventory (nmon_inventory): Inventory Extraction of NMON data**

The Nmon Inventory data is an important piece of the Application, it is being used to provide useful inventory information about your servers with main configuration items. (CPU and memory configuration, uptime...)

The nmon_inventory data is build over the nmon_config sourcetype which contains the extraction of AAA and BBB* sections of Nmon:

.. image:: img/nmon_inventory1.png
   :alt: nmon_inventory1.png
   :align: center

To build with efficiency the nmon_inventory data, the Application uses the accelerated data model "NMON Config - Inventory Items extracted from Nmon raw data" and intensive regular expressions:

.. image:: img/nmon_inventory2.png
   :alt: nmon_inventory2.png
   :align: center

.. image:: img/nmon_inventory3.png
   :alt: nmon_inventory3.png
   :align: center

.. image:: img/nmon_inventory4.png
   :alt: nmon_inventory4.png
   :align: center

.. image:: img/nmon_inventory5.png
   :alt: nmon_inventory5.png
   :align: center

.. image:: img/nmon_inventory6.png
   :alt: nmon_inventory6.png
   :align: center

.. image:: img/nmon_inventory7.png
   :alt: nmon_inventory7.png
   :align: center

.. image:: img/nmon_inventory8.png
   :alt: nmon_inventory8.png
   :align: center







************************
Main Configuration Files
************************

*********
Configure
*********

************
Troubleshoot
************

*******
Upgrade
*******



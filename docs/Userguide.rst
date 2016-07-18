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

* **sourcetype=nmon_data**

The "nmon_data" sourcetype available in the eventtype=nmon:performance contains all the data related to performance metrics of your systems.

In the nmon:performance data, the "key" is the **type** field.

This field contains the monitor identifier that matches a category of metrics, such as "type=CPU_ALL". (global CPU usage in percentage)





********************
Lookups and KV Store
********************

************************
Main Configuration Files
************************

************
Configure
************

************
Troubleshoot
************

*******
Upgrade
*******



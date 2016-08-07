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

*Since the major release V1.7, the nmon inventory data is stored in a KVstore collection*

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


---------------
data_dictionary
---------------

**data-dictionary lookup : Dictionary of Nmon Application data**

The "data-dictionary" lookup is a csv file provided by the Application, it contains the definition of every piece of data available within the Application.

It is being used in the "Data Dictionary" interface to provide a extensible view of metrics and data available in the context of the application, with a hierarchy by type of operating system:

.. image:: img/data_dic1.png
   :alt: data_dic1.png
   :align: center

.. image:: img/data_dic2.png
   :alt: data_dic2.png
   :align: center

.. image:: img/data_dic3.png
   :alt: data_dic3.png
   :align: center



-------------
nmon_baseline
-------------

**Nmon Baseline (nmon_baseline): Key system metrics from the Nmon KV Store Baseline**

The Nmon KV Store baseline is a feature that provides an advanced analysis of historical past data charted versus real time data to help detecting unexpected or unusual system resources usage.

**The key concept is quite simple:**

Every week (scheduled each Sunday starting at midnight by default), scheduled reports will generate data for different metrics and store the result in kvstore collections:

* CPU (CPU_ALL, LPAR)
* Real and Virtual Memory (MEM)
* Disks I/O per second (DISKXFER)

These reports will generate statistics per day of the week and per 5 minutes step of 3 statistics results for each metric per server: lower (perc05), Average and upper (perc95)

At the end, results are being stored in different kvstore Collections on search heads. (2016 records per server and per kvstore)

Specific macros called within the Baseline interface will retrieve current (or custom if you select your own time range) statistics for these metrics and selected host
The macro will evaluate statistics per day of the week and per minute (data is being retrieved from indexers using data model acceleration)

The lookup command being called within the macro will retrieve stored values within the KV Store for associated days of week and minute to generate the metric baseline (eg. compare Mondays over Mondays, Tuesdays over Tuesdays…)
This operation will fully occurs on search head within generating unnecessary loads for indexers

Finally, if the selected time range runs over the future (default of baseline interface starts at beginning of the day and finishes at the end of the current day), the baseline will be charted over the future in 2 available mode: Full Baseline using the predict rendering with lower, average and upper, of the Simple baseline which will only generate the Average baseline serie

**List of kvtore Collections:**

*Here are kvstore Collections and corresponding lookup table references:*

+-------------------------------+-----------------------------+----------------------------------------------------------+
| kvstore collection            |   lookup name               |     baseline generation report name                      |
+===============================+=============================+==========================================================+
| kv_nmon_baseline_CPU_ALL      |   nmon_baseline_CPU_ALL     |     Generate NMON Baseline KV Collection for CPU_ALL     |
+-------------------------------+-----------------------------+----------------------------------------------------------+
| kv_nmon_baseline_LPAR         |   nmon_baseline_LPAR        |     Generate NMON Baseline KV Collection for LPAR        |
+-------------------------------+-----------------------------+----------------------------------------------------------+
| kv_nmon_baseline_MEM	        |   nmon_baseline_MEM	      |     Generate NMON Baseline KV Collection for MEM         |
+-------------------------------+-----------------------------+----------------------------------------------------------+
| kv_nmon_baseline_DISKXFER     |   nmon_baseline_DISKXFER    |     Generate NMON Baseline KV Collection for DISKXFER    |
+-------------------------------+-----------------------------+----------------------------------------------------------+

*Note that only the LPAR kvstore and related report are specific for Power systems, if you are not using such systems, these objects can be safety deactivated.*

**Here are some examples of the baseline charting:**

.. image:: img/baseline1.png
   :alt: baseline1.png
   :align: center

.. image:: img/baseline2.png
   :alt: baseline2.png
   :align: center


--------------------
filesystem_excluding
--------------------

The lookup table "filesystem_excluding" is file lookup that will contain mount point of file systems to be excluded from file system alerting.

The alert "NMON - File System % usage exceeds 90% (5 consecutive minutes minimal duration)" will exclude any mount point listed in this lookup table from its analysis.
Note that this lookup table is case insensitive, can contain wildcards of pattern to be excluded (such as *cdrom*).

**upgrade resiliency caution:**

If you customize this lookup table, you will need to back it up before upgrading, and recover it from your backup after the update.
This feature will probably be updated and improved in future releases!

************************
Main Configuration Files
************************

-----------
inputs.conf
-----------

Since the major release V1.7, all data generation pieces were migrated to the TA-nmon and PA-nmon add-on, these information are only valid for these add-on and the core application does not implement any input anymore

Here is the default configuration for inputs.conf file provided by the TA-nmon and PA-nmon technical add-ons.

Since the Application root directory will vary depending on the fact your running the Application on an indexer (PA-nmon) or Heavy / Universal Forwarder (TA-nmon), the normal root directory is being replace with the pattern "<root_directory>" in this configuration documentation.

See the configuration file provided within the Application to see original configuration.

Since the major release V1.7, and following Splunk requirements for certification criteria, the main working directory were migrated from $SPLUNK_HOME/var/run to $SPLUNK_HOME/var/log

+++++++++++++++++++++++++++
01 - Nmon raw data monitor:
+++++++++++++++++++++++++++

::

    [monitor://$SPLUNK_HOME/var/log/nmon/var/nmon_repository/*.nmon]

    disabled = false
    followTail = 0
    recursive = false
    index = nmon
    sourcetype = nmon_processing
    crcSalt = <SOURCE>

This input stanza configuration is responsible in managing nmon raw data files produced by nmon binaries, themselves launched by the nmon_helper.sh script.

Because a large part of the nmon raw data file won't change until a new file is being generated, the "crcSalt" option is required.

The input is associated with the "nmon_processing" configuration in props.conf.
When a new nmon file, or a current nmon file is updated, Splunk will output the content of the file to the converter script defined in props.conf.

++++++++++++++++++++++++++++++++++++++++
02 - Nmon Performance csv data indexing:
++++++++++++++++++++++++++++++++++++++++

::

    [batch://$SPLUNK_HOME/var/log/nmon/var/csv_repository/*nmon*.csv]

    disabled = false
    move_policy = sinkhole
    recursive = false
    crcSalt = <SOURCE>
    index = nmon
    sourcetype = nmon_data
    # source override: to prevent Metadata from having millions of entry, the source is overriden by default
    # You can disable this for trouble shooting purposes if required
    source = perfdata

This input monitor will index within Splunk csv data produced by nmon2csv Python or Perl converter.

Note that csv files are being indexed in "batch" mode, which means indexing and deleting files.

Under common circumstances (unless you are managing large number of nmon file for example with a NFS central nmon repositories), there shall never be any file here as they are indexed and deleted once generated.

++++++++++++++++++++++++++++++++++++++++++
03 - Nmon Configuration csv data indexing:
++++++++++++++++++++++++++++++++++++++++++

::

    [batch://$SPLUNK_HOME/var/log/nmon/var/config_repository/*nmon*.csv]

    disabled = false
    move_policy = sinkhole
    recursive = false
    crcSalt = <SOURCE>
    index = nmon
    sourcetype = nmon_config
    # source override: to prevent Metadata from having millions of entry, the source is overriden by default
    # You can disable this for trouble shooting purposes if required
    source = configdata

This input monitor will index within Splunk csv configuration data produced by nmon2csv Python or Perl converter.

This concerns the AAA and BBB* sections of nmon that contains configuration produced by the nmon binary.

Note that csv files are being indexed in "batch" mode, which means indexing and deleting files.

Nmon for Splunk will use these data to identify important information such as the type of Operating System (used to provide Operating System selector) and configuration data inventory. (see the lookup table nmon_inventory)

+++++++++++++++++++++++++++++++++
04 - nmon_helper.sh input script:
+++++++++++++++++++++++++++++++++

::

    [script://./bin/nmon_cleaner.sh --cleancsv]
    disabled = false
    index = nmon
    interval = 600
    source = nmon_cleaner
    sourcetype = nmon_clean

This input is responsible for launching nmon instances when required, it is associated with the nmon_helper.sh input script.

In default configuration, it is launched every minute, its activity is indexed within Splunk in the nmon_collect sourcetype.

---------
nmon.conf
---------

**Customizing Nmon related parameters**

Technical add-ons will allow you to configure various settings of parameters used at the nmon binary startup by using a local definition of "nmon.conf" file.

The "nmon.conf" file is located in the "default" directory of nmon / TA-nmon / PA-nmon Apps, it works in a "Splunk" fashion, to modify these settings, copy the default/non.conf to local/nmon.conf. (upgrade resilient)
This configuration file is sourced at starting time by the "nmon_helper.sh" input script which apply these settings.

**To set custom properties, create a local/nmon.conf file and set parameters:**

+++++++++++++++++++
INTERVAL & SNAPSHOT
+++++++++++++++++++

**A number of preset configurations are provided for proposal:**

::

    shortperiod_low

    interval="60"
    snapshot="10"

    shortperiod_middle

    interval="30"
    snapshot="20"

    shortperiod_high

    interval="20"
    snapshot="30"

    longperiod_low

    interval="240"
    snapshot="120"

    longperiod_middle

    interval="120"
    snapshot="120"

    longperiod_high

    interval="60"
    snapshot="120"

    The default mode is set to "longperiod_high", which is a good compromise between accuracy, CPU / licensing cost and operational intelligence, and should be relevant for very large deployment in Production environments

    # custom --> Set a custom interval and snapshot value, if unset short default values will be used (see custom_interval and custom_snapshot)

    # Default is longperiod_low
    mode="longperiod_low"
    A custom mode combining custom values for snapshot and interval can be set:

    mode="custom"

    # Refresh interval in seconds, Nmon will this value to refresh data each X seconds
    # UNUSED IF NOT SET TO custom MODE
    custom_interval="20"

    # Number of Data refresh occurrences, Nmon will refresh data X times
    # UNUSED IF NOT SET TO custom MODE
    custom_snapshot="90"
    Note that the CPU usage associated with Nmon and Splunk processing steps, the data volume to be generated and the licensing cost are a combination of these factors







++++++++++++++
NFS Statistics
++++++++++++++

**NFS options for AIX and Linux: Activate NFS statistics:**

NFS statistics generation is deactivated by default for AIX and Linux (NFS statistics are not applicable for Solaris)

To activate NFS statistics generation, you must activate this in a local/nmon.conf, as shown bellow:

::

    ### NFS OPTIONS ###

    # Change to "1" to activate NFS V2 / V3 (option -N) for AIX hosts
    AIX_NFS23="0"

    # Change to "1" to activate NFS V4 (option -NN) for AIX hosts
    AIX_NFS4="0"

    # Change to "1" to activate NFS V2 / V3 / V4 (option -N) for Linux hosts
    # Note: Some versions of Nmon introduced a bug that makes Nmon to core when activating NFS, ensure your version is not outdated
    Linux_NFS="0"

++++++++++++++++++++++++++++++++++++
End time marging (Nmon parallel run)
++++++++++++++++++++++++++++++++++++

Nmon processes generated by technical add-ons have specific time of live which is the computation of INTERVAL * SNAPSHOT.

Between two run of nmon collections, there can be several minutes required by nmon to collect configuration items before starting collecting performance metrics, moreover on very large systems.

For this reason, a parallel run of two nmon concurrent processes will occur a few minutes before the current process ends, which prevents from having gaps in charts and data.

This feature can be controlled by changing the value of the endtime_margin, and can also be totally deactivated if you like:

::

    ### VARIOUS COMMON OPTIONS ###

    # Time in seconds of margin before running a new iteration of Nmon process to prevent data gaps between 2 iterations of Nmon
    # the nmon_helper.sh script will spawn a new Nmon process when the age in seconds of the current process gets higher than this value

    # The endtime is evaluated the following way:
    # endtime=$(( ${interval} * ${snapshot} - ${endtime_margin} ))

    # When the endtime gets higher than the endtime_margin, a new Nmon process will be spawned
    # default value to 240 seconds which will start a new process 4 minutes before the current process ends

    # Setting this value to "0" will totally disable this feature

    endtime_margin="240"


+++++++++++++++++++++++++
Linux OS specific options
+++++++++++++++++++++++++

**Embedded nmon binaries versus locally available nmon binaries**

In default configuration, the "nmon_helper.sh" script will always give the priority to embedded nmon binary.

The Application has embedded binaries specifically compiled for almost every Linux OS and versions, such that you can manage from a center place nmon versions for all your Linux hosts!

The nmon_helper.sh script will proceed as above:

* Search for an embedded binary that suits processor architecture, Linux OS version (example: RHEL), that suite vendor version (example: RHEL 7) and vendor subversion (RHEL 7.1)
  Best result will be achieved using /etc/os-release file, if not available specific information file will be searched (example: /etc/issue, /etc/redhat-release, etc…)
* In the worst case (no binary found for vendor OS (example: Linux RHEL), the nmon_helper.sh search for generic binary that fits the local processor architecture
* If none of these options are possible, the script will search for nmon binary in PATH
* If this fails, the script exists in error, this information will stored in Splunk and shown in home page error counter

::

    ### LINUX OPTIONS ###

    # Change the priority applied while looking at nmon binary
    # by default, the nmon_helper.sh script will use any nmon binary found in PATH
    # Set to "1" to give the priority to embedded nmon binaries
    # Note: Since release 1.6.07, priority is given by default to embedded binaries
    Linux_embedded_nmon_priority="1"

**Unlimited capture**

Recently introduced, you can set nmon linux to run its mode of capture in unlimited mode, specially for the TOP section (processes) and block devices.

*CAUTION: This option is experimental and cause increasing volume of data to be generated*

::

    # Change the limit for processes and disks capture of nmon for Linux
    # In default configuration, nmon will capture most of the process table by capturing main consuming processes
    # You can set nmon to an unlimited number of processes to be captured, and the entire process table will be captured.
    # Note this will affect the number of disk devices captured by setting it to an unlimited number.
    # This will also increase the volume of data to be generated and may require more cpu overhead to process nmon data
    # The default configuration uses the default mode (limited capture), you can set bellow the limit number of capture to unlimited mode
    # Change to "1" to set capture of processes and disks to no limit mode
    Linux_unlimited_capture="0"

**Maximum number of disk devices**

The maximum number of disk devices to be taken in charge by nmon for Linux has to be set at starting time.

*Note that currently, nmon2csv parsers have a hard limit at 3000 devices*

::

    # Set the maximum number of devices collected by Nmon, default is set to 1500 devices
    # Increase this value if you have systems with more devices
    # Up to 3000 devices will be taken in charge by the Application (hard limit in nmon2csv.py / nmon2csv.pl)
    Linux_devices="1500"

+++++++++++++++++++++++++++
Solaris OS specific options
+++++++++++++++++++++++++++

**Using a local/nmon.conf file, you can for Solaris activate the generation of statistics for VxVM volumes:**


::

    ### SOLARIS OPTIONS ###

    # CHange to "1" to activate VxVM volumes IO statistics
    Solaris_VxVM="0"

**You can manage the activation / deactivation of UARG generation: (full commands arguments)**

::

    # UARG collection (new in Version 1.11), Change to "0" to deactivate, "1" to activate (default is activate)
    Solaris_UARG="1"

+++++++++++++++++++++++
AIX OS specific options
+++++++++++++++++++++++

**For AIX hosts, you can customize the full command line sent to nmon at launch time, at the exception of NFS options. (see previous section)**

::

    # Change this line if you add or remove common options for AIX, do not change NFS options here (see NFS options)
    # the -p option is mandatory as it is used at launch time to save instance pid
    AIX_options="-f -T -A -d -K -L -M -P -^ -p"


----------
props.conf
----------

**props.conf - nmon sourcetypes definition**

+++++++++++++++++++++++++++++++++++++++++++++++++++++++
01 - Nmon raw data source stanza to nmon2csv converters
+++++++++++++++++++++++++++++++++++++++++++++++++++++++

**These stanza are linked with the nmon_processing sourcetype wich contains the output of Nmon raw data conversion operated by nmon2csv converters.**

*TA-nmon:*

::

    [source::.../*.nmon]
    invalid_cause = archive
    unarchive_cmd = $SPLUNK_HOME/etc/apps/TA-nmon/bin/nmon2csv.sh --mode realtime
    sourcetype = nmon_processing
    NO_BINARY_CHECK = true

    [source::.../*.nmon.gz]
    invalid_cause = archive
    unarchive_cmd = gunzip | $SPLUNK_HOME/etc/apps/nmon/bin/nmon2csv.sh
    sourcetype = nmon_processing
    NO_BINARY_CHECK = true


*PA-nmon (clustered indexer):*

::

    [source::.../*.nmon]
    invalid_cause = archive
    unarchive_cmd = $SPLUNK_HOME/bin/splunk cmd $SPLUNK_HOME/etc/slave-apps/PA-nmon/bin/nmon2csv.sh --mode realtime
    sourcetype = nmon_processing
    NO_BINARY_CHECK = true

    [source::.../*.nmon.gz]
    invalid_cause = archive
    unarchive_cmd = gunzip | $SPLUNK_HOME/bin/splunk cmd $SPLUNK_HOME/etc/apps/nmon/bin/nmon2csv.sh
    sourcetype = nmon_processing
    NO_BINARY_CHECK = true

+++++++++++++++++++++++++++++++++++++
02 - Nmon Performance Data definition
+++++++++++++++++++++++++++++++++++++

**This stanza defines the nmon_data sourcetype wich contains Nmon Performance data.**


::

    [nmon_data]

    FIELD_DELIMITER=,
    FIELD_QUOTE="
    HEADER_FIELD_LINE_NUMBER=1

    # your settings
    INDEXED_EXTRACTIONS=csv
    NO_BINARY_CHECK=1
    SHOULD_LINEMERGE=false
    TIMESTAMP_FIELDS=ZZZZ
    TIME_FORMAT=%d-%m-%Y %H:%M:%S

    # set by detected source type
    KV_MODE=none
    pulldown_type=true

    # Overwritting default host field based on event data for nmon_data sourcetype (useful when managing Nmon central shares)
    TRANSFORMS-hostfield=nmon_data_hostoverride

This uses csv format defined by csv header, and time stamp definition adapted for generated data from raw nmon data file.

It is being used by associated input monitor in props.conf that consumes csv data from csv_repository.

++++++++++++++++++++++++++++++++++++++++++++++++++++
03 - Nmon Processing definition (output of nmon2csv)
++++++++++++++++++++++++++++++++++++++++++++++++++++

**This stanza sets the appropriated time format for indexing of nmon2csv converters.**

::

    [nmon_processing]

    TIME_FORMAT=%d-%m-%Y %H:%M:%S
    This sourcetype contains useful information about processing steps operated by converters, such as the list of Nmon section proceeded, the number of events per section, processing various information and more.

+++++++++++++++++++++++++++++++++++++++
04 - Nmon Configuration Data definition
+++++++++++++++++++++++++++++++++++++++

**This stanza defines the nmon_config sourcetype wich contains Nmon Configuration data.**

::

    [nmon_config]

    BREAK_ONLY_BEFORE=CONFIG,
    MAX_EVENTS=100000
    NO_BINARY_CHECK=1
    SHOULD_LINEMERGE=true
    TIME_FORMAT=%d-%b-%Y:%H:%M
    TIME_PREFIX=CONFIG,
    TRUNCATE=0

    # Overwritting default host field based on event data for nmon_data sourcetype (useful when managing Nmon central shares)
    TRANSFORMS-hostfield=nmon_config_hostoverride

Events stored within this sourcetype are large multi line events containing items available in AAA and BBB* sections of Nmon.

---------------
transforms.conf
---------------

**Notable configuration used in default transforms.conf will concern the host default field override:**

::

    ###########################################
    #            nmon data stanza                        #
    ###########################################

    # Host override based on event data form nmon_data sourcetype

    [nmon_data_hostoverride]
    DEST_KEY = MetaData:Host
    REGEX = ^[a-zA-Z0-9\_]+\,[a-zA-Z0-9\-\_\.]+\,([a-zA-Z0-9\-\_\.]+)\,.+
    FORMAT = host::$1

    ###########################################
    #            nmon config stanza                    #
    ###########################################

    # Host override based on event data form nmon_config sourcetype

    [nmon_config_hostoverride]
    DEST_KEY = MetaData:Host
    REGEX = CONFIG\,[a-zA-Z0-9\-\:\.]+\,([a-zA-Z0-9\-\_\.]+)\,[a-zA-Z0-9\-\_\.]+
    FORMAT = host::$1

The reason for this is simple, when managing Nmon data that has been generated out of Splunk (so not by a Universal Forwarder or full Splunk instance runnning the Application), the "host" field which is a default Splunk field will have the value of the host that managed the data, and not the value of the real host that generated the Nmon data.

This will happens for example when using the Application in a central NFS repository scenario deployment.

Using the configuration above, Splunk will always and automatically rewrite the default host field based on the Nmon data, and not only on Splunk information


*********
Configure
*********

************
Troubleshoot
************

*******
Upgrade
*******



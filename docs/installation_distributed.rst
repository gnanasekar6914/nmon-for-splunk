================================
Deploy to distributed deployment
================================

----------------------------------------
Installation for distributed deployments
----------------------------------------

**Distributed deployment matrix:**

+--------------------------------------------+------------+----------+----------+
| Splunk Instance                            | Core App   | PA-nmon  | TA-nmon  |
| (role description)                         |            |          |          |
+============================================+============+==========+==========+
| Search head (single instance or clustered) |     X      |          |          |
+--------------------------------------------+------------+----------+----------+
| Indexer (single instance or clustered)     |            |    X     |          |
+--------------------------------------------+------------+----------+----------+
| Master node                                |            |          |    X     |
+--------------------------------------------+------------+----------+----------+
| Deployment servers                         |            |          |    X     |
+--------------------------------------------+------------+----------+----------+
| Heavy Forwarder                            |            |          |    X     |
+--------------------------------------------+------------+----------+----------+
| Universal Forwarder                        |            |          |    X     |
+--------------------------------------------+------------+----------+----------+

**The following installation tutorial covers all aspects of a distributed deployment scenario:**

* Standalone indexers
* Single site or multi site indexer clusters
* Standalone search heads
* Search heads in a sh cluster

*Note: Search heads in sh pooling deployment is not covered as it is a deprecated behavior, and has been replaced advantageously by sh clusters*

.. image:: img/steps_summary_distributed.png
   :alt: steps_summary_distributed.png
   :align: center

1. Deploying the PA-nmon on indexers
------------------------------------

1.1. Deploying the PA-nmon on clustered indexers (single site or multi site)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We will assume your indexers are already operational, in the case of a new installation, remember to activate port receiving to allow the indexer to retrieve data.

If required (eg. new installation), this can be easily achieved:

**in CLI:**

::

    /opt/splunk/bin/splunk enable listen 9997
    Where 9997 (default) will be the receiving port for Splunk Forwarder connections


Deploying the PA-nmon on clustered indexers
"""""""""""""""""""""""""""""""""""""""""""

*ALL THESE ACTION MUST BE DONE ON THE MASTER NODE*

**Download the Application tar.gz archive from:**

https://splunkbase.splunk.com/app/1753/

**Extract the content of the archive on your master node in a temporary directory, example:**

::

    cd /tmp/
    <upload the archive here>

    tar -xvzf nmon-performance-monitor-for-unix-and-linux-systems*.tgz

The PA-nmon is a tar.gz archive located in the "resources" of the core Application

It must be uncompressed and installed in the Master Node in $SPLUNK_HOME/etc/master_apps/ (where $SPLUNK_HOME refers to the root directory of your Splunk installation)

::

    cd /opt/splunk/etc/master/apps

    tar -xvzf /tmp/nmon/resources/PA-nmon*.tar.gz

In default configuration, the PA-nmon is able to generate Nmon Performance data. (which is why the PA-nmon contains file inputs and script inputs)

*if you don't want this, you can create a local/inputs.conf to deactivate these features:*

::

    cd /opt/splunk/etc/master_apps/PA-nmon

    mkdir local

    cp -p default/inputs.conf local/

    <edit local/inputs.conf>

    <replace:>

    disabled = false

    <by:>

    disabled = true

**Publish the cluster bundle to indexers, this implies an automatic rolling restart of indexers:**

::

    splunk apply cluster-bundle

**To see the current status of the indexer cluster:**

*In CLI:*

::

    splunk show cluster-bundle-status

*In Splunk Web, connected to the master node console:*

Settings --> Indexer Clustering

**Upon Rolling Restart of the indexer cluster, and if the local data performance collecting is activated, a new clustered index will be available in the indexer clustering console from the Master node:**

.. image:: img/cluster1.png
   :alt: cluster1.png
   :align: center

1.2. Deploying the PA-nmon on standalone indexers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*ALL THESE ACTION MUST BE DONE FOR EACH STANDALONE INDEXER*

**Download the Application tar.gz archive from:**

https://splunkbase.splunk.com/app/1753/

Extract the content of the archive on your indexer in a temporary directory, example:

::

    cd /tmp/

    <upload the archive here>

    tar -xvzf nmon-performance-monitor-for-unix-and-linux-systems*.tgz

The PA-nmon is a tar.gz archive located in the "resources" of the core Application

It must be uncompressed and installed in the indexer in $SPLUNK_HOME/etc/apps/ (where $SPLUNK_HOME refers to the root directory of Splunk installation)

::

    cd /opt/splunk/etc/apps

    tar -xvzf /tmp/nmon/resources/PA-nmon*.tar.gz

In default configuration, the PA-nmon is able to generate Nmon Performance data. (which is why the PA-nmon contains file inputs and script inputs)

*if you don't want this, you can create a local/inputs.conf to deactivate these features:*

::

    cd /opt/splunk/etc/apps/PA-nmon

    mkdir local

    cp -p default/inputs.conf local/

    <edit local/inputs.conf>

    <replace:>

    disabled = false

    <by:>

    disabled = true

If you want to get Performance data to be generated automatically by the Application on your standalone indexers, you must set a custom configuration of props.conf (only applicable for standalone indexers):

::

    cd /opt/splunk/etc/apps/PA-nmon

    mkdir local

    cp -p default/props.conf local/

    <edit local/props.conf>

    <replace:>

    unarchive_cmd = $SPLUNK_HOME/bin/splunk cmd $SPLUNK_HOME/etc/slave-apps/PA-nmon/bin/nmon2csv.sh

    <by:>

    unarchive_cmd = $SPLUNK_HOME/bin/splunk cmd $SPLUNK_HOME/etc/apps/PA-nmon/bin/nmon2csv.sh

    And:

    unarchive_cmd = gunzip | $SPLUNK_HOME/bin/splunk cmd $SPLUNK_HOME/etc/slave-apps/PA-nmon/bin/nmon2csv.sh

    By:

    unarchive_cmd = gunzip | $SPLUNK_HOME/bin/splunk cmd $SPLUNK_HOME/etc/apps/nmon/bin/nmon2csv.sh

**Restart the indexer:**

::

    splunk restart


















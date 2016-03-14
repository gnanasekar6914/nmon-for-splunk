======================
Deploy to Splunk Cloud
======================

Deploying Nmon Performance Monitor in Splunk Cloud

Nmon Performance Monitor can totally be installed and used will all functionality with the power of Splunk Cloud:

The Nmon core application will be deployed to Splunk Cloud

Optionally, you can use Nmon Performance to monitor the Splunk Cloud instance itself (the same you would with a single Splunk instance)
The TA-nmon App will be deployed to your servers and will send performance data to Splunk Cloud transparently

STEP 1: Deploy Nmon Core Application to Splunk Cloud

Once connected to your Splunk Cloud instance, go to the Application menu management:

STEP 1: Deploy Nmon Core Application to Splunk Cloud

Once connected to your Splunk Cloud instance, go to the Application menu management:

.. image:: img/install_splunkcloud1.png
   :alt: install_splunkcloud1.png
   :align: center

Search for the Nmon App and confirm installation:

.. image:: img/install_splunkcloud2.png
   :alt: install_splunkcloud2.png
   :align: center

Finally, Nmon Performance Monitor is installed:

.. image:: img/install_splunkcloud3.png
   :alt: install_splunkcloud3.png
   :align: center

STEP 2 (Optional): Activate local Performance monitor of your Splunk Cloud instance

Splunk Cloud are running Linux OS, and if you like it can be easily monitored by the Nmon Core Application such that you will even know about your Splunk Cloud instance:

This is very simply achieved by activating local inputs scripts:

Click on Activate for both inputs (nmon_helper and nmon_clean)

.. image:: img/install_splunkcloud4.png
   :alt: install_splunkcloud4.png
   :align: center

Within a few minutes, Performance data will start to be collected and available within the Nmon Application:

.. image:: img/install_splunkcloud5.png
   :alt: install_splunkcloud5.png
   :align: center

STEP 3: Deploy TA-nmon to your Universal Forwarders agents to send Performance data to your Splunk Cloud instance

If you haven't already, open the Universal Forwarder Application and follow instructions to install and configure your Universal Forwarder instances to send data to Splunk Cloud:

Open the Universal Forwarder App:

screen004.png
Follow instructions and install Splunk Credentials:

screen005.png
Optionally if this is your first Universal Forwarder deployment to Splunk Cloud, you can easily validate your installation by confirming the Universal Forwarders sends data to Splunk Cloud:

—> Search Splunk internal events:

index=_internal
As exposed above, you should see incoming events from your host, example:

screen006.png
Finally deploy the TA-nmon to your Universal Forwarder instance(s):

—> Upload the TA-nmon package to your servers

—> Install the TA-nmon:

Manually:

The TA-nmon is a tar.gz archive located in the "resources" of the core Application
It must be uncompressed and installed in the indexer in $SPLUNK_HOME/etc/deployment-apps/ (where $SPLUNK_HOME refers to the root directory of Splunk installation)
cd /opt/splunkforwarder/etc/apps

tar -xvzf /tmp/TA-nmon_*.tar.gz
Restart the Universal Forwarder
Using Splunk CLI:

You can install the TA-nmon using Splunk CLI:
/opt/splunkforwarder/bin/splunk install app /tmp/TA-nmon_*.tar.gz -auth admin:changeme
Restart the Universal Forwarder
Example:

[root@RHEL7 ~]# /opt/splunkforwarder/bin/splunk install app /media/BIGDATA/TA-nmon_V1.2.27.tar.gz -auth admin:changeme
App '/media/BIGDATA/TA-nmon_V1.2.27.tar.gz' installed
You need to restart the Splunk Server (splunkd) for your changes to take effect.

[root@RHEL7 ~]# /opt/splunkforwarder/bin/splunk restart
Stopping splunkd...
Shutting down.  Please wait, as this may take a few minutes.
                                                           [  OK  ]
Stopping splunk helpers...
                                                           [  OK  ]
Done.

Splunk> The Notorious B.I.G. D.A.T.A.

Checking prerequisites...
    Checking mgmt port [8089]: open
    Checking conf files for problems...
    Done
All preliminary checks passed.

Starting splunk server daemon (splunkd)...
Done
                                                           [  OK  ]
[root@RHEL7 ~]#
STEP 4: Check your work and verify incoming Performance events

A few minutes later you will immediately start to receive incoming Performance data from your servers:

screen009.png
And you will find incoming data from your host(s):

screen010.png
Recommended: After you added new hosts to your deployment, you can immediately update configuration information by running the dedicated report (this is operation is done by default every hour):

screen011.png
Et voilà !!! There you go, enjoy :-)


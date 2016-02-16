
#########################################
About Nmon Performance Monitor for Splunk
#########################################

* Author: Guilhem Marchand

* First release was published on starting 2014

* Purposes:

In very brieve description, the Nmon Performance application for Splunk uses the excellent and powerful nmon binary known as Nigel's Performance Monitor.
Originally developed for IBM AIX Performance monitoring and Analysis, it is now an Open source Projects that made it available to many other systems.
It is fully available for any Linux flavor, and thanks to the excellent work of Guy Deffaux it also available for Solaris 10/11 systems using the sarmon project.

The Nmon Performance monitor application for Splunk will generate Performance and Inventory data for your hosts, and provides a rich number of monitors and tools to manage your AIX / Linux / Solaris systems.

* Splunk versions:

The core Application available on Splunk base requires Splunk 6.2.x or superior to operate normally (in distributed deployments, only search heads requires Splunk 6.2.x)

The last release compatible with Splunk 6.x can be downloaded here: https://splunkbase.splunk.com/app/1753/release/1.4.91/agree/

The PA-nmon add-on available in the resources directory of the core Application is designed to be installed in indexers (clusters or standalone), it is compatible with Splunk 6.x (Splunk 5.x shall not be used as the App intensively uses Data Model acceleration which is not available in Splunk 5.x and prior)

The TA-nmon add-on available in the resources directory of the core Application is designed to be installed in Universal Forwarders end clients or Heavy Forwarders, it is only compatible with Splunk 6.x (Splunk 5.x and prior will not be able to extract fields definition from generated data, leading to the Application being unable to analyse performance data)

* Index time operations:

The Application operates index time operation, the PA-nmon add-on must be installed in indexers in order for the Application to operate normally.
In complex Distributed Deployments, Heavy Forwarders (which are full Splunk instances) can operate as intermediate collectors between indexers and Universal Forwarder clients, in such a case Heavy Forwarders with the TA-nmon add-on installed will operate index time parsing and will free indexers from these tasks.

* Index creation:

The Application creates an index called "nmon" at installation time, this is where the application stores its data
In Standalone installation, the Nmon core Application will creates the "nmon" index at Splunk restart beyond installation.

In Distributed Deployments using clusters of indexers, the PA-nmon add-on will automatically creates the "nmon" replicated index

* Summarization implementation:

Nmon for Splunk App intensively uses Data Model acceleration in almost every user interfaces, reports and dashboards
The Application provides multiple Data Model that have the acceleration activated by default using All time as the time range limit

Because of the richness of Nmon monitors, and with large deployments, Data Model acceleration offers an improved user experience
Note that some minor scheduled reports that keep users informed of the application activity in home page uses standard acceleration
About me and Nmon Performance Monitor:

Nmon Performance Monitor for Splunk is provided in Open Source, you are totally free to use it for personal or professional use without any limitation,
and you are free to modify sources or participate in the development if you wish.

Feedback and rating the App will be greatly appreciated.

* Join the Google group: https://groups.google.com/d/forum/nmon-splunk-app


* App's Github page: https://github.com/guilhemmarchand/nmon-for-splunk


* Videos: https://www.youtube.com/channel/UCGWHd40x0A7wjk8qskyHQcQ





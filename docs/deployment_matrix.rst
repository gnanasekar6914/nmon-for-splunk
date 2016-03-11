#################
Deployment Matrix
#################

What goes where
---------------

* Core App: This is the full package you download in Splunk Base (tgz archive)

* PA-nmon: Is available in the resources directory of the Core App (tgz archive)

* TA-nmon: Is available in the resources directory of the Core App (tgz archive)

**Standalone deployment: A single Splunk instance does all**

+------------------------+------------+----------+----------+
| Splunk Instance        | Core App   | PA-nmon  | TA-nmon  |
| (role description)     |            |          |          |
+========================+============+==========+==========+
| Standalone             |     X      |          |          |
+------------------------+------------+----------+----------+

**Distributed deployment:**

+------------------------+------------+----------+----------+
| Splunk Instance        | Core App   | PA-nmon  | TA-nmon  |
| (role description)     |            |          |          |
+========================+============+==========+==========+
| Search head            |     X      |          |          |
+------------------------+------------+----------+----------+
| Standalone indexer     |            |    X     |          |
+------------------------+------------+----------+----------+
| Distributed indexer    |            |    X     |          |
+------------------------+------------+----------+----------+
| Master node            |            |          |    X     |
+------------------------+------------+----------+----------+
| Deployment servers     |            |          |    X     |
+------------------------+------------+----------+----------+
| Heavy Forwarder        |            |          |    X     |
+------------------------+------------+----------+----------+
| Universal Forwarder    |            |          |    X     |
+------------------------+------------+----------+----------+

**FAQ:**

* What is the difference between the PA-nmon and the TA-nmon ?

*The PA-nmon is the light addon dedicated for indexers, it is very closed to the TA-nmon, but it is adapted to
be able to automatically generate Nmon performance data for your distributed indexers.
The PA-nmon addon will be included in the bundle configuration published to indexers by the master node.*

* Why isn't the TA-nmon required in standalone instances or search heads ?

*Because the Core application also includes binaries and required configuration items, it is able to generate Nmon Performance data
with no additional software.
Note that by default the Core application will not activate the data generation, and this must be activated from the application
for standalone installations, or from a local configuration published in the search head bundle for search head clusters.*









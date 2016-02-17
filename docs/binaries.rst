====================
Scripts and Binaries
====================

Scripts and Binaries embedded in Nmon Performance Monitor

The Application brings various scripts and binaries required for data generation, processing and management

* Embedded Scripts:

NOTE: All these scripts are included in strictly same versions in the Core application, PA-nmon and TA-nmon add-ons

 * bin/nmon_helper.sh:

Shell script used by the Application to launch Nmon binaries whenever required

More information

 * bin/nmon2csv.sh | bin/nmon2csv.py | bin/nmon2csv.pl:

Shell / Python / Perl scripts used to manage and process Nmon raw data into multiple csv files being indexed by Splunk
More information

 * bin/nmon_cleaner.sh:

Shell script used to purge old Nmon raw data whenever required

More information

 * resources/Nmon_SplunkApp_Customize.py.gz:

Python script (provided compressed, must be uncompressed before use) that allows complex and automatic customization of the Application to fit your company standards, such as renaming the Application root directory, changing the data index name, changing PA-nmon and TA-nmon add-on root directories

Its utilization is documented here

 * resources/create_agent.py.gz:

Python script (provided compressed, must be uncompressed before use) that allows creating supplementary TA-nmon add-on custom packages to manage deployment for different scenarios from the same root (allows for example having a specific TA-nmon package for AIX servers that custom modifications, and another for Linux servers)

Its utilization is documented here

* Embedded Binaries:

NOTE: All these binaries are included in strictly same versions in the Core application, PA-nmon and TA-nmon add-ons

The Applications brings Nmon binaries for Linux vendors and Solaris OS, on AIX the application will only try to use the version shipped with system

 * bin/linux: Main directory for Linux specific Nmon binaries
 * bin/linux/centos: 32/64 bits binaries for Centos
 * bin/linux/debian: 32/64 bits binaries for Debian GNU/Linux
 * bin/linux/fedora: 32/64 bits binaries for Fedora project
 * bin/linux/generic: 32/64/ia64/power/mainframe binaries compiled for generic Linux
 * bin/linux/mint: 32/64 bits binaries for Linux Mint
 * bin/linux/opensuse: 32/64 bits binaries for Linux Opensuse
 * bin/linux/rhel: 32/64/ia64/mainframe/power binaries for Redhat Entreprise Server
 * bin/linux/sles: 32/64/ia64/power binaries for Suse Linux Entreprise Server
 * bin/linux/ubuntu: 32/64/power binaries for Ubuntu Linux

All these binaries comes from the official Nmon Linux project site

Associated scripts resource (nmon_helper.sh) will try to use the better version of Nmon available, it will fall back to generic or system embedded if none of specially compiled versions can fit the system.

* sarmon binaries for Oracle Solaris x86 and Sparc:

 * bin/sarmon_bin_i386: sarmon binaries for Solaris running on x86 arch
 * bin/sarmon_bin_sparc: sarmon binaris for Solaris running on sparc arch
 * sarmon binaries comes from the official sarmon site project

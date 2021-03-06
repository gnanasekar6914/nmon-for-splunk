
.. _scripts_and_binaries:

====================
Scripts and Binaries
====================

**Scripts and Binaries embedded in Nmon Performance Monitor:**

*The Nmon core application does not collect or generate any performance and configuration data, and does not contain any related scripts or binaries*

**Technical addon: please consult the TA-nmon dedicated documentation:**

* http://ta-nmon.readthedocs.io

However, the Nmon core application contains scripted tools to be used for advanced customization purposes, as described above.

************************************
Embedded Scripts in technical addons
************************************

* resources/Nmon_SplunkApp_Customize.py.gz:

This Python script (must be uncompressed before execution) is a tool provided to execute different kind of automated customizations, such as restricting the application purpose to a given operating system for instance. (hide AIX and Solaris)

Detailed documentation: :ref:`Nmon_SplunkApp_Customize_py`

****************
Additional tools
****************

* create_agent.py available in the Git repository https://github.com/guilhemmarchand/TA-nmon:

This Python script is a tool provided to create different version of the TA-nmon technical addon.

For example, you can use it to create a specific TA-nmon version for your critical production servers, and another version for your non production servers.

Each of the TA-nmon version would have its own parameters, such as the indexes, the data accuracy (interval between measures), etc.

Detailed documentation: :ref:`create_agent_py`

Study of usage: :ref:`split_by_datacenter`

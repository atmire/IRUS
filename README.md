# IRUS

**Table of Contents**

- [Adding the functionality to your DSpace codebase ](#Patch-installation-procedures)
    - [Prerequisites](#Prerequisites)
    - [Patch installation ](#Patch-installation)
        - [1. Go to the DSpace Source directory. ](#goto-DSpace-Source)
        - [2. Run the Git command to check whether the patch can be correctly applied. ](#Run-git-command)
        - [3. Apply the patch ](#Apply-patch)
        - [4. Rebuild and redeploy your repository ](#Rebuild-redeploy)
        - [5. Restart your Tomcat ](#Restart-tomcat)
        - [6. Verify the firewall ](#Firewall-verification)
- [Configuration](#Configuration)
- [Functionality of the patch](#Patch-functionality)
    - [Retrying failed commits](#Failed-commits-retry)


# Adding the functionality to your DSpace codebase <a name="Patch-installation-procedures"></a> #

## Prerequisites  <a name="Prerequisites"></a> ##

The IRUS changes have been released as a "patch" for DSpace as this allows for the easiest installation process of the incremental codebase. The code needed to install and deploy the IRUS changes can be found in the [Obtaining a recent patch file](#Obtaining-recent-patch) section, which needs to be applied to your DSpace source code.

**__Important note__**: Below, we will explain you how to apply the patch to your existing installation. This will affect your source code. Before applying a patch, it is **always** recommended to create backup of your DSpace source code.

In order to apply the patch, you will need to locate the **DSpace source code** on your server. That source code directory contains a directory _dspace_, as well as the following files:  _LICENSE_,  _NOTICE_ ,  _README_ , ....

For every release of DSpace, generally two release packages are available. One package has "src" in its name and the other one doesn't. The difference is that the release labelled "src" contains ALL of the DSpace source code, while the other release retrieves precompiled packages for specific DSpace artifacts from maven central. **The IRUS patches were designed to work on both "src" and other release packages of DSpace**.

To be able to install the patch, you will need the following prerequisites:

* A running DSpace 4.x, 5.x or 6.x instance.
* Git should be installed on the machine. The patch will be applied using several git commands as indicated in the next section.

## Obtaining a recent patch file<a name="Obtaining-recent-patch"></a> ##

Atmire's modifications to a standard DSpace for IRUS are tracked on Github. The newest patch can therefore be generated from git.

- DSPACE 4.x [https://github.com/atmire/IRUS/compare/dspace_4x…stable_4x.diff](https://github.com/atmire/IRUS/compare/dspace_4x…stable_4x.diff)
- DSPACE 5.x [https://github.com/atmire/IRUS/compare/dspace_5x…stable_5x.diff](https://github.com/atmire/IRUS/compare/dspace_5x…stable_5x.diff)
- DSPACE 6.x [https://github.com/atmire/IRUS/compare/dspace_6x…stable_6x.diff](https://github.com/atmire/IRUS/compare/dspace_6x…stable_6x.diff)

Save this file under a meaningful name. It will be later referred to as \<patch file\>

## Patch installation<a name="Patch-installation"></a> ##

To install the patch, the following steps will need to be performed.

### 1. Go to the DSpace Source directory.<a name="goto-DSpace-Source"></a> ###

This folder should have a structure similar to:
dspace
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    config
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    modules
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    ...
pom.xml

### 2. Run the Git command to check whether the patch can be correctly applied.<a name="Run-git-command"></a> ###

Run the following command where \<patch file\> needs to be replaced with the name of the patch:

```
git apply --check <patch file>
```

This command will return whether it is possible to apply the patch to your installation. This should pose no problems in case the DSpace is not customized or in case not much customizations are present.
In case the check is successful, the patch can be installed as explained in the next steps.


### 3. Apply the patch<a name="Apply-patch"></a> ###

To apply the patch, the following command should be run where \<patch file\> is replaced with the name of the patch file.

```
git apply <patch file>
```

Applying the patch should result in an output similar to this

```
patching file dspace/config/launcher.xml
patching file dspace/config/modules/stats.cfg
patching file dspace/config/spring/jspui/open-url-listeners.xml
patching file dspace/config/spring/xmlui/open-url-listeners.xml
patching file dspace/modules/atmire-statistics-exporter/atmire-statistics-exporter-
api/pom.xml
patching file dspace/modules/atmire-statistics-exporter/atmire-statistics-exporter-
api/src/main/java/com/atmire/statistics/export/ExportUsageEventListener.java
patching file dspace/modules/atmire-statistics-exporter/atmire-statistics-exporter-
api/src/main/java/com/atmire/statistics/export/OpenUrlTrackerLogger.java
...
...
```

There may be various warnings about whitespaces, but these will pose no problems when applying the patch and can be ignored.
Some IDE's also have a built in ui which allows the user to apply patches manually.
This might also help during conflicts.

### 4. Rebuild and redeploy your repository <a name="Rebuild-redeploy"></a> ###

After the patch has been applied, the repository will need to be rebuild.
DSpace repositories are typically built using Maven and deployed using Ant.

### 5. Restart your Tomcat <a name="Restart-tomcat"></a> ###

After the repository has been rebuild and redeployed, Tomcat will need to be restarted to bring the changes to production.

### 6. Verify the firewall <a name="Firewall-verification"></a> ###

The server should be able to access the tracker’s base URL. This can be verified easily using the command:

```
wget http://jusp.jisc.ac.uk/counter/
```

If this results in an exception, please verify whether the firewall is configured to authorize outgoing connections.

# Configuration <a name="Configuration"></a> #

A new stats.cfg has been created in the modules configuration directory to offer compatibility with your repository. Below is an excerpt from the configuration and a short explanation of every property that is used.

```
#-----------------------#
# Atmire stats exporter #
#-----------------------#

# OPTIONAL metadata field used for filtering.
# If the returned items require specific values for the "dc.type" field, "dc.type" should be placed here.
# This should comply to the syntax schema.element.qualified or schema.element if the qualifier is null.
tracker.type-field = dc.type
# If "tracker.type-field" is set, the list of values must be defined in "tracker.type-value".
# This lists a comma separated list of values which can be used for the given field.
tracker.type-value = Article, Postprint

# The metadata field containing the doi identifier of the record. This field is optional
tracker.doi-field=dc.identifier.doi
# The metadata field containing the version of the record. This field is optional
tracker.version-field=dc.type.version
# The base url for submitting the tracking info to.
tracker.baseurl = http://jusp.jisc.ac.uk/counter/
# Identifies data as OpenURL 1.0
tracker.urlversion = Z39.88-2004

# The deployed user interface should be provided to build correct links to files.
# The dspace.type field can be set to either "xmlui" or "jspui".
dspace.type = xmlui

# Spider options
spider.ipmatch.enabled = true
spider.agentempty.enabled = false
spider.agentregex.enabled = true
# Default is downloaded during build: ${dspace.dir}/config/COUNTER_Robots_list_Jul2016.txt
spider.agentregex.regexfile = ${dspace.dir}/config/COUNTER_Robots_list_Jul2016.txt
```

|Property |Usage |Default 
| --------|--------| -------|
|tracker.type-field| Only a certain set of file downloads can be sent depending on the value of a certain metadata field, the metadata field to check is configured in this property.|All stats are sent 
|tracker.type-value| The values in the above metadata field that will be processed.|All stats are sent 
|tracker.baseurl | The url to which the stats are exported.| N/A
|tracker.urlversion| Tracker version| N/A
|dspace.type| The url to download a file differs between xmlui & jspui, so the exporter needs to know which link to create.| xmlui
|spider.ipmatch.enabled| If the IP address matches a spider IP addresses from a txt list that comes with your dspace installation (this is the default implementation - lists in /config/spiders/)| true
|spiders.agentempty.enabled | Checks the user agent (a header string that comes with every request). If this agent is empty, it is most likely not a user browser. This verification is disabled by default.|false 
|spiders.agentregex.enabled| Matches the user agent vs a defined set of regular expressions (provided by an online service), which are downloaded during installation of your dspace repository, or during an update.| true
|spiders.agentregex.regexfile| Defines where the file should be downloaded to/where the system should search for the regexes| N/A


# Functionality of the patch <a name="Patch-functionality"></a> #
## Retrying failed commits <a name="Failed-commits-retry"></a> ##
If the OpenURLTracker system is down or some other kind of error should occur, which prevents the ExportUsageEventLogger from committing to the tracker, this record is stored in the database (using the separate table OpenUrlTracker created automatically during deployment). Committing these entries can be tried again at a later time via the //retry-tracker// command (command line). This will iterate over all the logged entries and retry committing them. If they fail again, they remain in the table, if they succeed, they are removed from this table.

This script can be executed by navigating to the //"bin"// folder inside your **deployed dspace installation** (not the source where the patch has been applied - it should not contain a "//src//" folder).

The //retry-tracker// script can be executed using the following commando:

```
./dspace retry-tracker
```

It is strongly advised to schedule this script to be executed daily or weekly (preferable at low load-times during the night or weekend). If there are no failed entries, the script will not perform any actions and exit immediately.

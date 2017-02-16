# DSpace IRUS-UK patch

- [IRUS-UK information](#IRUS)
- [Prerequisites](#Prerequisites)
    - [Codebase](#Codebase)
    - [Firewall verification](#Firewall-verification)
    - [Download patch](#Download-patch)
- [Installation](#Installation)
    - [1. Go to the DSpace Source directory](#Installation-src-dir)
    - [2. Check patch compatibility](#Check-patch)
    - [3. Apply the patch](#Apply-patch)
    - [4. Tracker configuration](#Tracker-configuration)
    - [5. Rebuild and redeploy your repository](#Rebuild-redeploy)
    - [6. Restart Tomcat](#Restart-tomcat)
- [Configuration overview](#Configuration-overview)
- [Additional functionality](#Additional-functionality)
    - [Retry failed trackings](#Failed-trackings)


## <a name="IRUS"></a> IRUS-UK

IRUS (Institutional Repository Usage Statistics) enables UK Institutional Repositories (IRs) to share and expose statistics based on the COUNTER standard. It provides a nation-wide view of UK repository usage to benefit organisations such as [Jisc](https://www.jisc.ac.uk/), offers opportunities for benchmarking and acts as an intermediary between UK repositories and other agencies.

IRUS-UK collects raw usage data from UK IRs and processes these data into COUNTER-compliant statistics. This provides repositories with comparable, authoritative, standards-based data.

IRUS-UK is being developed by a consortium involving [Jisc](https://www.jisc.ac.uk/), [Cranfield University](https://www.cranfield.ac.uk/) and [Evidence Base](http://www.bcu.ac.uk/evidence-base).

The DSpace IRUS-UK patch has been developed and is maintained by [Atmire](https://www.atmire.com), a registered service provider for DSpace.

## <a name="Prerequisites"></a> Prerequisites

### <a name="Codebase"></a> Codebase

The IRUS changes have been released as a "patch" for DSpace as this allows for the easiest installation process of the incremental codebase. The code needed to install and deploy the IRUS changes can be found in the [Download patch](#Download-patch) section, which needs to be applied to your DSpace source code.

**__Important note__**: Below, we will explain you how to apply the patch to your existing installation. This will affect your source code. Before applying a patch, it is **always** recommended to create backup of your DSpace source code.

In order to apply the patch, you will need to locate the **DSpace source code** on your server. That source code directory should look similar to the following structure:

```
[dspace-src]
  - dspace
  - ...
  - LICENSE
  - NOTICE
  - README 
```

For every release of DSpace, generally two release packages are available. One package has "src" in its name and the other one doesn't. The difference is that the release labelled "src" contains ALL of the DSpace source code, while the other release retrieves precompiled packages for specific DSpace artifacts from maven central. **The IRUS patches were designed to work on both "src" and other release packages of DSpace**.

To be able to install the patch, you will need the following prerequisites:

* A running DSpace 4.x, 5.x or 6.x instance.
* Git should be installed on the machine. The patch will be applied using several git commands as indicated in the next section.

### <a name="Firewall-verification"></a> Firewall verification

The server should be able to access the tracker’s base URL. This can be verified easily using the following commands:

```
wget http://jusp.jisc.ac.uk/testcounter/
wget http://jusp.jisc.ac.uk/counter/
```

If this results in an exception, please verify whether the firewall is configured to authorize outgoing connections.

### <a name="Download-patch"></a> Download patch

Atmire's modifications to a standard DSpace for IRUS are tracked on Github. The newest patch can therefore be generated from git.

| DSpace | Patch                                                                       |
| ------ | --------------------------------------------------------------------------- |
| 4.x    | [Download](https://github.com/atmire/IRUS/compare/dspace_4x…stable_4x.diff) |
| 5.x    | [Download](https://github.com/atmire/IRUS/compare/dspace_5x…stable_5x.diff) |
| 6.x    | [Download](https://github.com/atmire/IRUS/compare/dspace_6x…stable_6x.diff) |


Save this file under a meaningful name. It will later be referred to as `<patch>` .

## <a name="Installation"></a> Installation

To install the patch, the following steps will need to be performed.

### <a name="Installation-src-dir"></a> 1. Go to the `dspace` directory

This folder should have a structure similar to:

```
[dspace-src]
  - dspace          <-- Change the working directory to this folder
      - config
      - modules
      - ...
      - pom.xml
  - ...
  - LICENSE
  - NOTICE
  - README 
```

### <a name="Check-patch"></a> 2. Check patch compatibility  ###

Run the following command where `<patch>` needs to be replaced with the name of the patch:

```bash
git apply --check <patch>
```

This command will return whether it is possible to apply the patch to your installation. This should pose no problems in case the DSpace is not customized or in case few customizations are present.
In case the check is successful, the patch can be installed as explained in the next steps.


### <a name="Apply-patch"></a> 3. Apply the patch ###

To apply the patch, the following command should be run where  `<patch>` is replaced with the name of the patch file.

```bash
git apply --whitespace=nowarn --reject <patch>
```

This command will tell git to apply the patch and ignore unharmful whitespace issues. The `--reject` flag instructs the command to continue when conflicts are encountered and saves the corresponding code hunks to a `.rej` file so you can review and apply them manually later on. This flag can be omitted if desired.

Applying the patch should result in an output similar to the following:

```
patching file dspace/config/launcher.xml
patching file dspace/config/modules/stats.cfg
patching file dspace/config/spring/jspui/open-url-listeners.xml
patching file dspace/config/spring/xmlui/open-url-listeners.xml
...

```

Some IDEs might have a built-in UI which allows you to apply patches visually. This could help during conflicts.

### <a name="Tracker-configuration"></a> 4. Tracker configuration

**PRODUCTION REPOSITORIES ONLY**

By default your repository will sent data to a TEST tracker to avoid sending unintentional test usage data. On production servers, please change the `tracker.environment` configuration (or `stats.tracker.environment` in case of DSpace 6.x) found in `[dspace-src]/dspace/config/modules/stats.cfg` to `production` to start sending data to the live IRUS tracker.

```
tracker.environment = production
```

A complete overview of all configuration options is available below.

### <a name="Rebuild-redeploy"></a> 5. Rebuild and redeploy

After the patch has been applied, the repository will need to be rebuild.
DSpace repositories are typically built using Maven and deployed using Ant.

### <a name="Restart-tomcat"></a> 6. Restart Tomcat

After the repository has been rebuilt and redeployed, Tomcat will need to be restarted to bring the changes live.

## <a name="Configuration-overview"></a> Configuration overview

A new `stats.cfg` file will be created in `[dspace-src]/dspace/config/modules`.

> **Important note**: all configurations in DSpace 6.x are prefixed with `stats.`, e.g. `stats.tracker.type-field`.

| Property | Description | Default 
| -------- | ----------- | -------|
|`tracker.type-field`| Metadata field to check if certain items should be excluded from tracking. If empty or commented out, all items are tracked. |All
|`tracker.type-value`| The values in the above metadata field that will be considered to be tracked.|All
|`tracker.environment`| The tracker environment determines to which url the statistics are exported.| test
|`tracker.testurl`| The url to which the trackings are exported when testing.|
|`tracker.produrl`| The url to which the trackings are exported in production.|
|`tracker.urlversion`| Tracker version|
|`dspace.type`| The UI utilized by DSpace.| xmlui
|`spider.ipmatch.enabled`| Boolean whether to ignore spider trackings; uses the lists in `/config/spiders`| true
|`spiders.agentempty.enabled`| Boolean whether to ignore trackings originating from requests having an empty `User-Agent` header (most likely not a user browser).|false 
|`spiders.agentregex.enabled`| Boolean whether `User-Agent` should be matched against a known lists of user agents (provided by an online service; downloaded during DSpace build).| true
|`spiders.agentregex.regexfile`| Location where the user agents file should be downloaded to.|


## <a name="Additional-functionality"></a> Additional functionality
### <a name="Failed-trackings"></a> Retry failed trackings
If the IRUS-UK tracker is down or some other kind of error should occur preventing from committing to the tracker, the record is stored in the database in a separate table (`OpenUrlTracker`) created automatically during deployment. Committing these entries can be tried again using the following command.

```bash
[deployed-dspace]/bin/dspace retry-tracker
```

This will iterate over all the logged entries and retry committing them. If they fail again, they remain in the table, if they succeed, they are removed.

It is strongly advised to schedule this script to be executed daily or weekly (preferable at low load-times during the night or weekend). If there are no failed entries, the script will not perform any actions and exit immediately.

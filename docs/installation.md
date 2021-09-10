# Installation

To install the patch, the following steps will need to be performed.

## 1. Go to the `dspace` directory

This folder should have a structure similar to:

```
[dspace-src]          <-- Change the working directory to this folder
  - dspace
      - config
      - modules
      - ...
      - pom.xml
  - ...
  - LICENSE
  - NOTICE
  - README 
```

## 2. Check patch compatibility

Run the following command where `<patch>` needs to be replaced with the name of the patch:

```bash
git apply --check <patch>
```

This command will return whether it is possible to apply the patch to your installation. This should pose no problems in case the DSpace is not customized or in case few customizations are present.
In case the check is successful, the patch can be installed as explained in the next steps.

## 3. Apply the patch

To apply the patch, the following command should be run where  `<patch>` is replaced with the name of the patch file.

```bash
git apply --whitespace=nowarn --reject <patch>
```

This command will tell git to apply the patch and ignore unharmful whitespace issues. The `--reject` flag instructs the command to continue when conflicts are encountered and saves the corresponding code hunks to a `.rej` file so you can review and apply them manually later on. This flag can be omitted if desired.

Applying the patch should result in an output similar to the following:

```
...
Applied patch dspace/modules/pom.xml cleanly.
Applied patch dspace/modules/xmlui/pom.xml cleanly.
Applied patch dspace/pom.xml cleanly.
Applied patch dspace/src/main/config/build.xml cleanly.

```

Some IDEs might have a built-in UI which allows you to apply patches visually. This could help during conflicts.

## 4. Tracker configuration

!> The following should be done for PRODUCTION REPOSITORIES ONLY.

By default your repository will sent data to a TEST tracker to avoid sending unintentional test usage data. On production servers, please change the `tracker.environment` configuration (or `stats.tracker.environment` in case of DSpace 6.x) found in `[dspace-src]/dspace/config/modules/stats.cfg` to `production` to start sending data to the live IRUS tracker.

```
tracker.environment = production
```
Setting `tracker.environment` to production will make your repository send stats events to the URL configured in `tracker.produrl` (in the same file). This URL has originally been configured with the default IRUS production URL, i.e. https://irus.jisc.ac.uk/counter/. If you are located in a country/area using a specific tracker URL, you should also update the configuration of `tracker.produrl`. Currently, the following locations are supported by IRUS:

- United Kingdom (default): http://irus.jisc.ac.uk/counter/
- Australia and New-Zealand: https://irus.jisc.ac.uk/counter/anz/
- United States: https://irus.jisc.ac.uk/counter/us/

## 5. Rebuild and redeploy

After the patch has been applied, the repository will need to be rebuilt.
DSpace repositories are typically built using Maven and deployed using Ant.

Please use `mvn clean package` instead of `mvn package` to avoid errors in the user interface. If `clean` is not specified some classes might not be updated correctly.

## 6. Running flyway migration manually

To make sure that the database contains the newly added 'OpenUrlTracker' table, it is advised to manually trigger a flyway migration, since flyway does not always run these migration automatically when 'newer' migrations have already been applied.
In the installation directory of DSpace, run the following command.

> bin/dspace database migrate ignored

## 7. Restart Tomcat

After the repository has been rebuilt and redeployed, Tomcat will need to be restarted to bring the changes live.

## 8. Testing and verification

If you are testing on a test environment, the default configuration will send the data to the IRUS test tracker (config `tracker.testurl` = https://irus.jisc.ac.uk/counter/test/).

If you are testing on a production environment, your configuration should have been updated to `tracker.environment = production` and, if appropriate, the `tracker.produrl` should have been updated as well (Refer to 4. Tracker configuration).

Internal testing can be executed by verifying in the DSpace logs that the statistics events are correctly sent to the tracker URL upon item view / bitstream download. In order to do so, you need to : 
- Make sure the debug logging is enabled for the relevant java class ExportUsageEventListener in [log4j.properties](https://github.com/atmire/IRUS/blob/stable_6x/dspace/config/log4j.properties#L119)
- Browse your repository and generate a few items views and bitstreams downloads
- Verify the dspace logs under `[dspace]/dspace/log/dspace.log.YYYY-MM-DD` and check for the occurrences of the following logs:
```
DEBUG com.atmire.statistics.export.ExportUsageEventListener @ Prepared to send url to tracker URL: https://irus.jisc.ac.uk/counter/test/ [...]
DEBUG com.atmire.statistics.export.ExportUsageEventListener @ Successfully posted https://irus.jisc.ac.uk/counter/test/ [...]
```

You should also request the [IRUS helpdesk](mailto:irus@jisc.ac.uk) to confirm that they are receiving the events on their end. 
In that case, you need to provide the IRUS staff with the following information:
- The URL of your repository
- The timeframe during which the stats events were sent
- The tracker URL

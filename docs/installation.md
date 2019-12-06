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

## 5. Rebuild and redeploy

After the patch has been applied, the repository will need to be rebuilt.
DSpace repositories are typically built using Maven and deployed using Ant.

Please use `mvn clean package` instead of `mvn package` to avoid errors in the user interface. If `clean` is not specified some classes might not be updated correctly.

## 6. Restart Tomcat

After the repository has been rebuilt and redeployed, Tomcat will need to be restarted to bring the changes live.

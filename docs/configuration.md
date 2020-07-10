# Configuration overview

## Configuration parameters

A new `stats.cfg` file will be created in `[dspace-src]/dspace/config/modules`.

!> All configurations in DSpace 6.x are prefixed with `stats.`, e.g. `stats.tracker.environment`.

| Property | Description | Default 
| -------- | ----------- | -------|
|`tracker.enabled`| Configuration to allow for the patch to be disabled, while still being installed on the codebase (So no reverts need to be applied) |All|
|`tracker.type-field`| Metadata field to check if certain items should be excluded from tracking. If empty or commented out, all items are tracked. |All|
|`tracker.type-value`| The values in the above metadata field that will be considered to be tracked.|All|
|`tracker.environment`| The tracker environment determines to which url the statistics are exported.| test|
|`tracker.testurl`| The url to which the trackings are exported when testing.|&nbsp;|
|`tracker.produrl`| The url to which the trackings are exported in production.|&nbsp;|
|`tracker.urlversion`| Tracker version|&nbsp;|
|`dspace.type`| The UI utilized by DSpace.| xmlui|
|`spider.ipmatch.enabled`| Boolean whether to ignore spider trackings; uses the lists in `/config/spiders`| true|
|`spiders.agentempty.enabled`| Boolean whether to ignore trackings originating from requests having an empty `User-Agent` header (most likely not a user browser).|false |
|`spiders.agentregex.enabled`| Boolean whether `User-Agent` should be matched against a known lists of user agents (provided by an online service; downloaded during DSpace build).| true|
|`spiders.agentregex.regexfile`| Location where the user agents file should be downloaded to.|&nbsp;|

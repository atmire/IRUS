# Functionality overview

## Bitstream tracking

The patch will automatically send bitstream download hits and item views to the IRUS-UK service. There's no manual action required.

## Retry failed trackings

If the IRUS-UK tracker is down or some other kind of error should occur preventing DSpace from committing to the tracker, the record is stored in the database in a separate table (`OpenUrlTracker`) that is being created automatically during deployment. Committing these entries can be tried again using the following command.

```bash
[deployed-dspace]/bin/dspace retry-tracker
```

This will iterate over all the logged entries and retry committing them. If they fail again, they remain in the table, if they succeed, they are removed.

It is strongly advised to schedule this script to be executed daily or weekly (preferable at low load-times during the night or weekend). If there are no failed entries, the script will not perform any actions and exit immediately.

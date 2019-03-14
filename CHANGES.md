# CHANGES

The releases are tagged with the date of the release. Currently, there are no version numbers.

## Unreleased

Unreleased changes can be found on the stable_4x, stable_5x and stable_6x branches

- Breaking change: ...
- Fix: ...
- improvement: ...
- Validation: ...
- Testing improvements: ...

## March 14th, 2019 (COUNTER R5 update: DSpace 5 and DSpace 6 patch versions only)
- Improvement: The new COUNTER release (R5) allows for reports that include 'Investigations' (item page views) and 'Requests' (file downloads). 
This patch expands on the previously existing functionality and updates the sent information accordingly.
- Improvement: Removal of svc_session parameter since other software isn't able to provide this info.
- Fix: Removal of 'urn:ip' prefix from the req_id parameter (Client IP address) since it has become irrelevant.
- Fix: The logic for item type exclusion did not work when multiple types were present. 

## January 24th, 2019 (DSpace 6 patch version only)
- Fix: added the "stats" prefix to the regexfile configuration property in stats.cfg.

## March 12th, 2018

- Fix: Applied synchronization to the loading of the spiders lists to prevent lists from being loaded multiple times.
- Fix: Added missing character to the oracle SQL script for IRUS.
- Fix: Resolved issue that prevented the retry-tracker command from being run from the command line. 
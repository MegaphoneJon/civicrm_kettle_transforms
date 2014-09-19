civicrm_kettle_transforms
=========================

Generic transformations which are helpful for migrating data into CiviCRM using Pentaho Data Integration (Kettle)

h2. Setup

Most of these transforms are implemented as subtransformations.  For best results, you should be comfortable using the mapping steps.

Most of these transforms do lookups against your target CiviCRM database.  Ideally you have a generic database connection which you can set via variables.  Currently, I use the following variables, which I set in the parent transformation.
Database name: ${CIVIDB}
Database user: ${MYSQLUSER}
Database password: ${MYSQLPW}

TODO: Write documentation of how my directories are set up.  For instance, good data is always dropped into a "data" subfolder, whereas invalid data is dropped into an "errors" subfolder.

TODO: Create a "samples" directory that demonstrates a good setup for each of these

h2. Transformations

h3. prefix_suffix_remapping.ktr

This transformation will:
* clean up obvious problems (e.g. "mr" to "Mr.").
* remap prefixes to prefix_ids (and suffixes to suffix_ids)
* output a list of prefixes unknown to your current CiviCRM database

h3. create_prefix_or_suffix

This transformation will take invalid prefixes and/or suffixes and create them, live, in CiviCRM via the REST API.

h3. website_cleanup

Given a contact_id, a url, and a website_type, will return website fields ready for API import.

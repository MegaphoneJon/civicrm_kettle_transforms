civicrm_kettle_transforms
=========================

Generic transformations which are helpful for migrating data into CiviCRM using Pentaho Data Integration (Kettle)

## Setup

Most of these transforms are implemented as subtransformations.  If you're not comfortable with subtransform mappings, you can copy/paste the contents of a transform into your own file.

Most of these transforms do lookups against your target CiviCRM database.  Ideally you have a generic database connection which you can set via variables.  Currently, I use the following variables, which I set in a "Settings" job (see below):
* Database name: ${CIVIDB}
* Database user: ${MYSQLUSER}
* Database password: ${MYSQLPW}

## Configuration

Many transforms assume a particular directory structure, which can be found in the `example` directory.  Copy this directory to an appropriate location outside of this repo.

Many of these transforms depend on variables being set.  In your work directory, open "Example Settings.kjb" and set the variables appropriately.

#### Variables
* **LOCALFILES**: Path to the directory structure containing this file.
* **DEVSERVER**: The hostname of your dev server.  Will usually be localhost.
* **DEVCMSROOT**: The root folder of your dev site's CMS.
* **SQLDUMP**: A path to a folder where SQL dumps of your database will be saved/loaded from.
* **IMPORTSCRIPT**: A path to `import.php` in your CiviCRM install.
* **EXPORTSCRIPT**: A path to `export.php` in your CiviCRM install.
* **DRUPALDB**: The name of your CMS database (not just Drupal).
* **CIVIDB**: The name of your CRM database.
* **MYSQLUSER**: The MySQL user on the dev site that can dump/load copies of the DB.
* **MYSQLPW**: The password for MYSQLUSER.
* **TRANSFORMREPO**: The path to the local copy of this repo.
* **DEVSSHUSER**: The SSH user on the dev site. Even if you use your localhost as a dev server, these transforms use SSH to execute commands.
* **KEYFILE**: The path to a PEM of your SSH key for DEVSSHUSER (and TESTSSHUSER below, if applicable).
* **SCHEMANAME**: The Postgres schema of your source data - only used if you're using Postgres.

The following are necessary to use any API-related transforms:
* **SITEURL**: The dev site's URL. Often (not always) will match DEVSERVER above.
* **SITEUSER**: A username with admin access to the site.
* **SITEPASSWORD**: The password for SITEUSER.
* **SITEKEY**: The site key.  Can be found in `civicrm.settings.php`.
* **APIKEY**: The API key for SITEUSER.
* **RESTPATH**: The path to the REST endpoint (`rest.php`) in CiviCRM.

The following are only necessary if you want to use the "Sync dev site to test site" transform.
* **TESTSERVER**: The hostname of your test server.
* **TESTSSHUSER**: The SSH user on the dev site.
* **TESTCMSROOT**: The root folder of your test site's CMS.
* **TESTSQLDUMP**: A path to a folder on the test server where SQL dumps of your database will be copied to/loaded from.
* **TESTMYSQLUSER**: The MySQL user on the test site that can load copies of the DB.
* **TESTMYSQLPW**: The password for TESTMYSQLUSER.

#### Directory structure
* Place any source data in `raw data`, and save your transforms in `transforms`.
* "data" is where Kettle outputs the CSVs intended for import.  
* "errors" is where Kettle outputs CSVs that don't pass internal validation (e.g. malformed emails).  I often share this folder with clients so they can fix their source data.
* "importlogs" stores the output of the CSV import tool.  The output is also automatically parsed for errors, and a second file correlates the non-imported records with the offending row.  So e.g. you'll see "raw_log_email.csv" and "noimport_email.csv" in here.

## Transforms
* **generic** contains a number of reusable transforms, many of which are described in detail below.
**TODO**: Move generic transforms from the root directory to the generic directory.
* The **salsa**, **raisers_edge**, **contact_contact** and **donorperfect** folders contain transforms specific to those tools.

Descriptions of some of the generic transforms follow:

##### prefix_suffix_remapping.ktr

This transformation will:
* clean up obvious problems (e.g. "mr" to "Mr.").
* remap prefixes to prefix_ids (and suffixes to suffix_ids)
* output a list of prefixes unknown to your current CiviCRM database

##### create_prefix_or_suffix

This transformation will take invalid prefixes and/or suffixes and create them, live, in CiviCRM via the REST API.

##### website_cleanup

Given a contact_id, a url, and a website_type, will return website fields ready for API import.

##### step_1_individual_cleanup

required fields: first_name, last_name, e_mail

Trims all fields, separates out contacts who lack a first_name, also contacts who lack a last_name AND email.

##### email_cleanup

required fields: email, location_type (make a blank one if you don't have one)

Fixes common e-mail problems (e.g. jon@aol, jon@gmail,com), validates for invalid characters/format, remaps common email location types, and does a location_type_id lookup.

##### email_wrapper

Calls email_cleanup; also does a lookup of name for invalid e-mail addresses, and requires a contact_id.

##### phone_cleanup

Remaps common phone and location types, removes invalid phone #s (just those over 32 chars for now), phone_type and location_type lookup, lookup of name for invalid phones.

##### address_cleanup

Looks up state_id, country_id, location_type_id; fixes obvious problems with typoed states/countries; remaps obviously Canadian provinces to Canada; filters out invalid countries, states/provinces, location types, and too-long postal codes.

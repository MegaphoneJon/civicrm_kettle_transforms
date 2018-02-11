civicrm_kettle_transforms
=========================

Generic transformations which are helpful for migrating data into CiviCRM using Pentaho Data Integration (Kettle)

## Setup

Most of these transforms are implemented as subtransformations.  For best results, you should be comfortable using the mapping steps.

Most of these transforms do lookups against your target CiviCRM database.  Ideally you have a generic database connection which you can set via variables.  Currently, I use the following variables, which I set in the parent transformation.
Database name: ${CIVIDB}
Database user: ${MYSQLUSER}
Database password: ${MYSQLPW}

## Transformations

### prefix_suffix_remapping.ktr

This transformation will:
* clean up obvious problems (e.g. "mr" to "Mr.").
* remap prefixes to prefix_ids (and suffixes to suffix_ids)
* output a list of prefixes unknown to your current CiviCRM database

### create_prefix_or_suffix

This transformation will take invalid prefixes and/or suffixes and create them, live, in CiviCRM via the REST API.

### website_cleanup

Given a contact_id, a url, and a website_type, will return website fields ready for API import.

### step_1_individual_cleanup

required fields: first_name, last_name, e_mail

Trims all fields, separates out contacts who lack a first_name, also contacts who lack a last_name AND email.

### email_cleanup

required fields: email, location_type (make a blank one if you don't have one)

Fixes common e-mail problems (e.g. jon@aol, jon@gmail,com), validates for invalid characters/format, remaps common email location types, and does a location_type_id lookup.

### email_wrapper

Calls email_cleanup; also does a lookup of name for invalid e-mail addresses, and requires a contact_id.

### phone_cleanup

Remaps common phone and location types, removes invalid phone #s (just those over 32 chars for now), phone_type and location_type lookup, lookup of name for invalid phones.

### address_cleanup

Looks up state_id, country_id, location_type_id; fixes obvious problems with typoed states/countries; remaps obviously Canadian provinces to Canada; filters out invalid countries, states/provinces, location types, and too-long postal codes.

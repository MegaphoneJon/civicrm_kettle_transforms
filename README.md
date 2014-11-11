civicrm_kettle_transforms
=========================

Generic transformations which are helpful for migrating data into CiviCRM using Pentaho Data Integration (Kettle)

h2. Setup

Most of these transforms are implemented as subtransformations.  For best results, you should be comfortable using the mapping steps.

Most of these transforms do lookups against your target CiviCRM database.  Ideally you have a generic database connection which you can set via variables.  Currently, I use the following variables, which I set in the parent transformation.
Database name: ${CIVIDB}
Database user: ${MYSQLUSER}
Database password: ${MYSQLPW}

h2. "cleanup" vs. "wrapper" transforms

Some entities have both a "cleanup" and a "wrapper" transform.  "cleanup" transforms take the bare minimum amount of data needed to ensure the data is valid.  "wrapper" transforms are designed for outputting data ready to import via API (CSV or REST), and presume that you're adding the data to an existing contact with a valid contact_id.

Where only a "cleanup" transform exists, the transform is for both cleanup and wrapper.  I'll endeavor to break those into two transforms over time.

_Example_: "email_cleanup" requires only an email and location_type field, and validates/does lookups on both.  "email_wrapper" calls "email_cleanup", but also requires a contact_id.

TODO: Write documentation of how my directories are set up.  For instance, good data is always dropped into a "data" subfolder, whereas invalid data is dropped into an "errors" subfolder.

TODO: Create a "samples" directory that demonstrates a good setup for each of these

TODO: Refactor into "cleanup" and "wrapper" functions for more transforms.

TODO: Refactor website_cleanup to have two outputs.

TODO: Document Constant Contact transforms.

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

h3. step_1_individual_cleanup

required fields: first_name, last_name, e_mail

Trims all fields, separates out contacts who lack a first_name, also contacts who lack a last_name AND email.

h3. email_cleanup

required fields: email, location_type (make a blank one if you don't have one)

Fixes common e-mail problems (e.g. jon@aol, jon@gmail,com), validates for invalid characters/format, remaps common email location types, and does a location_type_id lookup.

h3. email_wrapper

Calls email_cleanup; also does a lookup of name for invalid e-mail addresses, and requires a contact_id.

h3. phone_cleanup

Remaps common phone and location types, removes invalid phone #s (just those over 32 chars for now), phone_type and location_type lookup, lookup of name for invalid phones.

h3. address_cleanup

Looks up state_id, country_id, location_type_id; fixes obvious problems with typoed states/countries; remaps obviously Canadian provinces to Canada; filters out invalid countries, states/provinces, location types, and too-long postal codes.

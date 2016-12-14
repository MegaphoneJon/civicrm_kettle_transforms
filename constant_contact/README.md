Exporting for Constant Contact:
For screenshots, "see here":http://support2.constantcontact.com/articles/FAQ/1694.

* Log into Constant Contact.
* Click "Contacts"
* (Optionally) Click "View All Contacts".  You can also just export "Active".
* Click the "Select All" checkbox to select all names.
* Click "Export".
* Select ALL fields.  This transformation assumes no custom fields will be migrated into Civi.

This set of transforms assumes a "Fill" strategy for most contact data.  Since CoCo is often a secondary data source, we generally want to match existing records based on email address, and not import data that already exists in CiviCRM.

To that end, this transform matches on email address, and won't import a phone/email/address that matches one already in the system.

TODO:
* Break the "Constant Contact" transform into multiple transforms; it's gotten pretty hairy.
* Implement more "required fields" using the data grid/cartesian join strategy.  
* Use the same strategy on the step1 transform to capture more email types.
* Only match the first 8 characters of a street address to determine a duplicate.

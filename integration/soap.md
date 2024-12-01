# SOAP API

`WSDL` `XMl` and is synchronous

Primarily used for

Best for small number of records.
This API is used when there are less than `10,000` records

- initially loading data from Salesforce org for a new mapping
- reloading data in an existing mapping
- reading changes from Salesforce org
- `create`, `update`, `retrieve`, `delete` can also `search`
- counting records
- query for mapped fields
- used when **writing to Salesforce**

> SOAP API calls don't count towards [API request limits](https://developer.salesforce.com/docs/atlas.en-us.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm). (counted for your org though)

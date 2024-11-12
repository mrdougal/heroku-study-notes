# Private spaces networking

Establishing "trusted connection" between Salesforce and Private space

## Salesforce => Heroku app

Eg for providing an http API for a custom Apex/Lightning component. If app isn't for public block public access

- Allow salesforce traffic via CIDR range
  - although will allow access for any salesforce org
    - limiting access via ip may not work due to "site switching" (geo-graphical separate locations) and site maintenance

## Heroku app => Salesforce

Salesforce permits login from anywhere on internet

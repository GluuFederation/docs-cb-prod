# Default Couchbase Architecture

## Overview

The Gluu Server setup script configures the basic Couchbase architecture for you. This document covers the details of that deployment, as well as procedures to keep Gluu and Couchbase running smoothly together. <!-- This will obviously be better when finalized -->

## System Architecture

## Data Structure

### Buckets

Depending on your storage locations (if you choose hybrid backend), the following buckets will be created:

#### gluu
This is default bucket for gluu server, the following documents will be imported to this bucket:
  - gluu base settings, oxauth, oxrust and configurations of other services
  - scopes
  - scripts
  - scim/saml/passport/radius related documents
  - clients
  - metric (statistic) documents will be inserted to this bucket 

#### gluu_cache

#### gluu_site
If you use Cache Refresh, data from remote database will be imported to this bucket

#### gluu_token

#### gluu_user
Users and groups will go to this bucket, initially admin user and admin group will be created.


### Indexes

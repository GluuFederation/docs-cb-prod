# Default Couchbase Architecture

## Overview

The Gluu Server setup script configures the basic Couchbase architecture for you. This document covers the details of that deployment, as well as procedures to keep Gluu and Couchbase running smoothly together.

## System Architecture

## Data Structure

### Buckets

By default, the following buckets will be created. If using a hybrid backend, buckets that are unused because their data is stored in LDAP will not be created.

#### gluu
This is the default bucket for the Gluu Server, the following documents will be imported to this bucket:
  - Gluu base settings, oxAuth, oxTrust and configurations for other services
  - scopes
  - scripts
  - SCIM/SAML/Passport/RADIUS related documents
  - clients
  - metric (statistic) documents will be inserted to this bucket 

#### gluu_cache

#### gluu_site
If you use Cache Refresh, data from the remote database will be imported to this bucket

#### gluu_token

#### gluu_user
Users and groups will go to this bucket, initially admin user and admin group will be created.

### Indexes

Indexes are created according to this JSON: [index.json](https://github.com/GluuFederation/community-edition-setup/blob/master/static/couchbase/index.json)

`setup.py` creates two types of indexes in buckets:

#### Indexes for attributes

Gluu services uses attributes to find the intended documents, so `setup.py` creates indexes for certain attributes. For example, for the **gluu_user** bucket, the following index is created for the **inum** attribute:

```
CREATE INDEX gluu_user_inum ON `gluu_user`(`inum`)
```

The index name is formed by joining the bucket name and attribute name with an underscore. (e.g. `gluu_user_inum`)

#### Static Indexes
For faster retrieval of documents, `setup.py` also creates static indexes if necessary. For example, we have this entry in the **gluu** bucket:

```
 [["oxApplicationType", "oxMetricType", "oxStartDate", "oxEndDate"], "objectClass = \"oxMetric\""]
```

For this entry the following index is created:

```
CREATE INDEX gluu_st_1 ON `gluu`(`oxApplicationType`, `oxMetricType`, `oxStartDate`, `oxEndDate`) WHERE objectClass="oxMetric"
```

The index name follows this convention: `<bucket_name>_st_#` where `#` is a consecutive number

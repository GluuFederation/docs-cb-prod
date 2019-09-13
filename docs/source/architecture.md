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

Indexes are created according to this json: [index.json](https://github.com/GluuFederation/community-edition-setup/blob/master/static/couchbase/index.json)

`setup.py` creates two types of indexes on buckets:

#### Indexes for attrbiutes
Gluu services uses attrbiutes to find intended document, hence `setup.py` creates indexes for certain attrbiutes. For example:
for bucket **gluu_user**, the following index is created for attribute **inum**:

```
CREATE INDEX gluu_user_inum ON `gluu_user`(`inum`)
```

Index name is formed by joining bucket name and attrubute name by underscore (`_`)

#### Static Indexes
For faster retreival of documents, `setup.py` also creates static indexes if necassary. For buket **gluu** we have such an entry in `index.json`:

```
 [["oxApplicationType", "oxMetricType", "oxStartDate", "oxEndDate"], "objectClass = \"oxMetric\""]
```

For this entry the following index is created:

```
CREATE INDEX gluu_st_1 ON `gluu`(`oxApplicationType`, `oxMetricType`, `oxStartDate`, `oxEndDate`) WHERE objectClass="oxMetric"
```

Index name is created as `<bucket_name>_st_#` wher `#` is consecutive number

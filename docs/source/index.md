# Installing Couchbase
## Overview

In Gluu 4.0, Couchbase Enterprise Edition (EE) can be used as a database backend. 

## Installation

There are two supported deployment strategies: 

- [Local Couchbase installation](#local-installation), where both Gluu and Couchbase are residing on the same servers. OK for basic testing. 

- [Remote Couchbase installation](#remote-installation), where Couchbase is on its own dedicated servers. Recommended for performance testing and production scenarios. 

### Local installation

To install a Gluu Server with a local instance of Couchbase: 

 - Prepare a VM with 16GB of RAM, 4 CPU cores, and 40 GB of disk space. 
 - Install the [Gluu Server 4.0](https://gluu.org/docs/ce/4.0/installation-guide/install/) packages, don't run `setup.py` yet. 
 - Start the `gluu-server` service and log in to the Gluu Server chroot
 - Download your preferred [Couchbase package](https://www.couchbase.com/downloads) and save it inside your Gluu Server chroot to `/opt/dist/couchbase`.
 - Run `setup.py` to configure the Gluu Server.
 - When prompted to choose a backend, you'll see the options below. Select Option 2 to use Couchbase:
 
    ```
    Install (1) Gluu OpenDj (2) Couchbase (3) Hybrid [1|2|3]
    ```

### Remote installation

To set up the Gluu Server to leverage a remote Couchbase Server, follow these instructions. 

#### Install and configure Couchbase

- Prepare a VM with 16GB physical memory and 4 core CPU 
- [Install Couchbase](https://docs.couchbase.com/server/current/install/get-started.html)
- Modify the VM firewalls according to [Couchbase requirements](https://docs.couchbase.com/server/current/install/install-ports.html)
- Configure your Couchbase deployment. Here are couple of screenshots attached from our test setup. 

    ![image](./img/CB_remote_one.PNG)

    ![image](./img/CB_remote_two.PNG)

#### Install and configure Gluu

- Install [Gluu Server 4.0](https://gluu.org/docs/ce/4.0/installation-guide/install/), but **do not** run `setup.py` yet. 
- After package installation, run these commands: 
   
    ```tab="Ubuntu 18, RHEL 7, Debian 9, or CentOS 7"
    /sbin/gluu-serverd enable

    /sbin/gluu-serverd start
   
    /sbin/gluu-serverd login
   
    cd /install/community-edition-setup/
   
    python setup.py --remote-couchbase
    ```
    
    ```tab="Ubuntu 16"
    service gluu-server start
    
    service gluu-server login
    
    cd /install/community-edition-setup/
    
    ./setup.py --remote-couchbase
    ```
    
- The setup script will add some Couchbase-specific fields to the normal [setup script prompts](https://gluu.org/docs/ce/4.0/installation-guide/setup_py/#setup-prompt):

    - **Couchbase host**: The IP address previously set for Couchbase 
    - **Couchbase Admin user**: The Admin username to log in to Couchbase
    - **Couchbase Admin password**: The password to log in to Couchbase - **this is also set to be the oxTrust admin password**
    - **Use hybrid backends**: Set this to `yes` to install a local OpenDJ backend, enabling a hybrid configuration
    
### Test

- Test WebUI login
- A successful Couchbase bucket will look like this: 
 
    ![image](./img/CB_remote_successful_bucket.PNG)
 
- Check if all Gluu Server bits are running properly or not ( identity/oxauth/idp/httpd etc. ) 
- Check logs for any error. 

## Hybrid backend

If both Couchbase and OpenDJ are available (either locally or remote), you will be asked if you want to use hybrid backends:

```
Install (1) Gluu OpenDj (2) Couchbase (3) Hybrid [1|2|3] [1] : 3
  Please note that you have to update your firewall configuration to
  allow connections to the following ports:
  4369, 28091 to 28094, 9100 to 9105, 9998, 9999, 11207, 11209 to 11211,
  11214, 11215, 18091 to 18093, and from 21100 to 21299.
By using this software you agree to the End User License Agreement.
See /opt/couchbase/LICENSE.txt.
Use Gluu OpenDj to store (1) default (2) user (3) cache (4) statistic (5) site : 14
```

In this example, both OpenDJ and Couchbase will be used for storing specific data. Default storage will be persisted in OpenDJ, e.g. system configurations, attributes, clients, metrics, etc., while all other data will be stored in Couchbase.

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

The index name is "`<bucket_name>_st_#`" where `#` is a consecutive number.

## License

Couchbase Enterprise Edition, and the corresponding integration with Gluu, is commercially licensed software. To discuss licensing, please [schedule a call with us](https://gluu.org/booking). 

# Installing Couchbase

## Local Couchbase Installation

Starting in CE 4.0, Gluu Server supports Couchbase Server as a database backend. To install with Couchbase, you need to download the OS-specific Couchbase package from https://www.couchbase.com/downloads (Enterprise version only), and save to `/opt/dist/couchbase`.

## Remote Couchbase Installation

These instructions currently use CentOS7 VMs for Gluu Server 4.0 and Couchbase.  

### Couchbase installation and configuration

- Prepare your VM with: 
  - 16GB physical memory
  - 4 core CPU 
- Install Couchbase 
- Modify firewalls according to Couchbase requirements: https://docs.couchbase.com/server/current/install/install-ports.html
- Configure your Couchbase deployment. Here are couple of screenshots attached from our test setup. 
    ![image](../img/4.0/Remote_CB/CB_remote_one.PNG)
    ![image](../img/4.0/Remote_CB/CB_remote_two.PNG)

### Gluu Server installation and configuration

- Grab Gluu Server 4.0 RC 
- Install rpm, don't run 'setup.py' yet. 
- After completion of rpm installation: 
  - `/sbin/gluu-serverd-4.0 enable`
  - `/sbin/gluu-serverd-4.0 start`
  - `/sbin/gluu-serverd-4.0 login`
  - `cd /install/community-edition-setup/`
  - `wget https://raw.githubusercontent.com/GluuFederation/community-edition-setup/master/setup.py -O /install/community-edition-setup/setup.py`
  - `python setup.py --remote-couchbase`
     - Setup.py script will ask couple of questions, along with hostname ( we used IP address ), Administrative username ( we used 'admin' ) and password for that administrative user. 
     - Complete installation. 

### Test

- Test WebUI login
- A successful Couchbase bucket will look like below: 
 
    ![image](../img/4.0/Remote_CB/CB_remote_successful_bucket.PNG)
 
- Check if all Gluu Server bits are running properly or not ( identity/oxauth/idp/httpd etc. ) 
- Check logs for any error. 

## Hybrid Couchbase / OpenDJ Installation

If both Couchbase and OpenDJ (either locally or remote) are available, you will be asked if you want to use hybrid backends:

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

In this example, both OpenDJ and Couchbase will be used for storing data. Default storage (system configurations, attributes, clients, etc.) will be OpenDJ and also metric data (statistic) will be stored in OpenDJ. Other data will be stored in Couchbase server.

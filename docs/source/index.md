# Installing Couchbase
## Overview

In Gluu 4.0, Couchbase Enterprise Edition (EE) can be used as a database backend. 

## Installation

There are two supported deployment strategies: 

- Remote Couchbase installation, where Couchbase is on its own dedicated server(s). This is the recommended strategy for production scenarios. 

- Local Couchbase installation, where both Gluu and Couchbase are residing on the same server(s).


### Local installation

To install a Gluu Server with a local instance of Couchbase, download your preferred [Couchbase package](https://www.couchbase.com/downloads) and save it inside your Gluu Server chroot to `/opt/dist/couchbase`.

### Remote installation

To setup your Gluu Server to leverage a remote Couchbase Server, following the instructions below. 

!!! Note
    These instructions assume both Gluu and Couchbase are installed on VMs running CentOS 7.x. 

#### Install and configure Couchbase

- Prepare a VM with 16GB physical memory and 4 core CPU 
- [Install Couchbase](https://www.couchbase.com/downloads)
- Modify firewalls according to [Couchbase requirements](https://docs.couchbase.com/server/current/install/install-ports.html)
- Configure your Couchbase deployment. Here are couple of screenshots attached from our test setup. 

    ![image](./img/CB_remote_one.PNG)

    ![image](./img/CB_remote_two.PNG)

#### Install and configure Gluu

- Install [Gluu Server 4.0](https://gluu.org/docs/ce/4.0/installation-guide/install/), but **do not** run `setup.py` yet. 
- After package installation, run these commands: 
    ```
    /sbin/gluu-serverd enable
    ```
    
    ```
    /sbin/gluu-serverd start
    ```
    
    ```
    /sbin/gluu-serverd login
    ```
    
    ```
    cd /install/community-edition-setup/
    ```
    
    ```
    python setup.py --remote-couchbase
    ```
    
- The setup script will ask some questions, along with hostname ( we used IP address ), Administrative username (we used `admin`) and password for that administrative user. 
- Complete installation. 

### Test

- Test WebUI login
- A successful Couchbase bucket will look like below: 
 
    ![image](./img/CB_remote_successful_bucket.PNG)
 
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


## License

Couchbase Enterprise Edition, and the corresponding integration with Gluu, is commercially licensed software. To discuss licensing, please [schedule a call with us](https://gluu.org/booking). 

*** If you add something to this repo, document it below ***

es-cli.sh
---------
Search server logs from the comfort of your terminal!

This is a command-line wrapper for Elasticsearch's RESTful API.
This is super-beta, version .000001-alpha. Questions/comments/hatemail to Kale Stedman,
I'm so sorry. You should probably pipe the output to less.

    usage: ./es-cli.sh -u $USER -p $PASS -h es-hostname -q "$query" -t $time -n 500
    ex: ./es-cli.sh -u kstedman -p hunter2 -h es.hostname.com -q "program:crond" -t 5 -n 50
    
    -h host      The Elasticsearch host you're trying to connect to.
    -u username  Optional: If your ES cluster is proxied through apache and you have http auth enabled, username goes here
    -p password  Optional: If your ES cluster is proxied through apache and you have http auth enabled, password goes here
    -q query     Optional: Query to pass to ES. If not given, "*" will be used.
    -t timeframe Optional: How far back to search. Value is in mimutes. If not given, defaults to 5.
    -n results   Optional: Number of results to return. If not given, defaults to 500.

[Shamelessly stolen from http://tech.superhappykittymeow.com/?p=356]


pccommon.sh
-----------

$ source ./pccommon.sh 

Provides the following functions for private cloud admins:

  - ix() - Quickly post things to ix.io
    * echo things into this and it will return a URL for sharing

  - rpc-hypervisor-vms() - Display all hypervisors and associated instances
    * Pulls information from MySQL, shows a list of VMs along with their associated resources

  - rpc-hypervisor-free() - Display free resources on each Hypervisor
    * Pulls information from MySQL, displays free resources per Hypervisor

  - rpc-filter() - Replace stinky UUIDs with refreshing descriptive names inline
    * echo things into this and it will replace any known UUIDs with descriptive names for the following items:
        * Networks
        * Flavors
        * Instances
        * Images
        * Users
        * Tenants

  - rpc-iscsi-generate-sessions() - Generate list of commands to re-initiate currently open iscsi sessions
    * Will create a list of iscsiadm commands based on the currently-mounted iscsi targets that will re-create all current mounts.

  - rpc-common-errors-scan() - Pretty much what it sounds like
    * This is a piece of shit and probably should just be removed

  - rpc-bondflip() - Change given bondX to backup NIC
    * Will flip a given bond interface; you don't have to know which interfaces are in the bond.

  - rpc-environment-scan() - Update list of internal filters
    * Updates the list of filters used by rpc-filter(), run automatically on load.

  - rpc-os-version-check() - Are we running latest availble version?
    * Checks apt-cache policy to see if we are running latest version of nova-common.  Only for v4 environments.

  - rpc-instance-test-networking() - Test instance networking.
    * Same checks performed by rpc-instance-per-* functions.  Can be used on arbitrary instances.

  - rpc-instance-per-network() - Per network, spin up an instance on given hypervisor, ping, and tear down
    * Spins up an instance per network on the given hypervisor, waits for them to boot, then runs rpc-instance-test-networking() on them

  - rpc-instance-per-network-per-hypervisor() - Per network, spin up an instance on each hypervisor, ping, and tear down
    * Spins up an instance on each hypervisor, per network, waits for them to boot, then runs rpc-instance-test-networking() on them.

  - rpc-sg-rules() - Makes security groups easier to read.  Pass it a Security Group ID (not name)
    * Pulls from MySQL

  - rpc-image-check() - Shows all running instances and the state of their base images (active/deleted)
    * Checks to make sure all running instances still have a backing image in glance

  - rpc-update-pccommon() - Grabs the latest version of pccommon.sh if there is one
    * Self explanitory

  - swap-usage() - Shows current usage of swap memory, by process
    * Yep, this one, too.


On load, this script will scan the environment for various openstack things in order to pre-populate the rpc 
filters.  This process takes a few seconds to complete, and can be skipped by setting S=1 in your environment:

    $ S=1 source ./pccommon.sh

** the rpc-sg-rules() function will not work if you skip the scan **

You can also suppress all messages entirely by adding Q=1

    $ Q=1 source ./pccommon.sh

They can be used in combination.  Both of these values are automatically assumed if the script detects that 
you are running via remote execution (eg, $ ssh 10.240.0.200 '. ./pccommon.sh; rpc-bondflip bond1')



rpchousecall.py
---------------

$ python ./rpchousecall.py

Will perform basic healthcheck of RPC environment:
  - Authenticate against all applicable endpoints
  - Import known good Ubuntu Image, download from Ubuntu, if necessary
  - Create a flavor
  - Create a cinder volume, if applicable
  - Create a network and subnet
  - Boot an instance using new flavor and image on new network
  - Ping the instance
  - Attach cinder volume, if applicable
  - Create snapshot
  - Detach cinder volume, if applicable
  - Destroy instance
  - Destroy network and subnet
  - Destroy snapshot
  - Destroy flavor
  - Destroy cinder volume
  - Test MySQL Replication
  - Verify Horizon Login Page


shelob.sh
---------

$ shelob.sh -v VLAN -i NIC -s SOURCEIP -d DESTIP -l LISTFILE

    SRCIP and DESTIP are optional.  They have defaults which can be overridden.

    - LISTFILE should contain IP addresses or hostnames of remote systems to test.
    - NIC is pretty self-explanatory.

    This script will configure a local VLAN-tagged interface, then connect remotely
    to each system listed in LISTFILE, configure another tagged interface, then ping
    across in order to definitively test connectivity.

    Script attempts to detect duplicate IP addresses so it doesn't stomp on others.

    It would be a REALLY good idea to set up SSH keys for root logins before running
      this script.


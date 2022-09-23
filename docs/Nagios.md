# Traditional monitoring: Nagios, et al

* In short: 
    * A backend is responsible for: 
        * Polling everything it's configured to poll (via SSH or NRPE)
        * Listening for incoming "passive" events (we don't use this, re: security)
        * Knowing the current state of all services; logging it to a file
        * Serving API requests from the Front-End
        * At startup: 
            * grok configuration tree
            * load last-known-state from log file
        * Performing notifications, as configured, at state-change
    * A Front-end is responsible for:
        * Human interaction!
        * Making it look pretty
        * Surfacing events - current status
        * Reviewing the log file
    * Pro: 
        * It's very reliable and stable
        * A decade of service, virtually unblemished by errors
        * We have institutional knowledge on how to run it (investment)
    * Con: 
        * GUI is antique PHP
        * Can't change config thru the GUI
        * No API for changing config
        * Limited to polling once-a-minute, at best
        * We typically configure it not to alert us, unless a service is in trouble for 15 minutes, keeping false-positives to a minimum
* It's generally implemented as a VM (but [container images exist](https://hub.docker.com/r/manios/nagios#!))
* /etc/nagios/ is your friend - as simple or complex as you like

```
[root@ayr ~]# tree -d /etc/nagios
/etc/nagios
├── objects
│   ├── firewalls
│   ├── switches
│   ├── unix
│   │   ├── blacklight
│   │   ├── CI
│   │   ├── CR944
│   │   ├── di_uat
│   │   ├── dmp
│   │   ├── ezproxy
│   │   ├── fedora
│   │   ├── groups
│   │   ├── haproxy
│   │   ├── iipimage
│   │   ├── ipmi
│   │   ├── jupiter
│   │   ├── kernelVersion
│   │   ├── kvm
│   │   ├── lockss
│   │   ├── mariadb
│   │   ├── nagios
│   │   ├── NEOSDiscovery
│   │   ├── nfs
│   │   ├── openstack
│   │   ├── postgres
│   │   ├── solrcloud
│   │   ├── web_servers
│   │   └── zookeeper
│   └── windows
├── perlExtract
├── plugins
└── private
```

* All configuration is done by finger-smashing in vim; copypasta & change the hostname. This is the minimum configuration for a single host:

```
define host{
        use             linux-server                            ; Inherit default values from a template
        host_name       era-app-prd-1                           ; The name we're giving to this host
        alias           era-app-prd-1.library.ualberta.ca       ; A longer name associated with the host
        hostgroups      os_root,os_boot,Linux-Servers-swap,load_avg,kernelVersion7,RPM-Packages, FW
        display_name    Jupiter2 prod
        check_command           check-host-alive
        max_check_attempts      20
        contact_groups          admins
        notification_interval   15
        notification_period     24x7
        notification_options    d,u,r
        notes_url       http://www.library.ualberta.ca/
        notes           era-app-prd-1 is an application server for Jupiter
        icon_image      vendors/centos.png
        icon_image_alt  CentOS 7.x
        statusmap_image vendors/centos.gd2
}
```
* and that gets you ![Nagios Screenshot](era-app-prd-1.png)
* "Plugins" are the road (where events are detected) [http://nagios-plugins.org/](http://nagios-plugins.org/). These need to be installed on clients.  On CentOS, there's an RPM  meta-package that install "everything"... 

```
[root@ayr jupiter]# rpm -qa | grep nagios | sort
nagios-4.4.6-4.el7.x86_64
nagios-common-4.4.6-4.el7.x86_64
nagios-contrib-4.4.6-4.el7.x86_64
nagiosgraph-1.5.2-1.el7.centos.noarch
nagios-plugins-2.3.3-2.el7.x86_64
nagios-plugins-all-2.3.3-2.el7.x86_64
nagios-plugins-bonding-1.4-3.el7.x86_64
nagios-plugins-breeze-2.3.3-2.el7.x86_64
nagios-plugins-by_ssh-2.3.3-2.el7.x86_64
nagios-plugins-check_raid-4.0.6-0.x86_64
nagios-plugins-cluster-2.3.3-2.el7.x86_64
nagios-plugins-dhcp-2.3.3-2.el7.x86_64
nagios-plugins-dig-2.3.3-2.el7.x86_64
nagios-plugins-disk-2.3.3-2.el7.x86_64
nagios-plugins-disk_smb-2.3.3-2.el7.x86_64
nagios-plugins-dns-2.3.3-2.el7.x86_64
nagios-plugins-dummy-2.3.3-2.el7.x86_64
nagios-plugins-fail2ban-1.4-0.noarch
nagios-plugins-file_age-2.3.3-2.el7.x86_64
nagios-plugins-flexlm-2.3.3-2.el7.x86_64
nagios-plugins-fping-2.3.3-2.el7.x86_64
nagios-plugins-game-2.3.3-2.el7.x86_64
nagios-plugins-hpjd-2.3.3-2.el7.x86_64
nagios-plugins-http-2.3.3-2.el7.x86_64
nagios-plugins-icmp-2.3.3-2.el7.x86_64
...snip 10,000,000...
nagios-plugins-users-2.3.3-2.el7.x86_64
nagios-plugins-wave-2.3.3-2.el7.x86_64
nagios-ssh-keyscan-0.8-1.noarch
percona-nagios-plugins-1.1.8-1.noarch
```

* Yet, as mature as the selection of Plugins is, we've written quite a number over the years!

```
ualib-nagios-plugin-ovirt-1.4-0.noarch
ualib-nagios-plugins-check_service-1.0-0.el7.x86_64
ualib-nagios-plugins-diskstat-1.0-0.el6.x86_64
ualib-nagios-plugins-graphdb-0.1-1.el7.x86_64
ualib-nagios-plugins-ipmi-1.0-0.el6.x86_64
ualib-nagios-plugins-kernelversion-1.1-0.el6.x86_64
ualib-nagios-plugins-logfiles-3.11.0.2-0.el7.x86_64
ualib-nagios-plugins-postgres-2.23.0-0.el7.centos.noarch
ualib-nagios-plugins-triplestore-1.1-2.el7.x86_64
ualib-nagios-plugins-zk-1.1-0.el6.x86_64
UAL-nagios-client-1.6-0.el7.x86_64
```

* "Services" are where the rubber meets the road (Nagios core calls remote Plugins over SSH or NRPE)

```
define service{
        use                     oncall-service
        host_name               assistant.portagenetwork.ca
        service_description     SSL Cert Expiry
        check_interval          1440                    ; Check the service once a day
        retry_interval          60                      ; If it is failing, check it once an hour
        notification_interval   1440                    ; ... but only alert once a day
        contact_groups          admins
        check_command           check_cert!443!21
}
```

* "Hostgroups" are the entire world, allowing you to group services together
    * They can be nested
    * An OS can belong to multiple groups

```
define hostgroup {
        hostgroup_name  os_var
        alias           OS volume var
}
define service{
        use                     oncall-service,graphed-service
        hostgroup_name          os_var
        service_description     /var volume
        contact_groups          unixadmin1s
        check_command           check_ssh_disk_epel!-w 10% -c 5% -p /var!
}
```

* Automation / integration with Ansible
    * UAL invested heavily in Nagios! Installation is automated:
        * We have an [Ansible playbook for deploying a Nagios server in a VM](https://github.com/ualbertalib/ansible-config/tree/main/projects/nagios) (multiple environments)
            * It includes installing & configuring extensions like Nagios-graph
        * /etc/nagios is a separate git repo (Ansible will clone it)
        * There's also a shared role for installing the Plugins: easy Client install
    * But Automated Configuration by Ansible? NO! 
        * The syntax is irregular. Parsers exist, they're not great.
        * We've experimented combining Nagios 

# The future of Nagios

* Icinga2 is the Nagios backend ...
* ... with a nicer, modern (not PHP) frontend
* ... with an API that allows you to script your way to configuratin-automation-nirvana. 
* I've got it running in Dev...
* ...but Kenton said "Nagios ain't broke; don't waste time tryin' to fix it!") 
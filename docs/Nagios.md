(5 min) Traditional monitoring: Nagios / Icinga2
        It's a VM (but container images exist)
        /etc/nagios/ is your friend - as simple or complex as you like
        ... but configuration is done by finger-smashing in vim; copypasta & change the hostname
        "Plugins" are the road (where events are detected)
        "Services" are where the rubber meets the road (Nagios core calls remote Plugins over SSH or NRPE)
        "Hostgroups" are the entire world
         Automation / integration with Ansible
            The syntax is irregular. Parsers exist, they're not great.
            Icinga2 is the Nagios backend with an API that allows you to script your way to automation nirvana. (But Kenton said "Nagios ain't broke; don't waste time tryin' to fix it!") 
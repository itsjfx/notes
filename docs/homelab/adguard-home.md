# AdGuard Home

[AdGuard Home](https://github.com/AdguardTeam/AdGuardHome) is my DNS server of choice. I run two instances: one on a PI on my local network and on my VPS which is running WireGuard.

Things I like:
* Interface is nice
* Controllable via API if needed
* A simple and very advanced way of doing DNS rewrites
    * <https://github.com/AdguardTeam/AdGuardHome/wiki/Hosts-Blocklists>
    * I have different VLANs and subnets and can control how rules are applied with a central DNS server, it's very handy
* There is also a rule tester on the custom filtering rules page. You can specify client and domain name to lookup, which is super helpful for troubleshooting
* Query log viewer
* Decent stats

Advanced rules examples:
```
# override medusa for main
||medusa^$dnsrewrite=192.168.88.9,client=192.168.88.0/24
||medusa.home.jfx.ac^$dnsrewrite=192.168.88.9,client=192.168.88.0/24

# override medusa for iot
||medusa^$important,dnsrewrite=192.168.20.2,client=192.168.20.0/24
||medusa.home.jfx.ac^$important,dnsrewrite=192.168.20.2,client=192.168.20.0/24

# override ntp for iot
||pool.ntp.org^$important,dnsrewrite=192.168.88.3,client=192.168.20.0/24

# drop all DNS lookups in iot
# except for ntp (gets rewritten to egg NTP server)
# medusa (home assistant, frigate, etc)
# and except for smart blinds (needs internet)
||*^$dnsrewrite=NXDOMAIN;;,denyallow=pool.ntp.org|medusa|medusa.home.jfx.ac,client=192.168.20.0/24|~192.168.20.11
```

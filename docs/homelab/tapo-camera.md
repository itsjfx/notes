# Tapo Camera setup with Frigate

1. Buy PoE splitter and injector, make sure it supports up to 9 volts and 0.6 amps
    1. Bullet head shaped
    2. The Tapo does not have native PoE, so you need to inject the CAT cable with power then break it out at the end
    3. I bought these, they work well, cheap and came quickly: <https://www.ebay.com.au/itm/322408095795>
    4. ~$8 AUD each, can probably find on AliExpress cheaper
2. Connect device to internet VLAN, link it to your Tapo account on app
3. Adjust on-screen settings to match your other cameras
4. Do firmware update
5. Set up auto-reboot for an appropriate time
    1. You should really do this, otherwise the camera cuts out a bunch
6. Setup RTSP
7. Cut it over to IoT VLAN
    1. You should allow NTP, or rewrite `*.pool.ntp.org` to a local time server

At the moment I'm having issues where it drops Ethernet connection. I'm seeing if this is localised to a single camera. I may get a smart plug + power board and power cycle the devices myself.

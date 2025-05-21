# Athom Plugs

I use some Athom Plug V2 AU plugs. These were all bought sometime in 2024

## Appearing as "Sonoff Device" after firmware upgrade

* I did a firmware upgrade and it set the device name to "Sonoff Device"
* I lost the ability to do power monitoring in Home Assistant
* Turns out you need to reconfigure the "template" on the Tasmota firmware
* A template in Tasmota is the definition of the device
* <https://tasmota.github.io/docs/Templates>

To fix:
1. Follow <https://tasmota.github.io/docs/Templates/#importing-templates>
2. If you got a multi pack, export the template for a working device, and input the value in the broken device
3. Otherwise, if you don't have access to a multi-pack, you can input the following:
    1. `{"NAME":"Athom Plug V2","GPIO":[0,0,0,3104,0,32,0,0,224,576,0,0,0,0],"FLAG":0,"BASE":18}` if shipped after 20 Jan 2022
    2. `{"NAME":"Athom Power Monitoring Plug","GPIO":[0,0,0,32,2720,2656,0,0,2624,544,224,0,0,1],"FLAG":0,"BASE":18}` if shipped before 20 Jan 2022
4. Your device will reboot and Home Assistant will work as expected

Source for template data: <https://www.athom.tech/blank-1/au-plug> (on page)

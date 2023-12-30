# Blue Yeti

## Blue Yeti showing as USB Advanced Audio Device

This is cause the EEPROM on the Blue Yeti has been wiped. Instructions to fix:

Sources:
* <https://www.youtube.com/watch?v=RzuMYxhqIEs>
* <https://samsontech.zendesk.com/hc/en-us/articles/224920547-Meteor-Mic-Fix-for-Windows>
    * <https://samsontech.zendesk.com/hc/en-us/article_attachments/210570827>

1. Get firmware from [eeprom/firmware.bin](eeprom/firmware.bin) or get a friend to extract it
    1. To extract it: follow the steps below, but select `EEPROM -> FILE`
2. Run `Config6400.exe` tool
    1. This comes from the Samson Technologies link above
3. In Device Manager
    1. Select the Mic (`USB Advanced Audio Device`)
    2. Details
    3. Last known parent
    4. Extract values after `VID_` and `PID_` 
    5. Mine are `08dc` and `016c`
4. Fill in the values at the bottom of the Config tool
5. Press `FILE -> EEPROM`
6. Select the `.bin` file
7. Re-plug the device
8. Should be fixed
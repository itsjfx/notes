# Weechat

* https://wiki.archlinux.org/title/WeeChat
* <https://gist.github.com/atoponce/f19666d6b206a10b411b97381a1861a1>
* <https://blog.weechat.org/post/2008/10/25/Smart-IRC-join-part-quit-message-filter>

TODO make these a dotfiles

```
/plugin unload xfer
/set weechat.plugin.autoload *,!xfer

/set irc.ctcp.action ""
/set irc.ctcp.clientinfo ""
/set irc.ctcp.finger ""
/set irc.ctcp.ping ""
/set irc.ctcp.source ""
/set irc.ctcp.time ""
/set irc.ctcp.userinfo ""
/set irc.ctcp.version ""

/set irc.server_default.msg_part ""
/set irc.server_default.msg_quit ""

/set irc.look.smart_filter on
/filter add irc_smart * irc_smart_filter *

/save
```

<!--
author: JP
publish: Sun Mar 13 2011 15:35:07 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/03/13/migrating-from-tunnelblick-on-os-x-to-openvpn-gui-client-on-windows/
tags: Uncategorized
slug: 2011/03/13/migrating-from-tunnelblick-on-os-x-to-openvpn-gui-client-on-windows
-->

Migrating from Tunnelblick on OS X to OpenVPN GUI Client on Windows
===================================================================

This was done on Windows 7. These same instructions should work for
Windows Vista.

First install OpenVPN from the actual OpenVPN site
here:[http://openvpn.net/index.php/open-source/downloads.html](http://openvpn.net/index.php/open-source/downloads.html)
You should use at least version 2.1.4. Do NOT install OpenVPN GUI from
http://openvpn.se/ It contains an older version that does not work
properly on Windows Vista or Windows 7.

Run the installer as Administrator.

You can copy all of the Tunnelblick OpenVPN files on your OS X in
"\~/Library/Application Support/Tunnelblick/Configurations/" to
"C:\\Program Files\\OpenVPN\\config\\". Change the file extension of all
"\*.conf" files to ".ovpn" The OpenVPN Client GUI software scans for
".opvn" files.

Run the OpenVPN GUI as Administrator. Once connected, look in the log
file and verify that you do not see the following line:

`ROUTE: route addition failed using CreateIpForwardEntry: One or more arguments are not correct.`

If you see this, you didn't install the version from
[openvpn.net](http://openvpn.net/index.php/open-source/downloads.html)
or you didn't run the GUI as Administrator.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

You should follow me on Twitter:
[@jprichardson](http://twitter.com/jprichardson) and read my [blog on
entrepreneurship](http://techneur.com).

-JP

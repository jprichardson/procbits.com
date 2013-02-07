<!--
author: JP
publish: Mon Jul 18 2011 18:44:59 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/07/18/open-a-new-tab-in-terminal-app-in-the-same-directory-on-mac-os-x/
tags: OSX
slug: 2011/07/18/open-a-new-tab-in-terminal-app-in-the-same-directory-on-mac-os-x
-->

Open a New Tab in Terminal.app in the Same Directory on Mac OS X
================================================================

When working with Rails on my Mac OS X machine, I often find myself
opening a lot of terminal tabs. Unfortunately I then need to navigate to
the same directory as the other tabs. It's a big pain. Fortunately
[superuser.com has a
solution](http://superuser.com/questions/232835/zsh-open-a-new-tab-in-the-same-directory/232862#232862).

Create a new file in "/usr/local/bin" named "nt"

```bash
#!/bin/bash
osascript -e 'tell application "Terminal"' \
-e 'tell application "System Events" to tell process "Terminal" to keystroke "t" using command down' \
-e "do script with command \"cd `pwd`;clear\" in selected tab of the front window" \
-e 'end tell' &> /dev/null
```

Then set its executable bit. "chmod +x /usr/local/bin/nt"

That's it!

Advertisement: Make Git simple. Let [Gitpilot](http://gitpilot.com) show
you the right way to Git things done. [Gitpilot](http://gitpilot.com)
will help you write software faster.

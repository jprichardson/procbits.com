<!--
author: JP Richardson
publish: Tue Oct 11 2011 01:00:24 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/10/10/automating-generation-ios-push-notification-certificates/
tags: Ruby
slug: 2011/10/10/automating-generation-ios-push-notification-certificates
-->

Automating the Generation of iOS Push Notification Certificates
===============================================================

I have a large number (200+) of iOS applications. I needed to generate
push notification certificates for each of them. If you've ever gone
through the process, it can be a huge pain to do just a few. I needed to
write a script to automate this process.

First, I created a [ruby gem to access the Mac OS X Keychain
App](http://procbits.com/2011/10/07/automating-the-mac-os-x-keychain-app-with-ruby/).

Next, I leveraged [watir](http://watir.com/) to actually login to the
iOS provisioning portal and upload/download the necessary files.

First, make sure that you're using Mac OS X 10.7 (Lion) and that you
have Chrome and Ruby 1.9.2 installed.

1.  Download chromedriver
    http://code.google.com/p/chromium/downloads/list
2.  Move the binary to /usr/local

Next, run these commands:

```bash
git clone git://github.com/jprichardson/GeneratePushCerts.git
gem install 'keychain_manager'
gem install 'watir-webdriver'
cd ./GeneratePushCerts
```

Modify the 'config.example.yml' file with your iOS
developer/provisioning portal credentials. Rename the file to
'config.yml'.

You'll want to edit 'app.rb' and modify the END\_WITH variable and set
it to ''. I should have created a regular expression around line 60.

Run the app 'ruby app.rb'.

Let's take a look at some watir code from the file:

```ruby
  browser.checkbox(id: 'enablePush').click() #enable configure buttons
  browser.button(id: 'aps-assistant-btn-prod-en').click() #configure button

  Watir::Wait.until { browser.body.text.include?('Generate a Certificate Signing Request') }

  browser.button(id: 'ext-gen59').click() #on lightbox overlay, click continue

  Watir::Wait.until { browser.body.text.include?('Submit Certificate Signing Request') }

  browser.file_field(name: 'upload').set(CERT_REQUEST_FILE)
  browser.execute_script("callFileValidate();")
  #browser.file_field(name: 'upload').click() #calls some local javascript to validate the file and enable continue button, unfortunately File Browse dialog shows up

  browser.button(id: 'ext-gen75').click()

  Watir::Wait.until(WAIT_TO) { browser.body.text.include?('Your APNs SSL Certificate has been generated.') }

  browser.button(id: 'ext-gen59').click() #continue

  Watir::Wait.until { browser.body.text.include?('Step 1: Download') }

  File.delete(DOWNLOADED_CERT_FILE) if File.exists?(DOWNLOADED_CERT_FILE)

  browser.button(alt: 'Download').click() #download cert

  puts('Checking for existence of downloaded certificate file...')
  while !File.exists?(DOWNLOADED_CERT_FILE)
    sleep 1
  end

  Watir::Wait.until { browser.body.text.include?("Download & Install Your Apple Push Notification service SSL Certificate") }

  browser.button(id: 'ext-gen91').click()
  browser.goto(APP_IDS_URL)
```

You can browse the entire [source code on
Github](https://github.com/jprichardson/GeneratePushCerts).

I think watir is pretty intuitive and is a swiss army knife for
automating the interaction with web pages. It's pretty slow as it's
meant for testing, so I wouldn't use it for screen scraping, but it's
served me well for this task.

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git thoughtless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson

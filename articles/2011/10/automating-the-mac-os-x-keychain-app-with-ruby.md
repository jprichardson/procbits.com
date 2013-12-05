<!--
author: JP Richardson
publish: Fri Oct 07 2011 15:39:19 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/10/07/automating-the-mac-os-x-keychain-app-with-ruby/
tags: Ruby
slug: 2011/10/07/automating-the-mac-os-x-keychain-app-with-ruby
title: Automating the Mac OS X Keychain App with Ruby
-->



Recently, I needed a way to automate the generation of 100+ Apple Push
notification certificates for my iOS development. So, I created a [Ruby
Gem [keychain\_manager]](https://rubygems.org/gems/keychain_manager) to
automate the Mac OS X Keychain application.

Here is how you can use it: ` gem install keychain_manager`

```ruby
require 'keychain_manager'

USER = 'jprichardson@gmail.com'
KEYCHAIN = 'apple_push_keychain' #this can be anything, we just don't want to pollute the 'login' keychain
YOUR_DOWNLOADS_DIR = '' # you must set this, this is where the file aps_production_identity.cer exists

RSA_FILE = '/tmp/myrsa.key'
KeychainManager.generate_rsa_key(RSA_FILE)

CERT_FILE = '/tmp/CertificateSigningRequest.certSigningRequest'
KeychainManager.generate_cert_request(USER, 'US', CERT_FILE) #'US' is the country abbreviation.

kcm = KeychainManager.new(KEYCHAIN)
kcm.delete if kcm.exists?
kcm.create

kcm.import_rsa_key(RSA_FILE)

#now from your browser, you'll have downloaded a file from Apple typically named: aps_production_identity.cer
kcm.import_apple_cert(File.join(YOUR_DOWNLOADS_DIR, '/aps_production_identity.cer'))

P12_FILE = '/tmp/push_prod.p12'
kcm.export_identites(P12_FILE)

PEM_FILE = '/tmp/push_prod.pem'
KeychainManager.convert_p12_to_pem(P12_FILE, PEM_FILE)

kcm.delete

#Now upload the PEM_FILE to your server.
```

This gem could easily be modified to support other Keychain functions.
Browse the sourcecode here: [Keychain Manager source
code](https://github.com/jprichardson/keychain_manager).

I'll post soon on how I automated the web portion of communicating with
the iOS Provisioning Portal.



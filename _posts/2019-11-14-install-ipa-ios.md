---
layout: post
title: How to install IPA on an iOS device
---

During a recent pentest, I needed to test an iOS app. Since the app was not in the App Store yet, it was given to me in the form of an .ipa file. I had to figure out a way to load it to my test iPhone. Apparently, there are several instructions on the Internet on how to do that, but I couldn't find the one that worked for me out of the box, so here is my own...

<!--more-->

## Given

1. An iOS app distributed as an IPA file.
1. Non-jailbroken iPhone. In addition, the device has been enterprise provisioned and had all the "niceties" such as MDM.
1. A Linux computer.


## Procedure

First, let's take a look at the IPA file:

```
$ file FairyTailes.ipa 
FairyTailes.ipa: iOS App Zip archive data, at least v1.0 to extract
```

Hey, it looks like a zip... so let's just unzip it!

```
$ unzip FairyTailes.ipa
```

This will create a subdirectory `Payload` with all the app's content in it. Open the app's plist file `Payload/FairyTailes.app/Info.plist`, search for `CFBundleIdentifier` and `CFBundleVersion`, and note their values:
```xml
<key>CFBundleIdentifier</key>
<string>com.grimm.fairytales</string>
```

```xml
<key>CFBundleVersion</key>
<string>1863</string>
```

In the directory where the IPA file is located, start an HTTP server:

```
$ python -m SimpleHTTPServer 
Serving HTTP on 0.0.0.0 port 8000 ...
```

If you haven't already, go ahead and get a free [ngrok](https://ngrok.com) account (tip: ngrok is an amazing tool!) After you download and set up ngrok, expose your HTTP server to the Internet:

```
$ ngrok http 8000
```

In ngrok's output, note the HTTPS forwarding address that looks something like `https://4c265f53.ngrok.io`


In the same directory where the IPA file is located, create a file called `download.html` with the following content (of course, use your **actual** ngrok address, and make sure it's the HTTPS one):

```html
<html><body>
<h1><a href="itms-services://?action=download-manifest&url=https://4c265f53.ngrok.io/FairyTailes.plist">
Download the app
</a></h1>
</body></html>
```

In the same directory, create `FairyTailes.plist` and use the identifier and version values you obtained from `Info.plist` earlier. Of course, also use the actual ngrok address.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>items</key>
  <array>
    <dict>
      
      <key>assets</key>
      <array>
        <dict>
          
          <key>kind</key>
          <string>software-package</string>
          
          <key>url</key>
          <string>https://4c265f53.ngrok.io/FairyTales.ipa</string>
        
        </dict>
      </array>
      
      <key>metadata</key>
      <dict>
        
        <key>bundle-identifier</key>
        <string>com.grimm.fairytales</string>
        
        <key>bundle-version</key>
        <string>1863</string>
        
        <key>kind</key>
        <string>software</string>
        
        <key>title</key>
        <string>Fairy Tales</string>
      
      </dict>
    </dict>
  </array>
</dict>
</plist>
```

On your iOS device, go to https://4c265f53.ngrok.io/download.html (make sure it's HTTPS, and do I need to say again you must use the **actual** ngrok address?) and click the *Download* link. Confirm the prompt to install the app. It should silently install and appear on your home screen, so go ahead to the home screen and click on your new shiny app.

Oh no, you get an error **Untrusted Enterprise Developer**, don't you? Don't worry, this is easy to fix:

1. Open the Settings app.
1. Go to General -> Device Management and click on the name of the app's developer (in my case it was *"Brothers Grimm"*).
1. Click *Trust "Brothers Grimm"* and confirm the prompt.

Go back to the home screen and try to launch the app again - it should work now!!!


## Bonus: proxy the app traffic through Burp

Assuming the machine where you run Burp Suite and your iOS device are on the same network:

1. [Configure the iOS device to work with Burp](https://support.portswigger.net/customer/portal/articles/1841108--Mobile%20Set-up_iOS%20Device.html)
2. [Install Burp's CA certificate in the iOS device](https://support.portswigger.net/customer/portal/articles/1841109-Mobile%20Set-up_iOS%20Device%20-%20Installing%20CA%20Certificate.html)


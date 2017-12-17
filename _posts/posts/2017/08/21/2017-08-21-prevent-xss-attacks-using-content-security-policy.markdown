---
title: Prevent XSS Attacks using Content Security Policy
date: 2017-08-21 17:33:00 Z
tags:
- Xss Attack
- Xss Prevention
- Content Security Policy
background: http://www.hdnicewallpapers.com/Walls/Big/Owl/Baby_Child_of_Owl_Bird_Photo_Background.jpg
layout: post
---

**Prevent XSS Attacks using Content Security Policy**

Today, I am working my project and make a script that downloads the other scripts. To test script working fine, I m trying to run this script on the console of the opened website.I tried it on my own website, it works fine but when I try to run the script in Gmail website console. I got an error:

    Refused to load the script 'http://abc.com/abc.js' because it violates the following Content Security Policy ﻿directive: "script-src https://clients4.google.com/insights/consumersurveys/ ﻿https://www.google.com/js/bg/ 'self' 'unsafe-inline' 'unsafe-eval' ﻿https://mail.google.com/*scs/mail-static/ ﻿https://hangouts.google.com/ https://talkgadget.google.com/ ﻿https://\*.talkgadget.google.com/ ﻿https://www.googleapis.com/appsmarket/v2/installedApps/ ﻿https://www-gm-opensocial.googleusercontent.com/gadgets/js/ ﻿https://docs.google.com/static/doclist/client/js/ ﻿https://www.google.com/tools/feedback/ ﻿https://s.ytimg.com/yts/jsbin/ ﻿https://www.youtube.com/iframe_api ﻿https://ssl.google-analytics.com/ ﻿https://apis.google.com/*/scs/abc-static/ ﻿https://apis.google.com/js/ ﻿https://clients1.google.com/complete/....".

I saw this error in console screen, so try to find the why Gmail does n’t allow me. In error, I found the Content security policy. This is property who tell the browser which content needs to be download on a page.

The\*\* [Content-Security-Policy](https://content-security-policy.com/) \*\*meta-tag allows you to reduce the risk of [XSS](http://en.wikipedia.org/wiki/Cross-site_scripting)attacks by allowing you to define where resources can be loaded from, preventing browsers from loading data from any other locations. This makes it harder for an attacker to inject malicious code into your site.

To add content security policy on your site, what you have to do, is to add a meta tag in a head for pages.

<meta http-equiv="Content-Security-Policy" content="default-src 'self'">

or in a server response(in web.config)

    <system.webServer>
        <httpProtocol>
            <customHeaders>
                 <add name="Content-Security-Policy" value="default-src 'self';" />
            </customHeaders>
        </httpProtocol>
    </system.webServer>

**The most common directives are**:

* default-src the default policy for loading javascript, images, CSS, fonts, AJAX requests, etc

* script-src defines valid sources for javascript files

* style-src defines valid sources for CSS files

* img-src defines valid sources for images

* connect-src defines valid targets for to XMLHttpRequest (AJAX), WebSockets or EventSource. If a connection attempt is made to a host that’s not allowed here, the browser will emulate a 400error

There are others, but these are the ones you’re most likely to need.You can control various operation in your application using content security policy like the sources from where your contents come to your website.

I know this one way to protect our website from XSS attack. if you have any XSS prevention technique, please share it on comments.

Thanks for reading! :)
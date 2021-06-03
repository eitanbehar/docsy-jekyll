---
title:  "OIDC Confiuration"
date:   2020-08-05
badges: 
 - type: info
   tag: oauth
 - type: info
   tag: oidc
---

Tales of configuring OIDC for Google, Yahoo!, GitLab, Apple (?), and  others

<!--more-->

Configure a Site to use a well-known Identity Provider (e.g. Yahoo!) using OpenID Connect (OIDC).

First, trust me, don't trust this list:
[Wiki List of OAuth and OIDC providers](https://en.wikipedia.org/wiki/List_of_OAuth_providers)

It says Yahoo! does not support OIDC, but Yahoo! says it does:
[Yahoo!'s OIDC Configuration](https://developer.yahoo.com/oauth2/guide/openid_connect/getting_started.html)

So, how to configure OIDC for an external provider?
The steps are similar to all, so I will mention the general steps, and only the endpoints per provider.

**On Provider**:
* Create an Application
* Set Redirect URL 
* Enable ***openid*** scope, and *optionally* profile, email
* You'll need the **client_id** and **client_secret** 

***On Consumer's Site:***
* Go to OIDC Login
* Add Provider
* Set Provider name (e.g. yahoo)
* Set Client ID and Secret as defined in the Provider app
* Set Endpoints according to the provider URLs 


## Providers URLs

**Yahoo!**
* Apps portal: https://developer.yahoo.com/apps/
* URLS:
    * https://api.login.yahoo.com/oauth2/request_auth
    * https://api.login.yahoo.com/oauth2/get_token
    * https://api.login.yahoo.com/openid/v1/userinfo

> Reference: https://developer.yahoo.com/oauth2/guide/openid_connect/

**Google**
* Apps portal: https://console.developers.google.com/apis/credentials
* URLS:
    * https://accounts.google.com/o/oauth2/v2/auth
    * https://oauth2.googleapis.com/token
    * https://openidconnect.googleapis.com/v1/userinfo

> Reference: https://developers.google.com/identity/protocols/oauth2/openid-connect

**GitLab**
* Apps portal: https://gitlab.com/profile/applications
* URLS:
    * https://gitlab.com/oauth/authorize
    * https://gitlab.com/oauth/token
    * https://gitlab.com/oauth/userinfo

> Reference: https://docs.gitlab.com/ee/integration/openid_connect_provider.html

**Microsoft**
* TBD 

**Apple**
 
 Don't know if I will ever find out, a developer's license costs around 33 hamburgers

 > In any case, the process is detailed here: 
 https://developer.okta.com/blog/2019/06/04/what-the-heck-is-sign-in-with-apple




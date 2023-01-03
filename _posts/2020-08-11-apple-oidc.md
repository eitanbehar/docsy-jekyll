---
title:  "Apple and OIDC Login"
date:   2020-08-11 
badges: 
 - type: info
   tag: oidc
 - type: info
   tag: apple
---

Lessons learned when configuring Apple for OIDC Login


<!--more-->

First at all, it's hard to find proper documentation on how to configure Apple.
And most probably, these instructions might change over time.

These are the most significant references that we found:
* https://developer.okta.com/blog/2019/06/04/what-the-heck-is-sign-in-with-apple
* https://bitbucket.org/openid/connect/src/master/How-Sign-in-with-Apple-differs-from-OpenID-Connect.md
* https://appleid.apple.com/.well-known/openid-configuration
* https://stackoverflow.com/questions/18971983/curl-requires-curlopt-ssl-verifypeer-false
* https://developer.apple.com/documentation/signinwithapplerestapi/generate_and_validate_tokens 

## How to configure Apple as an OP, create a simple RP client and a local server to handle the redirect

#### On Apple

You'll need an Apple Developers account, if you don't have one, well, it costs 100 USD per year :angry:

In general, you'll need to create an App, Service (which is the client_id), and a private key.
The private key is downloaded, and using a ruby script, you can generate the client_secret *(which is a jwt token)*.
The steps are detailed here: https://developer.okta.com/blog/2019/06/04/what-the-heck-is-sign-in-with-apple


#### Client RP

**login.php**
```js
<?php
    session_start();

    $client_id = 'com.oidcserviceid';
    $redirect_uri = 'http://eitan.be.com/demo/apple/redirect.php';

    $_SESSION['state'] = bin2hex(random_bytes(5));

    $authorize_url = 'https://appleid.apple.com/auth/authorize'.'?'.http_build_query([
    'response_type' => 'code',
    'response_mode' => 'form_post',
    'client_id' => $client_id,
    'redirect_uri' => $redirect_uri,
    'state' => $_SESSION['state'],
    'scope' => 'openid email name', //name email
    ]);
    echo '<a href="'.$authorize_url.'">Sign In with Apple</a>';
?>
```

#### Server side

> Note that you'll need a PHP server running the redirect page
> The built-in PHP server can be used (php -S 127.0.0.1:8080)
> Although, on Windows, I prefer to install PHP over IIS and have a dedicated site

**redirect.php**
```js
<?php

    $client_id = 'com.oidcserviceid';
    $client_secret = 'CLIENT-SECRET_GOES-HERE';
    $redirect_uri = 'http://eitan.be.com/demo/apple/redirect.php';

    if(isset($_POST['code'])) {

    #if($_SESSION['state'] != $_POST['state']) {        
        #die('<p>Authorization server returned an invalid state parameter</p>');
    #}

    // Token endpoint docs: 
    // https://developer.apple.com/documentation/signinwithapplerestapi/generate_and_validate_tokens

    $response = http('https://appleid.apple.com/auth/token', [
        'grant_type' => 'authorization_code',
        'code' => $_POST['code'],
        'redirect_uri' => $redirect_uri,
        'client_id' => $client_id,
        'client_secret' => $client_secret,
    ]);

    echo '<h3>Access Token Response</h3>';
    echo '<pre>'; print_r($response); echo '</pre>';

    if(!isset($response->access_token)) {
        echo '<p>Error getting an access token:</p>';
        echo '<p><a href="../">Start Over</a></p>';
        die();
    }

    $claims = explode('.', $response->id_token)[1];
    $claims = json_decode(base64_decode($claims));

    echo '<h3>Parsed ID Token</h3>';
    echo '<pre>';
    print_r($claims);
    echo '</pre>';

    die();
    }

    function http($url, $params=false) {
        echo $url; echo "?"; echo(http_build_query($params));        
        $ch = curl_init($url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        if($params)
          curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($params));
          curl_setopt($ch, CURLOPT_POST, true);
          curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);          
          curl_setopt($ch, CURLOPT_HTTPHEADER, [
          'Accept: */*',
          'Connection: keep-alive',
          'Accept-Encoding: br,gzip,deflate',
          'User-Agent: eitan.be.com' # Apple requires a user agent header at the token endpoint
        ]);
        $response = curl_exec($ch);
        curl_close($ch);        
        return json_decode($response);
      }     

?>
```

#### Additional Info

* The user name is supplied only during the **first** connection, upon accepting the consent screen
* The user email is supplied on every authorization request, **if** the user selected sharing the email address


##### First connection sample

Authorization response (code) * note that it includes the user's firstName and lastName attributes *
```json
{ ["state"]=> string(10) "4c787429d2" ["code"]=> string(63) "c37e98af176a14ca1949c2482c646454c.0.nwqv.rLqViV0PdM01Z1cTXMNuRA" ["user"]=> string(77) "{"name":{"firstName":"Eitan","lastName":"Behar"},"email":"eitan@baconao.net"}" }
```
Token request response (token)
```json
{"access_token":"ac9ab2c17c9224c0fb3fba2ce380f714c.0.mwqv.8dcUtL9a3zDiMjNsG3V4uA","token_type":"Bearer","expires_in":3600,"refresh_token":"r97d86e2cb7b5492ca2a806c8d59b0209.0.mwqv.5fkAUqUDgdvh8RzsMqmzGw","id_token":"eyJraWQiOiI4NkQ4OEtmIiwiYWxnIjoiUlMyNTYifQ.eyJpc3MiOiJodHRwczovL2FwcGxlaWQuYXBwbGUuY29tIiwiYXVkIjoiY29tLmdpZ3lhLm9pZGNzZXJ2aWNlaWQiLCJleHAiOjE1OTcxMzAzNDYsImlhdCI6MTU5NzEyOTc0Niwic3ViIjoiMDAwNjA1LjYwMmFjZGQ2ODZjZjQwNzU5OGQ1Mjc3YWE3ZmE3Nzc5LjA5MzQiLCJhdF9oYXNoIjoid0l3eE9YOUdpRG9MTGNOZzhSMk1tUSIsImVtYWlsIjoiZWl0YW5AYmFjb25hby5uZXQiLCJlbWFpbF92ZXJpZmllZCI6InRydWUiLCJhdXRoX3RpbWUiOjE1OTcxMjk3NDQsIm5vbmNlX3N1cHBvcnRlZCI6dHJ1ZX0.CYSWrBgvfhWQXC9I3W0FIY2C7DMjbjesH0srEDssVdOF-rnjgRc23ReJ11s-Yz_CqCVsBAUkAhvyGHVUrnUC0Xq8jxtLnHY9-HUyvS2xxLGUQJ31cBsMZH87d4gWLZZY4NKqV-qB-sdMsXqmgEwH3cNXLHDHTFFZ7wrSHvGE-YXGec0Bv9jdRSKgG1Wre5JnRMCW3uze7aCqUczoS7vZaefzWT6fdOyOFvyklzc5RvgkrZljEva5K7Hg6riWSSlT2qcSwCCV1PivrVPE6mh18xso7jLPeHKO9jDLEtvUY1wB1pJox7314wb1DCrfmCjFGwbDOsIhQa40v_LIyvJgQg"}
```
Parsed ID Token
```js
stdClass Object
(
    [iss] => https://appleid.apple.com
    [aud] => com.oidcserviceid
    [exp] => 1597130346
    [iat] => 1597129746
    [sub] => 000605.602acdd686cf407598d5277aa7fa7779.0934
    [at_hash] => wIwxOX9GiDoLLcNg8R2MmQ
    [email] => eitan@baconao.net
    [email_verified] => true
    [auth_time] => 1597129744
    [nonce_supported] => 1
)
```

##### Subsequent connection sample

Authorization response (code) * note that it **does not** include the user's firstName and lastName attributes *
```json
{ ["state"]=> string(10) "c86fc76ca6" ["code"]=> string(63) "c4ffd4cb5042d4b23ac7eff816a88e4d2.0.nwqv.4X5XxRlTkNzsQP6fbObp4A" }
```
Token request response (token)
```json
{"access_token":"ab426e0243aa54d3ca5728154e5aa8b9e.0.mwqv.E0AQFfOR32cobupA_GXvbw","token_type":"Bearer","expires_in":3600,"refresh_token":"raf27d36dabea494d8c0a4db138805aa4.0.mwqv.3gpBlMnpWB2uTBnq78enAQ","id_token":"eyJraWQiOiJlWGF1bm1MIiwiYWxnIjoiUlMyNTYifQ.eyJpc3MiOiJodHRwczovL2FwcGxlaWQuYXBwbGUuY29tIiwiYXVkIjoiY29tLmdpZ3lhLm9pZGNzZXJ2aWNlaWQiLCJleHAiOjE1OTcxMzA0MjIsImlhdCI6MTU5NzEyOTgyMiwic3ViIjoiMDAwNjA1LjYwMmFjZGQ2ODZjZjQwNzU5OGQ1Mjc3YWE3ZmE3Nzc5LjA5MzQiLCJhdF9oYXNoIjoiOU91UzduTHFMRFdmRnRmZVA0aVRwZyIsImVtYWlsIjoiZWl0YW5AYmFjb25hby5uZXQiLCJlbWFpbF92ZXJpZmllZCI6InRydWUiLCJhdXRoX3RpbWUiOjE1OTcxMjk4MjEsIm5vbmNlX3N1cHBvcnRlZCI6dHJ1ZX0.0sD0vyQHC4-WCvRrMjckcSuaeid7fNoF_oaSPIII8HwAINiOZKDDaiA-W23Ci2FXM7M0j41IvL9ltmmAIMuJ34CWe8p4r2gNHHLf9xJVQkP_IzNSVidOVECNclQRy2D0ajMYSzAch0Q3FKCiaZOLZ5D2OC3MXvOR4Rb-VOvK-7Je5tF28dPNC7zdYhCN7QjfmUZ6agpk51ST1htYMROPp-CcXj6pIQENT2ssrBFWLtx-jscAKR2zH1qbzVW-d4HBTnpaoJ2VujOW71UyGgA9txhl72-xKL6axId-6gYjmgxu554ji0VLEeF8vVJ0-M1ROxb138Ht_LzT-YgRLv4kPw"}
```
Parsed ID Token
```js
stdClass Object
(
    [iss] => https://appleid.apple.com
    [aud] => com.oidcserviceid
    [exp] => 1597130422
    [iat] => 1597129822
    [sub] => 000605.602acdd686cf407598d5277aa7fa7779.0934
    [at_hash] => 9OuS7nLqLDWfFtfeP4iTpg
    [email] => eitan@baconao.net
    [email_verified] => true
    [auth_time] => 1597129821
    [nonce_supported] => 1
)
```



---
title:  "How To Extract A Certificate From A HTTPS Call Using .NET Core"
date:   2021-03-08 
categories: https, netcore, certificate
badges: 
 - type: info
   tag: netcore
---

Sometimes you want to programmatically extract / export the server certificates in a HTTPS call. 
Well, it’s pretty simple. See below a C# working example for .NET Core

<!--more-->

## Declare your Http Handler

~~~ csharp
private readonly HttpClientHandler handler; 
~~~

## Initialize the handler, Callback method, and Certificate Export

~~~ csharp
handler = new HttpClientHandler
{
    ServerCertificateCustomValidationCallback = (message, certificate, chain, sslPolicyErrors) =>
    {
        File.WriteAllBytes("C://Temp/Test.cer", certificate.Export(X509ContentType.Cert));

        return true;
    }
};
~~~ 

## Do a HTTP call using the handler

~~~ csharp
public string DoACall(string url)
{
    using (var client = new HttpClient(handler))
    {
        var response = client.GetAsync(url, HttpCompletionOption.ResponseHeadersRead);
        return response.Result.ToString();
    }
}
~~~

## Usage

~~~ csharp
var caller = new WebCaller();
var result = caller.DoACall("https://google.com/");
~~~ 

## Source Code

<https://github.com/eitanbehar/WebClient>


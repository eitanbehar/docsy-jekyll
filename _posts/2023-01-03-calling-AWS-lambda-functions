---
title:  "Calling AWS Lambda functions from LINQPad"
date:   2023-01-03 
badges: 
 - type: info
   tag: csharp
tags:
  - AWS  
---

* Open LINQPad
* Add reference to JSON.NET
* Use this C# statement to call a Lambda function in AWS:

```csharp
 var lambdaUrl = "https://<base>.amazonaws.com/default/<api>";

var message = "some message";
var data = new { message };
var payloadJson = JsonConvert.SerializeObject(data);

var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Post, lambdaUrl);
request.Content = new StringContent(payloadJson, Encoding.UTF8, "application/json");
var response = await client.SendAsync(request);

Console.WriteLine(await response.Content.ReadAsStringAsync());
```

In order to create/configure the lambda function, follow these steps:
* Login to AWS
* Search for `Lambda`
* Create new function
  * Select existing permission `lambda_execution`
* Go to Configuration
   * Create New trigger of type API Gateway
   * Set Security to None
   
   
   


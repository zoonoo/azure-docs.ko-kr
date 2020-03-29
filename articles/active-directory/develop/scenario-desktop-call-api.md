---
title: 데스크톱 앱에서 웹 API 호출 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 데스크톱 앱을 빌드하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2b3d9fdc163d0661670f3d0cf6e6a276c8b691bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702167"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>웹 API를 호출하는 데스크톱 앱: 웹 API 호출

이제 토큰이 있으므로 보호된 웹 API를 호출할 수 있습니다.

## <a name="call-a-web-api"></a>웹 API 호출

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[Macos](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서 웹 API 호출

토큰을 획득하는 메서드는 `MSALResult` 개체를 반환합니다. `MSALResult`웹 API를 `accessToken` 호출하는 데 사용할 수 있는 속성을 노출합니다. 보호된 웹 API에 액세스하기 위해 호출하기 전에 HTTP 권한 부여 헤더에 액세스 토큰을 추가합니다.

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };

NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>여러 API 호출: 증분 동의 및 조건부 액세스

첫 번째 API에 대한 토큰을 받은 후 동일한 사용자에 대해 `AcquireTokenSilent`여러 API를 호출하려면 을 호출합니다. 대부분의 경우 다른 API에 대한 토큰을 자동으로 받게 됩니다.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

상호 작용은 다음과 같은 경우에 필요합니다.

- 사용자는 첫 번째 API에 동의했지만 이제 더 많은 범위에 대해 동의해야 합니다. 이러한 종류의 동의는 증분 동의라고 합니다.
- 첫 번째 API에는 다단계 인증이 필요하지 않지만 다음 API는 인증을 필요로 하지 않습니다.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```
---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 환경으로 이동](scenario-desktop-production.md)

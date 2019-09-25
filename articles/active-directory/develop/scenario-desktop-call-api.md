---
title: Web api를 호출 하는 데스크톱 앱 (웹 API 호출)-Microsoft identity platform
description: Web api를 호출 하는 데스크톱 앱을 빌드하는 방법 알아보기
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268286"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>웹 api를 호출 하는 데스크톱 앱-web API 호출

이제 토큰이 있으므로 보호 된 web API를 호출할 수 있습니다.

## <a name="calling-a-web-api-from-net"></a>.NET에서 web API 호출

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL에서 web API 호출

토큰을 획득 하는 메서드는 `MSALResult` 개체를 반환 합니다. `MSALResult`웹 API를 호출 하는 데 사용할 수 있는 속성을노출합니다.`accessToken` 보호 된 Web API에 액세스 하기 전에 액세스 토큰을 HTTP 권한 부여 헤더에 추가 해야 합니다.

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

Swift

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>여러 Api 호출-증분 동의 및 조건부 액세스

동일한 사용자에 대해 여러 api를 호출 해야 하는 경우 첫 번째 api에 대 한 토큰을 얻은 후에만를 호출 `AcquireTokenSilent`하면 대부분의 시간에 자동으로 다른 api에 대 한 토큰을 얻을 수 있습니다.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

상호 작용이 필요한 경우는 다음과 같습니다.

- 사용자가 첫 번째 API에 대해 동의한 이제 더 많은 범위 (증분 승인)에 동의 해야 합니다.
- 첫 번째 API에는 multi-factor authentication이 필요 하지 않습니다.

```CSharp
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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-desktop-production.md)

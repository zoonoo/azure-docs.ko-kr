---
title: 모바일 앱에서 웹 API 호출 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법을 알아봅니다. (웹 API를 호출합니다.)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160154"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>모바일 앱에서 웹 API 호출

앱이 사용자에 로그인하고 토큰을 수신한 후 MSAL(Microsoft 인증 라이브러리)은 사용자, 사용자 환경 및 발급된 토큰에 대한 정보를 노출합니다. 앱은 이러한 값을 사용하여 웹 API를 호출하거나 사용자에게 환영 메시지를 표시할 수 있습니다.

이 문서에서는 먼저 MSAL 결과를 살펴보겠습니다. 그런 다음 액세스 토큰을 `AuthenticationResult` 사용하거나 `result` 보호된 웹 API를 호출하는 방법을 살펴보겠습니다.

## <a name="msal-result"></a>MSAL 결과
MSAL은 다음 값을 제공합니다. 

- `AccessToken`HTTP 보유자 요청에서 보호된 웹 API를 호출합니다.
- `IdToken`에는 로그인한 사용자에 대한 유용한 정보가 들어 있습니다. 이 정보에는 사용자의 이름, 홈 테넌트 및 저장소에 대한 고유 식별자가 포함됩니다.
- `ExpiresOn`은 토큰의 만료 시간입니다. MSAL은 앱의 자동 새로 고침을 처리합니다.
- `TenantId`은 사용자가 로그인한 테넌트의 식별자입니다. Azure Active Directory(Azure AD) B2B의 게스트 사용자의 경우 이 값은 사용자가 로그인한 테넌트를 식별합니다. 값은 사용자의 홈 테넌트를 식별하지 않습니다.  
- `Scopes`은 토큰으로 부여된 범위를 나타냅니다. 부여된 범위는 요청한 범위의 하위 집합일 수 있습니다.

MSAL은 `Account` 값에 대한 추상화도 제공합니다. 값은 `Account` 현재 사용자의 로그인 계정을 나타냅니다.

- `HomeAccountIdentifier`사용자의 홈 테넌트를 식별합니다.
- `UserName`은 사용자의 기본 사용자 이름입니다. Azure AD B2C 사용자의 경우 이 값이 비어 있을 수 있습니다.
- `AccountIdentifier`로그인한 사용자를 식별합니다. 대부분의 경우 이 값은 사용자가 `HomeAccountIdentifier` 다른 테넌트의 게스트가 아니면 값과 동일합니다.

## <a name="call-an-api"></a>API 호출

액세스 토큰이 있으면 웹 API를 호출할 수 있습니다. 앱은 토큰을 사용하여 HTTP 요청을 빌드한 다음 요청을 실행합니다.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL

토큰을 획득하는 메서드는 `MSALResult` 개체를 반환합니다. `MSALResult`속성을 노출합니다. `accessToken` 웹 API를 호출하는 데 사용할 `accessToken` 수 있습니다. 보호된 웹 API에 액세스하기 위해 호출하기 전에 이 속성을 HTTP 권한 부여 헤더에 추가합니다.

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

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>여러 API 요청 만들기

동일한 API를 여러 번 호출해야 하거나 여러 API를 호출해야 하는 경우 앱을 빌드할 때 다음 주제를 고려하십시오.

- **증분 동의**: Microsoft ID 플랫폼을 사용하면 앱에서 처음에 권한이 필요한 경우 사용자 동의를 얻을 수 있습니다. 앱이 API를 호출할 준비가 될 때마다 필요한 범위만 요청해야 합니다.

- **조건부 액세스**: 여러 API 요청을 할 때 특정 시나리오에서는 추가 조건부 액세스 요구 사항을 충족해야 할 수 있습니다. 첫 번째 요청에 조건부 액세스 정책이 없고 앱에서 조건부 액세스가 필요한 새 API에 자동으로 액세스하려고 시도하는 경우 이러한 방식으로 요구 사항이 증가할 수 있습니다. 이 문제를 처리하려면 자동 요청에서 오류를 catch하고 대화형 요청을 할 준비를 하십시오.  자세한 내용은 [조건부 액세스에 대한 지침을](../azuread-dev/conditional-access-dev-guide.md)참조하십시오.

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>증분 동의 및 조건부 액세스를 사용하여 여러 API를 호출합니다.

동일한 사용자에 대해 여러 API를 호출해야 하는 경우 사용자에 대한 토큰을 획득한 후 나중에 토큰을 가져오도록 호출하여 `AcquireTokenSilent` 사용자에게 자격 증명을 반복적으로 요청하지 않도록 할 수 있습니다.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

상호 작용은 다음과 같은 경우에 필요합니다.

- 사용자는 첫 번째 API에 동의했지만 이제 더 많은 범위에 대해 동의해야 합니다. 이 경우 증분 동의를 사용합니다.
- 첫 번째 API에는 다단계 인증이 필요하지 않지만 다음 API는 인증을 수행합니다.

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 환경으로 이동](scenario-mobile-production.md)

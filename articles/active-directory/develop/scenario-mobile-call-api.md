---
title: 모바일 앱에서 웹 API 호출 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다. (웹 API를 호출합니다.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 781406a1bfd253f0ab3eb333f23917be4aeb3ba9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83771743"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>모바일 앱에서 웹 API 호출

앱에서 사용자를 로그인하고 토큰을 받은 후 MSAL(Microsoft 인증 라이브러리)에서 사용자, 사용자 환경 및 발급된 토큰에 대한 정보를 제공합니다. 앱은 이러한 값을 사용하여 웹 API를 호출하거나 사용자에게 환영 메시지를 표시할 수 있습니다.

이 문서에서는 먼저 MSAL 결과를 살펴보겠습니다. 그런 다음 `AuthenticationResult` 또는 `result`에서 액세스 토큰을 사용하여 보호된 웹 API를 호출하는 방법을 살펴보겠습니다.

## <a name="msal-result"></a>MSAL 결과
MSAL은 다음 값을 제공합니다. 

- `AccessToken`은 HTTP 전달자 요청에서 보호된 웹 API를 호출합니다.
- `IdToken`에는 로그인한 사용자에 대한 유용한 정보가 포함되어 있습니다. 사용자 이름, 홈 테넌트 및 스토리지에 대한 고유 식별자 등의 정보입니다.
- `ExpiresOn`은 토큰의 만료 시간입니다. MSAL은 앱의 자동 새로 고침을 처리합니다.
- `TenantId`는 사용자가 로그인한 테넌트의 식별자입니다. Azure AD(Azure Active Directory) B2B에서 게스트 사용자의 경우 이 값은 사용자가 로그인한 테넌트를 식별합니다. 이 값은 사용자의 홈 테넌트를 식별하지 않습니다.  
- `Scopes`는 토큰과 함께 부여된 범위를 식별합니다. 부여된 범위는 요청한 범위의 하위 집합일 수 있습니다.

또한 MSAL은 `Account` 값에 대한 추상적 개념을 제공합니다. `Account` 값은 현재 사용자가 로그인한 계정을 나타냅니다.

- `HomeAccountIdentifier`는 사용자의 홈 테넌트를 식별합니다.
- `UserName`은 사용자의 기본 설정 사용자 이름입니다. Azure AD B2C 사용자의 경우에는 이 값이 비어 있을 수 있습니다.
- `AccountIdentifier`는 로그인한 사용자를 식별합니다. 대부분의 경우 이 값은 사용자가 다른 테넌트의 게스트가 아닌 한 `HomeAccountIdentifier` 값과 동일합니다.

## <a name="call-an-api"></a>API 호출

액세스 토큰이 있으면 웹 API를 호출할 수 있습니다. 앱은 토큰을 사용하여 HTTP 요청을 작성한 다음 요청을 실행합니다.

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

토큰을 획득하는 메서드는 `MSALResult` 개체를 반환합니다. `MSALResult`는 `accessToken` 속성을 노출합니다. `accessToken`을 사용하여 웹 API를 호출할 수 있습니다. 호출하기 전에 이 속성을 HTTP 인증 헤더에 추가하여 보호된 웹 API에 액세스합니다.

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

동일한 API를 여러 번 호출해야 하거나 여러 API를 호출해야 하는 경우에는 앱을 빌드할 때 다음 사항을 고려해야 합니다.

- **증분 동의**: Microsoft ID 플랫폼을 사용하면 앱 시작 시가 아닌 권한이 필요할 때 사용자 동의를 가져올 수 있습니다. 앱이 API를 호출할 준비가 될 때마다 필요한 범위만 요청해야 합니다.

- **조건부 액세스**: 여러 API 요청을 만들 때 특정 시나리오에서는 추가 조건부 액세스 요구 사항을 충족해야 할 수 있습니다. 첫 번째 요청에 조건부 액세스 정책이 없고 앱에서 조건부 액세스가 필요한 새 API에 자동으로 액세스하려는 경우 이런 식으로 요구 사항이 늘어날 수 있습니다. 이 문제를 처리하려면 자동 요청에서 오류를 파악하고 대화형 요청을 준비해야 합니다.  자세한 내용은 [조건부 액세스에 대한 지침](../azuread-dev/conditional-access-dev-guide.md)을 참조하세요.

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>증분 동의 및 조건부 액세스를 사용하여 여러 API 호출

동일한 사용자에 대해 여러 API를 호출해야 하는 경우 사용자에 대한 토큰을 가져온 후에는 `AcquireTokenSilent`를 호출하여 토큰을 가져오기 위해 사용자에게 자격 증명을 묻는 메시지를 반복적으로 표시하지 않을 수 있습니다.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

다음과 같은 경우 상호 작용이 필요합니다.

- 사용자가 첫 번째 API에 대해 동의했지만 이제 더 많은 범위에 동의해야 하는 경우. 이 경우에는 증분 동의를 사용합니다.
- 첫 번째 API는 [다단계 인증](../authentication/concept-mfa-howitworks.md)이 필요하지 않지만 다음 API에서는 다단계 인증을 필요로 하는 경우

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

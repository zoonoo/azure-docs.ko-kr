---
title: 호출 웹 Api-web API 호출 되는 모바일 앱 | Microsoft id 플랫폼
description: 웹 Api (web API 호출)를 호출 하는 모바일 앱을 빌드하는 방법을 알아봅니다
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962391"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>웹 Api를 호출 하는 모바일 앱 웹 API를 호출 합니다.

앱 사용자를 로그인 하 고 후에 토큰을 수신, MSAL 일부의 사용자, 사용자 환경 및 발급 된 토큰에 대 한 정보를 표시 합니다. 앱 웹 API를 호출 하거나 사용자에 게 환영 메시지를 표시 이러한 값을 사용할 수 있습니다.

먼저 MSAL 결과 살펴보겠습니다. 다음에서 액세스 토큰을 사용 하는 방법을 살펴보겠습니다 합니다 `AuthenticationResult` 또는 `result` 보호 된 web API를 호출 합니다.

## <a name="msal-result"></a>MSAL 결과
MSAL은 다음 값을 제공합니다. 

- `AccessToken`: 전달자 HTTP 요청에 보호 된 web Api를 호출 하는 데 사용 합니다.
- `IdToken`: 로그인 사용자, 사용자의 이름, 홈 테 넌 트 및 저장소에 대 한 고유 식별자와 같은 유용한 정보를 포함합니다.
- `ExpiresOn`: 토큰의 만료 시간입니다. MSAL은 앱에 대 한 자동 새로 고침을 처리 합니다.
- `TenantId`: 사용자 로그인에 테 넌 트의 식별자입니다. 게스트 사용자 (Azure Active Directory B2B)에 대 한이 값은 테 넌 트 사용자 로그인을 하지 사용자의 홈 테 넌 트를 식별 합니다.  
- `Scopes`: 토큰을 사용 하 여 부여 된 범위입니다. 부여 된 범위에는 요청한 범위의 하위 집합일 수 있습니다.

MSAL에서는 대 한 추상화를 `Account`입니다. `Account` 현재 사용자의 로그인 계정을 나타냅니다.

- `HomeAccountIdentifier`: 사용자의 홈 테 넌 트의 식별자입니다.
- `UserName`: 사용자의 기본 사용자 이름입니다. 이 Azure Active Directory B2C 사용자에 대 한 비어 있을 수 있습니다.
- `AccountIdentifier`: 로그인 한 사용자의 식별자입니다. 이 값은 동일 합니다 `HomeAccountIdentifier` 대부분의 경우 사용자가 다른 테 넌 트의 게스트 값입니다.

## <a name="call-an-api"></a>API를 호출 합니다.

액세스 토큰을 사용 하는 다음 웹 API를 호출 하기 쉽습니다. 앱 토큰을 사용 하 여 HTTP 요청을 만들어 다음 요청을 실행 합니다.

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>여러 API 요청

동일한 API를 여러 번 호출 하는 경우 또는 여러 Api를 호출 하는 경우 응용 프로그램을 빌드할 때 고려 사항에는 다음을 수행 합니다.

- **증분 동의**: Microsoft id 플랫폼에는 앱을 사용 권한은 필요한 것이 아니라 때 시작할 때 모든 사용자 동의 가져올 수 있습니다. 앱이 API를 호출할 준비가 될 때마다 사용 해야 하는 범위만 요청 해야 합니다.
- **조건부 액세스**: 특정 시나리오에서 몇 가지 API 요청을 하는 경우 추가 조건부 액세스 요구 사항에 발생할 수 있습니다. 이 첫 번째 요청에 적용 된 조건부 액세스 정책이 없습니다 및 앱을 자동으로 조건부 액세스 해야 하는 새 API에 액세스 하려고 하는 경우 발생할 수 있습니다. 이 시나리오를 처리 하려면 사용할 자동 요청에서 오류를 catch 하 고 대화형 요청을 수행 하도록 준비 해야 합니다.  자세한 내용은 참조 하세요 [조건부 액세스를 위한 지침](conditional-access-dev-guide.md)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-mobile-production.md)

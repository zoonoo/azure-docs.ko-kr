---
title: 웹 api를 호출 하는 모바일 앱-web API 호출 | Microsoft id 플랫폼
description: Web api를 호출 하는 모바일 앱을 빌드하는 방법 알아보기
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1408c06570babfd93c46fdfc7a3c6754000bcbc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320844"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>웹 api를 호출 하는 모바일 앱-web API 호출

앱이 사용자에 게 로그인 하 고 토큰을 받은 후 MSAL은 사용자, 사용자 환경 및 발급 된 토큰에 대 한 여러 정보를 노출 합니다. 앱은 이러한 값을 사용 하 여 web API를 호출 하거나 사용자에 게 환영 메시지를 표시할 수 있습니다.

먼저 MSAL 결과를 살펴보겠습니다. 그런 다음 `AuthenticationResult` 또는 `result` 에서 액세스 토큰을 사용 하 여 보호 된 웹 API를 호출 하는 방법을 살펴보겠습니다.

## <a name="msal-result"></a>MSAL 결과
MSAL은 다음 값을 제공 합니다. 

- `AccessToken`: HTTP 전달자 요청에서 보호 된 웹 Api를 호출 하는 데 사용 됩니다.
- `IdToken`: 사용자 이름, 홈 테 넌 트 및 저장소에 대 한 고유 식별자와 같은 로그인 한 사용자에 대 한 유용한 정보를 포함 합니다.
- `ExpiresOn`: 토큰의 만료 시간입니다. MSAL은 앱에 대 한 자동 새로 고침을 처리 합니다.
- `TenantId`: 사용자가 로그인 한 테 넌 트의 식별자입니다. 게스트 사용자 (Azure Active Directory B2B)의 경우이 값은 사용자의 홈 테 넌 트가 아니라 사용자가 로그인 한 테 넌 트를 식별 합니다.  
- `Scopes`: 토큰과 함께 부여 된 범위입니다. 부여 된 범위는 요청한 범위의 하위 집합일 수 있습니다.

또한 MSAL은에 대 한 `Account`추상화를 제공 합니다. 는 `Account` 현재 사용자의 로그인 된 계정을 나타냅니다.

- `HomeAccountIdentifier`: 사용자의 홈 테 넌 트 식별자입니다.
- `UserName`: 사용자의 기본 설정 사용자 이름입니다. Azure Active Directory B2C 사용자에 게는 비어 있을 수 있습니다.
- `AccountIdentifier`: 로그인 한 사용자의 식별자입니다. 사용자가 다른 테 넌 트의 게스트가 `HomeAccountIdentifier` 아니면이 값은 대부분의 경우의 값과 동일 합니다.

## <a name="call-an-api"></a>API 호출

액세스 토큰을 만든 후에는 web API를 호출 하는 것이 쉽습니다. 앱은 토큰을 사용 하 여 HTTP 요청을 생성 한 다음 요청을 실행 합니다.

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

## <a name="making-several-api-requests"></a>여러 API 요청 만들기

동일한 API를 여러 번 호출 해야 하거나 여러 Api를 호출 해야 하는 경우에는 앱을 빌드할 때 다음 사항을 고려 하세요.

- **증분 승인**: Microsoft id 플랫폼을 사용 하면 앱이 시작 시가 아니라 권한이 필요 하므로 사용자 동의를 가져올 수 있습니다. 앱이 API를 호출할 준비가 될 때마다 사용 해야 하는 범위만 요청 해야 합니다.
- **조건부 액세스**: 특정 시나리오에서는 여러 API 요청을 만들 때 조건부 액세스 요구 사항이 추가로 발생할 수 있습니다. 첫 번째 요청에 적용 된 조건부 액세스 정책이 없고 앱에서 조건부 액세스가 필요한 새 API에 자동으로 액세스 하려고 하는 경우이 문제가 발생할 수 있습니다. 이 시나리오를 처리 하려면 자동 요청에서 오류를 catch 하 고 대화형 요청을 수행할 준비를 해야 합니다.  자세히 알아보려면 [조건부 액세스에 대 한 지침](conditional-access-dev-guide.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-mobile-production.md)

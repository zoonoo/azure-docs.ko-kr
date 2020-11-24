---
title: 응용 프로그램에서 지속적인 액세스 평가를 사용 하는 Api를 사용 하는 방법 | Microsoft
titleSuffix: Microsoft identity platform
description: 지속적인 액세스 평가 지원을 추가 하 여 앱 보안 및 복원 력을 높이는 방법으로, 중요 한 이벤트 및 정책 평가에 따라 취소할 수 있는 수명이 긴 액세스 토큰을 사용할 수 있습니다.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 975c92256ea0993badde0faf840a939f42901059
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753700"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>응용 프로그램에서 지속적인 액세스 평가를 사용 하도록 설정 된 Api를 사용 하는 방법

CAE ( [연속 액세스 평가](../conditional-access/concept-continuous-access-evaluation.md) )는 수명에 따라 토큰 만료에 의존 하는 대신 [중요 한 이벤트](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) 및 [정책 평가](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) 에 따라 액세스 토큰을 해지할 수 있게 해 주는 새로운 업계 표준입니다. 일부 리소스 Api의 경우 위험 및 정책이 실시간으로 평가 되기 때문에 토큰 수명이 최대 28 시간까지 늘어날 수 있습니다. 이러한 수명이 긴 토큰은 MSAL (Microsoft Authentication Library)을 통해 사전에 새로 고쳐 응용 프로그램의 복원 력을 향상 시킵니다.

이 문서에서는 응용 프로그램에서 CAE 사용 Api를 사용 하는 방법을 보여 줍니다.

## <a name="implementation-considerations"></a>구현 고려 사항

연속 액세스 평가를 사용 하려면 앱 및 액세스 하는 리소스 API 모두 CAE를 사용 하도록 설정 해야 합니다. 그러나 CAE 사용 리소스를 사용 하도록 코드를 준비 해도 CAE 사용 되지 않는 Api를 사용 하는 것은 차단 되지 않습니다. 

리소스 API가 CAE을 구현 하 고 응용 프로그램에서 CAE를 처리할 수 있도록 선언 하는 경우 앱은 해당 리소스에 대 한 CAE 토큰을 가져옵니다. 이러한 이유로 CAE ready 앱을 선언 하는 경우 응용 프로그램은 Microsoft Id 액세스 토큰을 수락 하는 모든 리소스 Api에 대 한 CAE 클레임 챌린지를 처리 해야 합니다. 이러한 API 호출에서 CAE 응답을 처리 하지 않는 경우 앱은 여전히 토큰의 반환 된 수명 동안 토큰을 사용 하 여 API 호출을 다시 시도 하지만 CAE로 인해 해지 된 루프에서 종료 될 수 있습니다. 

## <a name="the-code"></a>코드

첫 번째 단계는 CAE로 인해 호출을 거부 하는 리소스 API의 응답을 처리 하는 코드를 추가 하는 것입니다. CAE를 사용 하는 경우 Api는 액세스 토큰이 해지 되었거나 API에서 사용 되는 IP 주소 변경을 감지한 경우 401 상태 및 WWW-Authenticate 헤더를 반환 합니다. WWW-Authenticate 헤더는 응용 프로그램이 새 액세스 토큰을 획득 하는 데 사용할 수 있는 클레임 챌린지를 포함 합니다.

예를 들면 다음과 같습니다.

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
 authorization_uri="https://login.windows.net/common/oauth2/authorize",
 error="insufficient_claims",
 claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

앱에서 다음을 확인 합니다.

- 401 상태를 반환 하는 API 호출
- 다음을 포함 하는 WWW-Authenticate 헤더가 존재 합니다.
  - "insufficient_claims" 값을 포함 하는 "error" 매개 변수
  - "클레임" 매개 변수

이러한 조건이 충족 되 면 앱에서 클레임 챌린지를 추출 하 고 디코딩할 수 있습니다.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // . . .
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

그러면 앱에서 클레임 챌린지를 사용 하 여 리소스에 대 한 새 액세스 토큰을 획득 합니다.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // . . .
```

응용 프로그램이 CAE 사용 리소스에서 반환 된 클레임 챌린지를 처리할 준비가 되 면 앱을 CAE ready로 Microsoft Id에 지시할 수 있습니다. MSAL 응용 프로그램에서이 작업을 수행 하려면 "cp1"의 클라이언트 기능을 사용 하 여 공용 클라이언트를 빌드합니다.

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

응용 프로그램에 사용자를 로그인 하 여 응용 프로그램을 테스트 한 다음 Azure Portal를 사용 하 여 사용자 세션을 해지할 수 있습니다. 다음 번에 앱이 CAE 사용 API를 호출 하면 사용자에 게 다시 인증 하 라는 메시지가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [연속 액세스 평가](/conditional-access/concept-continuous-access-evaluation.md)를 참조 하세요.

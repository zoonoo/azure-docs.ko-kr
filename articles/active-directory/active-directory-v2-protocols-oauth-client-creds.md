
<properties
    pageTitle="Azure AD v2.0 OAuth 클라이언트 자격 증명 흐름 | Microsoft Azure"
    description="OAuth 2.0 인증 프로토콜의 Azure AD의 구현을 사용하여 웹 응용 프로그램을 빌드합니다."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dastrock"/>


# <a name="v2.0-protocols---oauth-2.0-client-credentials-flow"></a>v2.0 프로토콜 - OAuth 2.0 클라이언트 자격 증명 흐름

때로 "양각 OAuth"라고 하는 [OAuth 2.0 클라이언트 자격 증명 권한 부여](http://tools.ietf.org/html/rfc6749#section-4.4)는 응용 프로그램의 ID를 사용하여 웹 호스팅 리소스에 액세스하는 데 사용될 수 있습니다.  이 기능은 최종 사용자의 즉각적인 존재 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 일반적으로 사용됩니다.  이러한 유형의 응용 프로그램은 종종 **디먼** 또는 **서비스 계정**이라고 합니다.

> [AZURE.NOTE]
    일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다.  v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

더 일반적인 세 개의 "삼각 OAuth"에서 클라이언트 응용 프로그램에는 특정 사용자를 대신하여 리소스에 액세스할 수 있는 사용 권한이 부여됩니다.  사용 권한은 일반적으로 **동의** 프로세스 동안 사용자에게서 응용 프로그램에 [위임](active-directory-v2-scopes.md) 됩니다.  그러나 클라이언트 자격 증명 흐름에서는 응용 프로그램 자체에 **직접** 사용 권한이 부여됩니다.  앱이 리소스에 대한 토큰을 제공하는 경우 리소스는 일부 사용자에 권한을 부여하지 않고 앱 자체에 작업을 수행할 수 있는 권한을 부여합니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램
전체 클라이언트 자격 증명 흐름은 다음과 같습니다. 각 단계를 아래에서 자세히 설명합니다.

![클라이언트 자격 증명 흐름](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>직접 권한 부여 가져오기 
일반적으로 앱이 리소스에 액세스할 수 있는 직접 권한을 부여받는 두 가지 방법이 있습니다. 리소스에서 액세스 제어 목록을 통하거나 Azure AD에서 응용 프로그램 사용 권한 할당을 통해서입니다.  리소스가 해당 클라이언트를 인증하도록 선택하고 각 리소스 서버가 해당 응용 프로그램에 가장 적합한 메서드를 선택할 수 있는 여러 가지 방법이 있습니다.  이 두 메서드는 Azure AD에서 가장 일반적이며 클라이언트 자격 증명 흐름을 수행하려는 클라이언트 및 리소스에 사용하는 것이 좋습니다.

### <a name="access-control-lists"></a>액세스 제어 목록
지정된 리소스 공급자는 몇 가지 특정 수준의 액세스를 알고 권한을 부여하는 응용 프로그램 ID 목록에 따라 권한 부여 확인을 적용할 수 있습니다.  리소스가 v2.0 끝점에서 토큰을 받으면 토큰을 디코딩하고 `appid` 및 `iss` 클레임에서 클라이언트의 응용 프로그램 ID를 추출할 수 있습니다.  그러면 유지 관리하는 일부 ACL(액세스 제어 목록)에 대해 해당 응용 프로그램을 비교할 수 있습니다.  액세스 제어 세분성 및 메서드 목록은 리소스 간에 크게 달라질 수 있습니다.

이러한 ACL에 대한 일반적인 사용 사례는 웹 응용 프로그램 또는 Web API에 대한 테스트 러너입니다.  Web API는 다양한 클라이언트에 대한 모든 사용 권한의 하위 집합만을 부여할 수 있습니다.  API에 종단 간 테스트를 실행하기 위해 v2.0 끝점에서 토큰을 획득하고 API에 전달할 테스트 클라이언트를 만듭니다.  그러면 API는 API의 전체 기능에 대한 전체 액세스를 위한 테스트 클라이언트의 응용 프로그램 ID를 ACL합니다.  해당 서비스에 그러한 목록이 있으면 호출자 `appid`의 유효성을 확인할 뿐만 아니라 토큰의 `iss`를 신뢰할 수 있는지, 그 유효성을 확인해야 합니다.

이 종류의 권한 부여는 개인 Microsoft 계정 소비자 사용자가 소유한 데이터에 액세스해야 하는 데몬 및 서비스 계정에 일반적입니다.  조직에서 소유한 데이터의 경우 응용 프로그램 사용 권한을 통해 필요한 권한을 획득하는 것이 좋습니다.

### <a name="application-permissions"></a>응용 프로그램 사용 권한
API는 ACL을 사용하는 대신 응용 프로그램에 권한을 부여할 수 있는 **응용 프로그램 사용 권한** 집합을 노출할 수 있습니다.  응용 프로그램 사용 권한은 조직의 관리자가 응용 프로그램에 부여하며 해당 조직 및 그 직원이 소유한 데이터 액세스에만 사용할 수 있습니다.  예를 들어 Microsoft Graph는 몇 가지 응용 프로그램 사용 권한을 노출합니다.

- 모든 사서함에서 메일 읽기
- 모든 사서함에서 메일 읽기 및 쓰기
- 모든 사용자로 메일 보내기
- 디렉터리 데이터 읽기
- [+ 자세히](https://graph.microsoft.io)

앱에서 이러한 사용 권한을 획득하기 위해 다음 단계를 수행할 수 있습니다.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>앱 등록 포털에서 사용 권한 요청

- [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 응용 프로그램으로 이동하거나 응용 프로그램이 없는 경우 [응용 프로그램을 만듭니다](active-directory-v2-app-registration.md).  응용 프로그램이 하나 이상의 응용 프로그램 암호를 만들었는지 확인해야 합니다.
- **응용 프로그램 직접 사용 권한** 섹션으로 이동하여 앱에 필요한 사용 권한을 추가합니다.
- 앱 등록을 **저장** 해야 합니다.

#### <a name="recommended:-sign-the-user-into-your-app"></a>사용자가 앱에 로그인하는 것이 좋습니다.

일반적으로 응용 프로그램 사용 권한을 사용하는 응용 프로그램을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지/ 보기가 포함되어야 합니다.  이 페이지는 앱의 등록 흐름, 앱의 설정 또는 전용 "연결" 흐름의 일부일 수 있습니다.  대부분의 경우에 사용자가 회사 또는 학교 Microsoft 계정으로 로그인한 후에 앱은 이 "연결" 보기만을 표시하게 됩니다.

사용자가 앱에 로그인하면 사용자에게 응용 프로그램 사용 권한을 승인하도록 요청하기 전에 사용자가 속해 있는 조직을 식별할 수 있습니다.  반드시 필요하지는 않지만 조직 사용자를 위한 직관적인 환경을 만들 수 있습니다.  사용자가 로그인하려면 [v2.0 프로토콜 자습서](active-directory-v2-protocols.md)를 수행합니다.

#### <a name="request-the-permissions-from-a-directory-admin"></a>디렉터리 관리에서 사용 권한 요청

회사의 관리에서 사용 권한을 요청할 준비가 되면 v 2.0 **관리 동의 끝점**에 사용자를 리디렉션할 수 있습니다.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 사용 권한을 요청하려는 디렉터리 테넌트입니다.  GUID 또는 친숙한 이름 형식으로 제공할 수 있습니다.  사용자가 속한 테넌트가 무엇인지 모르고 테넌트를 사용하여 로그인하지 않으려는 경우 `common`을 사용합니다. |
| client_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| redirect_uri | 필수 | redirect_uri는 처리할 앱에 응답을 전송하려는 위치입니다.  URL로 인코딩되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 하며 추가 경로 세그먼트가 있을 수 있습니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다.  원하는 모든 콘텐츠의 문자열일 수 있습니다.  상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |

이 시점에서 Azure AD는 테넌트 관리자가 요청을 완료하기 위해 로그인하도록 강제합니다.  관리자에게는 등록 포털에서 앱에 요청한 응용 프로그램 직접 사용 권한을 모두 승인하라는 메시지가 표시됩니다. 

##### <a name="successful-response"></a>성공적인 응답
관리자가 응용 프로그램에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 응용 프로그램에 부여합니다. |
| state | 토큰 응답에도 반환되는 요청에 포함된 값입니다.  원하는 모든 콘텐츠의 문자열일 수 있습니다.  상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| admin_consent | `True`로 설정합니다. |


##### <a name="error-response"></a>오류 응답
관리자가 응용 프로그램에 대한 사용 권한을 승인하지 않는 경우 실패한 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description | 개발자가 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다.  |

앱 프로비전 끝점에서 성공적인 응답을 받았다면 앱은 요청한 응용 프로그램 직접 사용 권한을 얻게 됩니다.  이제 원하는 리소스에 대한 토큰을 요청하는 단계로 이동합니다.

## <a name="get-a-token"></a>토큰 가져오기
응용 프로그램에 필요한 권한을 부여받았다면 API에 대한 액세스 토큰을 획득하는 과정을 진행할 수 있습니다.  클라이언트 자격 증명 권한 부여를 사용하여 토큰을 가져오려면 POST 요청을 `/token` v2.0 끝점에 보냅니다.

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| client_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| scope | 필수 | 이 요청에서 `scope` 매개 변수에 전달된 값은 원하는 리소스의 리소스 식별자(앱 ID URI)여야 하고 `.default` 접미사가 붙어 있어야 합니다.  지정된 Microsoft Graph 예제의 경우 값은 `https://graph.microsoft.com/.default`이어야 합니다.  이 값은 앱에 구성한 모든 응용 프로그램 직접 사용 권한의 v2.0 끝점을 알려주며 원하는 리소스에 관련된 사용 권한의 토큰을 발급해야 합니다. |
| client_secret | 필수 | 앱에 대한 등록 포털에서 만든 응용 프로그램 암호입니다. |
| grant_type | 필수 | `client_credentials`이어야 합니다. | 

#### <a name="successful-response"></a>성공적인 응답
성공적인 응답은 다음과 같은 형식을 따릅니다.

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access_token | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스를 인증할 수 있습니다. |
| token_type | 토큰 유형 값을 나타냅니다. Azure AD는 `Bearer` 유형만 지원합니다.  |
| expires_in | 액세스 토큰이 유효한 기간(초)입니다. |

#### <a name="error-response"></a>오류 응답
오류 응답은 다음과 같은 형식을 따릅니다.

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다.  |
| error_codes | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다.  |
| timestamp | 오류가 발생한 시간입니다. |
| trace_id | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다.  |
| correlation_id | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

## <a name="use-a-token"></a>토큰 사용
토큰을 획득했으므로 해당 토큰을 사용하여 리소스에 요청할 수 있습니다.  토큰이 만료되면 `/token` 끝점에 대한 요청을 반복하여 새 액세스 토큰을 획득합니다.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>코드 샘플
관리 동의 끝점을 사용하여 client_credentials 권한 부여를 구현하는 응용 프로그램의 예를 보려면 [v2.0 데몬 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)을 참조하세요.


<!--HONumber=Oct16_HO2-->



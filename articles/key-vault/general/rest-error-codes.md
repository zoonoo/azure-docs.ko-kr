---
title: REST API 오류 코드 - Azure 키 볼트
description: 이러한 오류 코드는 Azure Key Vault 웹 서비스의 작업에서 반환될 수 있습니다.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432061"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure 키 볼트 REST API 오류 코드
 
Azure Key Vault 웹 서비스의 작업에서 다음 오류 코드를 반환할 수 있습니다.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: 인증되지 않은 요청

401은 키 볼트에 대한 요청이 인증되지 않은 것을 의미합니다. 

다음과 같은 경우 요청이 인증됩니다.

- 키 볼트는 호출자의 ID를 알고 있습니다. 및
- 호출자는 키 볼트 리소스에 액세스하려고 시도할 수 있습니다. 

요청이 401을 반환할 수 있는 데는 여러 가지 이유가 있습니다.

### <a name="no-authentication-token-attached-to-the-request"></a>요청에 연결된 인증 토큰이 없습니다. 

다음은 비밀의 값을 설정하는 PUT 요청 예제입니다.

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

"권한 부여" 헤더는 데이터 평면 작업에 대한 Key Vault 호출마다 필요한 액세스 토큰입니다. 헤더가 없는 경우 응답은 401이어야 합니다.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>토큰에 연결된 올바른 리소스가 없습니다. 

Azure OAUTH 끝점에서 액세스 토큰을 요청할 때 "리소스"라는 매개 변수가 필수입니다. 토큰은 토큰의 용도에 대한 범위를 정하기 때문에 토큰 공급자에게 중요한 값입니다. 키 볼트에 액세스하는 **모든** 토큰에 대한 리소스는 *https:\//vault.keyvault.net(후행* 슬래시 없음)입니다.

### <a name="the-token-is-expired"></a>토큰이 만료되었습니다.

토큰은 base64 인코딩되며 값은 와 같은 [http://jwt.calebb.net](http://jwt.calebb.net)웹 사이트에서 디코딩될 수 있습니다. 위의 토큰은 다음과 같습니다.

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

이 토큰에서 많은 중요한 부분을 볼 수 있습니다.

- aud (청중): 토큰의 리소스입니다. 이 것입니다. <https://vault.azure.net> 이 토큰은 그래프와 같이 이 값과 명시적으로 일치하지 않는 리소스에서는 작동하지 않습니다.
- iat (발행): 토큰이 발행된 시기가 시작된 이후의 틱 수입니다.
- nbf (이전이 아님): 이 토큰이 유효해지면 시대가 시작된 이후의 틱 수입니다.
- exp(만료): 이 토큰이 만료되는 시기가 시작된 이후의 틱 수입니다.
- appid(응용 프로그램 ID): 이 요청을 하는 응용 프로그램 ID에 대한 GUID입니다.
- tid(테넌트 ID): 이 요청을 하는 주 체의 테넌트 ID에 대한 GUID

요청이 작동하려면 토큰에서 모든 값을 올바르게 식별하는 것이 중요합니다. 모든 것이 정확하면 요청이 401이 되지 않습니다.

### <a name="troubleshooting-401"></a>문제 해결 401

401s는 키 자격 증명 모음에 대한 요청이 이루어지기 전에 토큰 생성 지점에서 조사해야 합니다. 일반적으로 코드는 토큰을 요청하는 데 사용됩니다. 토큰이 수신되면 키 볼트 요청으로 전달됩니다. 코드가 로컬로 실행되는 경우 Fiddler를 사용하여 에 대한 `https://login.microsoftonline.com`요청/응답을 캡처할 수 있습니다. 요청은 다음과 같습니다.

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

다음 사용자 제공 정보 mush 올바른:

- 키 볼트 테넌트 ID
- 리소스 값은 https%3A%2F%2Fvault.azure.net(URL 인코딩됨)으로 설정됨
- 클라이언트 ID
- 클라이언트 암호

요청의 나머지 가 거의 동일한지 확인합니다.

응답 액세스 토큰만 얻을 수 있는 경우 테넌트 ID, 클라이언트 ID(앱 ID) 및 리소스를 보장하기 위해 응답 액세스 토큰을 디코딩할 수 있습니다(위에 표시된 대로).

## <a name="http-403-insufficient-permissions"></a>HTTP 403: 사용 권한 부족

HTTP 403은 요청이 인증되었지만(요청하는 ID를 알고 있음) ID에 요청된 리소스에 액세스할 수 있는 권한이 없습니다. 두 가지 원인이 있습니다.

- ID에 대한 액세스 정책이 없습니다.
- 요청 리소스의 IP 주소는 키 자격 증명 모음의 방화벽 설정에 제외되지 않습니다.

HTTP 403은 고객의 응용 프로그램이 고객이 생각하는 클라이언트 ID를 사용하지 않는 경우가 많습니다. 즉, 일반적으로 액세스 정책이 실제 호출 ID에 대해 올바르게 설정되지 않음을 의미합니다.

### <a name="troubleshooting-403"></a>문제 해결 403

먼저 로깅을 켭니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure 키 볼트 로깅)을](logging.md)참조하십시오.

로깅이 켜지면 403이 액세스 정책 또는 방화벽 정책 때문인지 확인할 수 있습니다.

#### <a name="error-due-to-firewall-policy"></a>방화벽 정책으로 인한 오류

"클라이언트 주소(00.00.00.00)가 승인되지 않았으며 호출자는 신뢰할 수 있는 서비스가 아닙니다."

"Azure 신뢰할 수 있는 서비스"의 제한된 목록이 있습니다. Azure 웹 사이트는 신뢰할 수 있는 Azure 서비스가 **아닙니다.** 자세한 내용은 Azure 앱 [서비스에서](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)키 볼트 방화벽 액세스 에 대한 블로그 게시물을 참조하십시오.

Azure 웹 사이트의 IP 주소가 작동하려면 키 자격 증명 모음에 추가해야 합니다.

액세스 정책으로 인한 경우: 요청에 대한 개체 ID를 찾아 개체 ID가 사용자가 액세스 정책을 할당하려는 개체와 일치하는지 확인합니다. AAD에는 이름이 같은 개체가 여러 개 있는 경우가 많므로 올바른 개체를 선택하는 것이 매우 중요합니다. 액세스 정책을 삭제하고 다시 추가하면 동일한 이름의 여러 개체가 있는지 확인할 수 있습니다.

또한 대부분의 액세스 정책은 포털에 표시된 대로 "권한 있는 응용 프로그램"을 사용할 필요가 없습니다. 인증된 응용 프로그램은 드문 "대신" 인증 시나리오에 사용됩니다. 


## <a name="http-429-too-many-requests"></a>HTTP 429: 요청이 너무 많습니다.

제한은 요청 수가 기간에 대해 명시된 최대값을 초과할 때 발생합니다. 제한이 발생하면 키 볼트의 응답은 HTTP 429가 됩니다. 요청 유형에 대해 명시된 최대값입니다. 예를 들어 HSM 2048비트 키를 만드는 것은 10초당 5개의 요청이지만 다른 모든 HSM 트랜잭션에는 1000개의 요청/10초 제한이 있습니다. 따라서 제한의 원인을 결정할 때 어떤 유형의 호출이 이루어지는지 이해하는 것이 중요합니다.
일반적으로 키 볼트에 대한 요청은 10초당 2,000개의 요청으로 제한됩니다. Key [Vault 서비스 제한에](service-limits.md) 설명된 대로 예외는 키 작업입니다.

### <a name="troubleshooting-429"></a>문제 해결 429
제한은 다음 기술을 사용하여 해결됩니다.

- 요청된 리소스에 패턴이 있는지 확인 하 고 호출 응용 프로그램에서 캐시 하려고 시도 하 여 Key Vault에 대 한 요청 수를 줄일 수 있습니다. 

- 키 볼트 제한이 발생하면 다시 시도할 때 지수 백오프를 사용하도록 요청 코드를 조정합니다. 알고리즘은 여기에 설명되어 [있습니다: 앱을 제한하는 방법](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- 캐싱으로 요청 수를 줄일 수 없고 시간 지정 백오프가 작동하지 않는 경우 키를 여러 키 볼트로 분할하는 것이 좋습니다. 단일 구독의 서비스 제한은 개별 키 볼트 한도의 5배입니다. 5개 이상의 키 볼트를 사용하는 경우 여러 구독을 사용하는 것을 고려해야 합니다. 

한도 증가 요청을 포함한 자세한 지침은 여기에서 찾을 수 있습니다: [키 볼트 제한 지침](overview-throttling.md)



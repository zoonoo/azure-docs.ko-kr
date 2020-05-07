---
title: Microsoft id 플랫폼 인증서 자격 증명
titleSuffix: Microsoft identity platform
description: 이 문서에서는 응용 프로그램 인증을 위해 인증서 자격 증명을 등록 하 고 사용 하는 방법을 설명 합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a35f70251622674205a28af9b7cc64132d0530
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690277"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft id 플랫폼 응용 프로그램 인증 인증서 자격 증명

Microsoft id 플랫폼을 사용 하면 응용 프로그램에서 인증을 위해 자체 자격 증명을 사용할 수 있습니다. 예를 들어 [OAuth 2.0 클라이언트 자격 증명 부여 flowv 2.0](v2-oauth2-client-creds-grant-flow.md) 및 [흐름에](v2-oauth2-on-behalf-of-flow.md)대 한 사용자 자격 증명을 사용할 수 있습니다.

애플리케이션이 인증에 사용할 수 있는 자격 증명의 한 가지 형태는 애플리케이션에서 소유한 인증서로 서명된 JWT(JSON Web Token) 어설션입니다.

## <a name="assertion-format"></a>어설션 형식
Microsoft identity platform 어설션을 계산 하려면 선택한 언어로 된 많은 [JSON Web Token](https://jwt.ms/) 라이브러리 중 하나를 사용할 수 있습니다. 토큰에 의해 전달되는 정보는 다음과 같습니다.

### <a name="header"></a>헤더

| 매개 변수 |  설명 |
| --- | --- |
| `alg` | **RS256**이어야 함 |
| `typ` | **JWT**여야 함 |
| `x5t` | X.509 인증서 SHA-1 지문이어야 함 |

### <a name="claims-payload"></a>클레임(페이로드)

| 매개 변수 |  설명 |
| --- | --- |
| `aud` | 대상: ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | 만료 날짜: 토큰이 만료되는 날짜입니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰의 유효 기간이 만료될 때까지의 시간(초)으로 표시됩니다.|
| `iss` | 발급자: client_id(클라이언트 서비스의 애플리케이션 ID)여야 함 |
| `jti` | GUID: JWT ID |
| `nbf` | 이전이 아님: 토큰을 사용할 수 없게 된 날짜입니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰이 발급된 시간까지 기간(초)으로 표시됩니다. |
| `sub` | 주체: `iss`의 경우 client_id(클라이언트 서비스의 애플리케이션 ID)여야 함 |

### <a name="signature"></a>서명

서명은 [JSON 웹 토큰 RFC7519 사양](https://tools.ietf.org/html/rfc7519)에 설명된 대로 인증서를 적용하여 계산됩니다.

## <a name="example-of-a-decoded-jwt-assertion"></a>디코딩된 JWT 어설션 예제

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>인코딩된 JWT 어설션 예제

다음 문자열은 인코딩된 어설션의 예입니다. 자세히 살펴보면 세 개의 섹션이 점(.)으로 구분된 것을 알 수 있습니다.
* 첫 번째 섹션은 헤더를 인코딩합니다.
* 두 번째 섹션은 페이로드를 인코딩합니다.
* 마지막 섹션은 처음 두 섹션 콘텐츠의 인증서로 계산된 서명입니다.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Microsoft id 플랫폼을 사용 하 여 인증서 등록

다음 방법 중 하나를 사용 하 여 Azure Portal를 통해 Microsoft identity platform의 클라이언트 응용 프로그램과 인증서 자격 증명을 연결할 수 있습니다.

### <a name="uploading-the-certificate-file"></a>인증서 파일 업로드

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **인증서 및 비밀**을 선택합니다.
2. **인증서 업로드** 를 클릭 하 고 업로드할 인증서 파일을 선택 합니다.
3. **추가**를 클릭합니다.
  인증서가 업로드 되 면 지문, 시작 날짜 및 만료 값이 표시 됩니다.

### <a name="updating-the-application-manifest"></a>애플리케이션 매니페스트 업데이트

인증서가 있을 때 다음을 컴퓨팅해야 합니다.

- `$base64Thumbprint` - 인증서 해시의 base64 인코딩
- `$base64Value` - 인증서 원시 데이터의 해시의 base64 인코딩

애플리케이션 매니페스트에서 키를 식별하는 GUID도 제공해야 합니다(`$keyId`).

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **매니페스트** 를 선택 하 여 응용 프로그램 매니페스트를 엽니다.
2. 다음 스키마를 사용해서 *keyCredentials* 속성을 새 인증서 정보로 바꿉니다.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 편집 내용을 응용 프로그램 매니페스트에 저장 한 다음 매니페스트를 Microsoft id 플랫폼에 업로드 합니다.

   `keyCredentials` 속성은 다중 값이므로 풍부한 키 관리를 위해 여러 인증서를 업로드할 수 있습니다.

## <a name="code-sample"></a>코드 샘플

> [!NOTE]
> X5T 헤더는 인증서의 해시를 사용 하 여 기본 64 문자열로 변환 하 여 계산 해야 합니다. C #에서이를 수행 하는 `System.Convert.ToBase64String(cert.GetCertHash());`코드는입니다.

[Microsoft id 플랫폼을 사용 하는 코드 샘플 .Net Core 디먼 콘솔 응용](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 프로그램은 응용 프로그램에서 인증에 자체 자격 증명을 사용 하는 방법을 보여 줍니다. 또한 `New-SelfSignedCertificate` Powershell 명령을 사용하여 [자체 서명된 인증서를 만드는](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) 방법을 보여줍니다. [앱 만들기 스크립트](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)를 활용 및 사용하여 인증서를 만들고, 지문 등을 계산할 수도 있습니다.

---
title: Azure AD의 인증서 자격 증명 | Microsoft Docs
description: 이 문서에서는 애플리케이션 인증을 위한 인증서 자격 증명의 등록 및 사용에 대해 설명합니다.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: celested
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4ee1ce56723e4a2c9ab80c12456bbc1b66f6d5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162801"
---
# <a name="certificate-credentials-for-application-authentication"></a>애플리케이션 인증을 위한 인증서 자격 증명

Azure AD(Azure Active Directory)를 사용하면 애플리케이션에서(예: OAuth 2.0 클라이언트 자격 증명 부여 흐름([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) 및 On-Behalf-Of 흐름([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md))에서) 인증을 위해 자체의 자격 증명을 사용할 수 있습니다.

애플리케이션이 인증에 사용할 수 있는 자격 증명의 한 가지 형태는 애플리케이션에서 소유한 인증서로 서명된 JWT(JSON Web Token) 어설션입니다.

## <a name="assertion-format"></a>어설션 형식
어설션을 계산하려면 선택한 언어로 많은 [JSON Web Token](https://jwt.ms/)(영문) 라이브러리 중 하나를 사용할 수 있습니다. 토큰에 의해 전달되는 정보는 다음과 같습니다.

### <a name="header"></a>헤더

| 매개 변수 |  설명 |
| --- | --- |
| `alg` | **RS256**이어야 함 |
| `typ` | **JWT**여야 함 |
| `x5t` | X.509 인증서 SHA-1 지문이어야 함 |

### <a name="claims-payload"></a>클레임(페이로드)

| 매개 변수 |  설명 |
| --- | --- |
| `aud` | Audience: **https://login.microsoftonline.com/*tenant_Id*/oauth2/token**이어야 함 |
| `exp` | 만료 날짜: 토큰이 만료되는 날짜입니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰의 유효 기간이 만료될 때까지의 시간(초)으로 표시됩니다.|
| `iss` | 발급자: client_id(클라이언트 서비스의 애플리케이션 ID)여야 함 |
| `jti` | GUID: JWT ID |
| `nbf` | 이전이 아님: 토큰을 사용할 수 없게 된 날짜입니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰이 발급된 시간까지 기간(초)으로 표시됩니다. |
| `sub` | 주체: `iss`의 경우 client_id(클라이언트 서비스의 애플리케이션 ID)여야 함 |

### <a name="signature"></a>서명

서명은 [JSON 웹 토큰 RFC7519 사양](https://tools.ietf.org/html/rfc7519)에 설명된 대로 인증서를 적용하여 계산됩니다.

## <a name="example-of-a-decoded-jwt-assertion"></a>디코딩된 JWT 어설션 예제

```
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

## <a name="register-your-certificate-with-azure-ad"></a>Azure AD에 인증서 등록

다음 방법 중 하나를 사용하여 Azure Portal을 통해 Azure AD에서 클라이언트 애플리케이션과 인증서 자격 증명을 연결할 수 있습니다.

### <a name="uploading-the-certificate-file"></a>인증서 파일 업로드

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **설정 > 키**를 선택한 다음, **공개 키 업로드**를 선택합니다. 
2. 업로드하려는 인증서 파일을 선택합니다.
3. **저장**을 선택합니다. 
   
   일단 저장하면 인증서가 업로드되고 지문, 시작 날짜 및 만료 값이 표시됩니다. 

### <a name="updating-the-application-manifest"></a>애플리케이션 매니페스트 업데이트

인증서가 있을 때 다음을 계산해야 합니다.

- `$base64Thumbprint` - 인증서 해시의 base64 인코딩
- `$base64Value` - 인증서 원시 데이터의 해시의 base64 인코딩

애플리케이션 매니페스트에서 키를 식별하는 GUID도 제공해야 합니다(`$keyId`).

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. 애플리케이션 매니페스트를 엽니다.
2. 다음 스키마를 사용해서 *keyCredentials* 속성을 새 인증서 정보로 바꿉니다.

   ```
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
3. 애플리케이션 매니페스트에 편집 내용을 저장한 다음, 매니페스트를 Azure AD에 업로드합니다. 

   `keyCredentials` 속성은 다중 값이므로 풍부한 키 관리를 위해 여러 인증서를 업로드할 수 있습니다.
   
## <a name="code-sample"></a>코드 샘플

[인증서로 디먼 앱에서 Azure AD에 인증](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)의 코드 샘플은 애플리케이션에서 인증에 자체 자격 증명을 사용하는 방법을 보여줍니다. 또한 `New-SelfSignedCertificate` Powershell 명령을 사용하여 [자체 서명된 인증서를 만드는](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) 방법을 보여줍니다. [앱 만들기 스크립트](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md)를 활용 및 사용하여 인증서를 만들고, 지문 등을 계산할 수도 있습니다.

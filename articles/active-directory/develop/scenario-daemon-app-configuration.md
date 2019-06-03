---
title: 디먼 앱 웹 Api 호출 (앱 구성)-Microsoft id 플랫폼
description: 디먼 앱을 빌드하는 방법을 알아봅니다 호출 web Api (응용 프로그램 구성)는
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: d8d377db827a6548c380128624c21f4ae7896aff
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075327"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>호출 웹 Api 코드 구성 되는 데몬 앱

웹 Api를 호출 하는 디먼 응용 프로그램에 대 한 코드를 구성 하는 방법에 알아봅니다.

## <a name="msal-libraries-supporting-daemon-apps"></a>MSAL 라이브러리 지원 디먼 앱

디먼 앱을 지 원하는 Microsoft 라이브러리는 다음과 같습니다.

  MSAL 라이브러리 | 설명
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 디먼 응용 프로그램을 빌드하는 지원 되는 플랫폼은.NET Framework 및.NET Core 플랫폼 (UWP 없습니다, Xamarin.iOS 및 Xamarin.Android 플랫폼으로 하는 데 공용 클라이언트 응용 프로그램 빌드)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 진행 중-공개 미리 보기에서 개발
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 진행 중-공개 미리 보기에서 개발

## <a name="configuration-of-the-authority"></a>인증 기관 구성

디먼 응용 프로그램은 위임 된 권한만 하지만 응용 프로그램 사용 권한을 사용 하지 마십시오는 자신의 *지원 되는 계정 유형* 일 수 없습니다 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정 ( 예를 들어, Skype, Xbox, Outlook.com)* 합니다. 실제로 Microsoft 개인 계정에 대 한 디먼 응용 프로그램에 동의할 수 없는 테 넌 트 관리자가 있습니다. 선택 해야 *내 조직에서 계정을* 또는 *조직에서 계정을*합니다.

따라서 응용 프로그램 구성에 지정 된 인증 기관에는 테 넌 트 ed (테 넌 트 ID 또는 조직과 연결 된 도메인 이름 지정) 이어야 합니다. 다중 테 넌 트 도구를 제공 하 고 ISV 인 경우 사용할 수 있습니다 `organizations`합니다. 하지만 관리자 동의 부여 하는 방법에 고객에 게 설명 해야 염두에서에 둡니다. 참조 [전체 테 넌 트에 대 한 동의 요청](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) 세부 정보에 대 한

## <a name="application-configuration-and-instantiation"></a>응용 프로그램 구성 및 인스턴스화

MSAL 라이브러리에 있는 클라이언트 자격 증명 (암호 또는 인증서)는 기밀 클라이언트 응용 프로그램 생성 매개 변수로 전달 됩니다.

> [!IMPORTANT]
> 응용 프로그램은 콘솔 응용 프로그램을 하는 경우에 디먼 응용 프로그램인 경우 서비스로 실행 해야 기밀 클라이언트 응용 프로그램입니다.

### <a name="msalnet"></a>MSAL.NET

추가 된 [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) 응용 프로그램에 NuGet 패키지.

MSAL.NET 네임 스페이스를 사용 합니다.

```CSharp
using Microsoft.Identity.Client;
```

디먼 응용 프로그램에서 제공 하는 프로그램 `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

응용 프로그램 비밀을 사용 하 여 응용 프로그램을 빌드하는 코드를 다음과 같습니다.

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

인증서를 사용 하 여 응용 프로그램을 빌드하는 코드를 다음과 같습니다.

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [디먼 앱-앱에 대 한 토큰을 얻기](./scenario-daemon-acquire-token.md)
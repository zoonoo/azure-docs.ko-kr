---
title: 웹 Api를 호출 하는 디먼 앱 (앱 구성)-Microsoft identity platform
description: 웹 Api (앱 구성)를 호출 하는 디먼 앱을 빌드하는 방법을 알아봅니다.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277835"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>웹 Api를 호출 하는 디먼 앱-코드 구성

웹 Api를 호출 하는 디먼 응용 프로그램에 대 한 코드를 구성 하는 방법을 알아봅니다.

## <a name="msal-libraries-supporting-daemon-apps"></a>디먼 앱을 지 원하는 MSAL 라이브러리

디먼 앱을 지 원하는 Microsoft 라이브러리는 다음과 같습니다.

  MSAL 라이브러리 | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 디먼 응용 프로그램을 빌드하는 데 지원 되는 플랫폼은 .NET Framework 및 .NET Core 플랫폼 (UWP, Xamarin.ios 및 Xamarin이 아님, 공용 클라이언트 응용 프로그램을 빌드하는 데 사용 되는 플랫폼)입니다.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 개발 진행 중-공개 미리 보기
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 개발 진행 중-공개 미리 보기

## <a name="configuration-of-the-authority"></a>인증 기관 구성

디먼 응용 프로그램은 위임 된 권한을 사용 하지 않지만 응용 프로그램 사용 권한은 지원 되는 *계정 유형은* *조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox,)의 계정일 수 없습니다. Outlook.com)* . 실제로 Microsoft 개인 계정의 디먼 응용 프로그램에 대 한 동의를 부여 하는 테 넌 트 관리자가 없습니다. 조직 *에서* *계정이* 나 계정을 선택 해야 합니다.

따라서 응용 프로그램 구성에 지정 된 기관은 테 넌 트 (조직에 연결 된 테 넌 트 ID 또는 도메인 이름을 지정)로 지정 해야 합니다.

ISV 이며 다중 테 넌 트 도구를 제공 하려는 경우에는를 사용할 `organizations`수 있습니다. 하지만 관리자 동의를 부여 하는 방법에 대해서도 설명 해야 합니다. 자세한 내용은 [전체 테 넌 트에 대 한 동의 요청](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) 을 참조 하세요. 또한 현재 msal `organizations` 에는 클라이언트 자격 증명이 인증서가 아닌 응용 프로그램 암호 인 경우에만 허용 되는 제한 사항이 있습니다. [MSAL.NET 버그 #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891) 를 참조 하세요.

## <a name="application-configuration-and-instantiation"></a>응용 프로그램 구성 및 인스턴스화

MSAL 라이브러리에서 클라이언트 자격 증명 (암호 또는 인증서)은 기밀 클라이언트 응용 프로그램 생성의 매개 변수로 전달 됩니다.

> [!IMPORTANT]
> 응용 프로그램이 서비스로 실행 되는 콘솔 응용 프로그램 이더라도 디먼 응용 프로그램의 경우 기밀 클라이언트 응용 프로그램 이어야 합니다.

### <a name="msalnet"></a>MSAL.NET

[IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 패키지를 응용 프로그램에 추가 합니다.

MSAL.NET 네임 스페이스 사용

```CSharp
using Microsoft.Identity.Client;
```

디먼 응용 프로그램은`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

응용 프로그램 암호를 사용 하 여 응용 프로그램을 빌드하는 코드는 다음과 같습니다.

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

인증서를 사용 하 여 응용 프로그램을 빌드하는 코드는 다음과 같습니다.

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

마지막으로 클라이언트 암호 또는 인증서 대신, 기밀 클라이언트 응용 프로그램은 클라이언트 어설션을 사용 하 여 해당 id를 증명할 수도 있습니다. 이 고급 시나리오는 [클라이언트 어설션에](msal-net-client-assertions.md) 자세히 설명 되어 있습니다.


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
> [앱에 대 한 디먼 앱 획득 토큰](./scenario-daemon-acquire-token.md)

---
title: MSAL 마이그레이션 가이드(MSAL4j)까지의 ADAL | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory 인증 라이브러리(ADAL) Java 앱을 MSAL(Microsoft 인증 라이브러리)으로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2929b94a2cb624b96649292714fe93dea09a2085
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886503"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Java에 대한 MSAL 마이그레이션 가이드에 대한 ADAL

이 문서에서는 ADAL(Azure Active Directory 인증 라이브러리)을 사용하여 MSAL(Microsoft 인증 라이브러리)을 사용하는 앱을 마이그레이션하는 데 필요한 변경 사항을 간중합니다.

Java용 Microsoft 인증 라이브러리(MSAL4J)와 Java용 Azure AD 인증 라이브러리(ADAL4J)는 모두 Azure AD 엔터티를 인증하고 Azure AD에서 토큰을 요청하는 데 사용됩니다. 지금까지 대부분의 개발자는 Azure AD 인증 라이브러리(ADAL)를 사용하여 토큰을 요청하여 Azure AD ID(회사 및 학교 계정)를 인증하기 위해 개발자 플랫폼(v1.0)을 위해 Azure AD와 협력해 왔습니다.

MSAL은 다음과 같은 이점을 제공합니다.

- 최신 Microsoft ID 플랫폼 끝점을 사용하기 때문에 Azure AD ID, Microsoft 계정 및 Azure AD Business to Consumer(소비자)를 통해 소셜 및 로컬 계정과 같은 광범위한 Microsoft ID 집합을 인증할 수 있습니다.
- 사용자는 최상의 Single-Sign-On 환경을 제공합니다.
- 응용 프로그램에서 증분 동의를 활성화할 수 있으며 조건부 액세스를 지원하는 것이 더 쉽습니다.

Java용 MSAL은 Microsoft ID 플랫폼과 함께 사용하는 것이 좋습니다. ADAL4J에는 새로운 기능이 구현되지 않습니다. 앞으로의 모든 노력은 MSAL 개선에 초점을 맞추고 있습니다.

## <a name="differences"></a>차이점

개발자(v1.0) 엔드포인트(및 ADAL4J)를 위해 Azure AD로 작업한 경우 [Microsoft ID 플랫폼(v2.0) 끝점의 다른 점을](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)읽어보시길 원할 수 있습니다.

## <a name="scopes-not-resources"></a>리소스가 아닌 범위

ADAL4J는 리소스에 대한 토큰을 획득하는 반면 Java용 MSAL은 범위에 대한 토큰을 획득합니다. Java 클래스에 대한 여러 MSAL에는 범위 매개 변수가 필요합니다. 이 매개 변수는 요청된 원하는 사용 권한 및 리소스를 선언하는 문자열 목록입니다. 예제 범위를 보려면 [Microsoft 그래프의 범위를](https://docs.microsoft.com/graph/permissions-reference) 참조하십시오.

## <a name="core-classes"></a>핵심 클래스

ADAL4J에서 클래스는 `AuthenticationContext` 기관을 통해 보안 토큰 서비스(STS) 또는 권한 부여 서버에 대한 연결을 나타냅니다. 그러나 Java용 MSAL은 클라이언트 응용 프로그램을 중심으로 설계되었습니다. 클라이언트 응용 프로그램을 `PublicClientApplication` 나타내는 `ConfidentialClientApplication` 두 개의 별도 클래스를 제공합니다.  후자의 `ConfidentialClientApplication`은 데몬 앱에 대한 응용 프로그램 식별자와 같은 비밀을 안전하게 유지 하도록 설계된 응용 프로그램을 나타냅니다.

다음 표에서는 ADAL4J 함수가 Java 함수에 대한 새 MSAL에 매핑하는 방법을 보여 주며 있습니다.

| ADAL4J 방법| MSAL4J 방법|
|------|-------|
|acquireToken(문자열 리소스, 클라이언트 자격 증명 자격 증명, 인증 호출 호출 콜백) | acquireToken(클라이언트 자격 증명 매개 변수)|
|acquireToken(문자열 리소스, ClientAssertion 어설션, 인증 호출 호출 콜백)|acquireToken(클라이언트 자격 증명 매개 변수)|
|acquireToken(문자열 리소스, 비대칭Key 자격 증명 자격 증명, 인증 콜백 콜백)|acquireToken(클라이언트 자격 증명 매개 변수)|
|acquireToken (문자열 리소스, 문자열 clientId, 문자열 사용자 이름, 문자열 암호, 인증 호출 콜백 콜백)| acquire토큰(사용자 이름 암호 매개 변수)|
|acquireToken (문자열 리소스, 문자열 clientId, 문자열 사용자 이름, 문자열 암호 = null, 인증 호출 콜백 콜백)|취득토큰(통합윈도우인증매개변수)|
|acquireToken(문자열 리소스, UserAssertion 사용자어설, 클라이언트 자격 증명 자격 증명, 인증 호출 호출 콜백)| 취득 토큰(온비하프매개 변수)|
|acquireTokenBy권한 코드() | acquireToken(권한 부여 코드매개 변수) |
| 취득DeviceCode() 및 획득토큰비디바이스코드()| acquireToken(장치코드매개 변수)|
|취득토큰비리프레시토큰()| acquireToken자동(자동 매개 변수)|

## <a name="iaccount-instead-of-iuser"></a>IUser 대신 I계정

ADAL4J는 사용자를 조작했습니다. 사용자가 단일 사용자 또는 소프트웨어 에이전트를 나타내지만 Microsoft ID 시스템에 하나 이상의 계정이 있을 수 있습니다. 예를 들어 사용자는 여러 Azure AD, Azure AD B2C 또는 Microsoft 개인 계정을 가질 수 있습니다.

Java용 MSAL은 `IAccount` 인터페이스를 통해 계정의 개념을 정의합니다. 이것은 ADAL4J에서 획기적인 변화이지만 동일한 사용자가 여러 계정을 가질 수 있다는 사실과 다른 Azure AD 디렉터리에서도 캡처하기 때문에 좋은 변화입니다. Java용 MSAL은 홈 계정 정보가 제공되므로 게스트 시나리오에서 더 나은 정보를 제공합니다.

## <a name="cache-persistence"></a>캐시 지속성

ADAL4J는 토큰 캐시를 지원하지 않았습니다.
Java용 MSAL은 가능한 경우 만료된 토큰을 자동으로 새로 고침하고 가능한 경우 사용자가 자격 증명을 제공하도록 불필요한 프롬프트를 방지하여 토큰 수명 관리를 간소화하기 위해 [토큰 캐시를](msal-acquire-cache-tokens.md) 추가합니다.

## <a name="common-authority"></a>공통 기관

v1.0에서 `https://login.microsoftonline.com/common` 권한을 사용하는 경우 사용자는 모든 조직에 대해 AAD(Azure Active Directory) 계정으로 로그인할 수 있습니다.

v2.0에서 `https://login.microsoftonline.com/common` 권한을 사용하는 경우 사용자는 AAD 조직 또는 MsA(Microsoft 개인 계정)에 로그인할 수 있습니다. Java용 MSAL에서 AAD 계정에 대한 로그인을 제한하려면 `https://login.microsoftonline.com/organizations` 권한을 사용해야 합니다(ADAL4J와 동일한 동작). 권한을 지정하려면 클래스를 `authority` 만들 때 [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) 메서드에서 `PublicClientApplication` 매개 변수를 설정합니다.

## <a name="v10-and-v20-tokens"></a>v1.0 및 v2.0 토큰

v1.0 엔드포인트(ADAL에서 사용)는 v1.0 토큰만 내보냅니다.

msal에서 사용하는 v2.0 끝점에서는 v1.0 및 v2.0 토큰을 내보엠플니다. Web API의 애플리케이션 매니페스트 속성을 사용하면 개발자가 허용할 토큰 버전을 선택할 수 있습니다. 응용 `accessTokenAcceptedVersion` [프로그램 매니페스트](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) 참조 문서를 참조하십시오.

v1.0 및 v2.0 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰을](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)참조하십시오.

## <a name="adal-to-msal-migration"></a>ADAL에서 MSAL로 마이그레이션

ADAL4J에서는 새로 고침 토큰이 노출되어 개발자가 캐시할 수 있었습니다. 그런 다음 `AcquireTokenByRefreshToken()` 사용자가 더 이상 연결되지 않을 때 사용자를 대신하여 대시보드를 새로 고치는 장기 실행 서비스 구현과 같은 솔루션을 사용하도록 설정하는 데 사용합니다.

Java용 MSAL은 보안상의 이유로 새로 고침 토큰을 노출하지 않습니다. 대신 MSAL은 새로 고침 토큰을 처리합니다.

Java용 MSAL에는 ADAL4j로 획득한 새로 고침 토큰을 clientApplication: [acquireToken(RefreshTokenParameters)로](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)마이그레이션할 수 있는 API가 있습니다. 이 메서드를 사용하면 이전에 사용한 새로 고침 토큰을 원하는 범위(리소스)와 함께 제공할 수 있습니다. 새로 고침 토큰은 새 토큰으로 교환되고 응용 프로그램에서 사용할 수 위해 캐시됩니다.

다음 코드 코드 조각은 기밀 클라이언트 응용 프로그램에서 일부 마이그레이션 코드를 보여 주며 다음과 같은 코드입니다.

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

새 `IAuthenticationResult` 새로 고침 토큰이 캐시에 저장되는 동안 액세스 토큰 및 ID 토큰을 반환합니다. 응용 프로그램에는 이제 IAccount도 포함됩니다.

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

캐시에 있는 토큰을 사용하려면 다음을 호출하십시오.

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```

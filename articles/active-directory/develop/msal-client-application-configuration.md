---
title: 클라이언트 응용 프로그램 구성(MSAL) | Azure
titleSuffix: Microsoft identity platform
description: MICROSOFT 인증 라이브러리(MSAL)를 사용하여 공용 클라이언트 및 기밀 클라이언트 응용 프로그램에 대한 구성 옵션에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534468"
---
# <a name="application-configuration-options"></a>응용 프로그램 구성 옵션

코드에서 토큰을 인증하고 획득하기 위해 새 공용 또는 기밀 클라이언트 응용 프로그램(또는 MSAL.js의 사용자 에이전트)을 초기화합니다. MSAL(Microsoft 인증 라이브러리)에서 클라이언트 앱을 초기화할 때 여러 구성 옵션을 설정할 수 있습니다. 이러한 옵션은 다음 두 그룹으로 나뉩니다.

- 다음을 포함한 등록 옵션:
    - [기관(앱에](#authority) 대한 ID 공급자 [인스턴스](#cloud-instance) 및 로그인 [대상](#application-audience) 및 테넌트 ID로 구성됨).
    - [클라이언트 ID](#client-id).
    - [URI 리디렉션 .](#redirect-uri)
    - [클라이언트 보안(기밀](#client-secret) 클라이언트 응용 프로그램의 경우).
- 로그 수준, 개인 데이터 제어 및 라이브러리를 사용하는 구성 요소 이름을 포함한 [로깅 옵션입니다.](#logging)

## <a name="authority"></a>Authority

권한은 MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 공통 권한은 다음과 같습니다.

- https\:\<//login.microsoftonline.com/\>테넌트 /, 여기서 &lt;테넌트는&gt; Azure Active Directory(Azure AD) 테넌트의 테넌트 ID 또는 이 Azure AD 테넌트와 연결된 도메인입니다. 특정 조직의 사용자에 로그인하는 데만 사용됩니다.
- https\://login.microsoftonline.com/common/. 직장 및 학교 계정 또는 개인 Microsoft 계정으로 사용자를 로그인하는 데 사용됩니다.
- https\://login.microsoftonline.com/organizations/. 직장 및 학교 계정으로 사용자를 로그인하는 데 사용됩니다.
- https\://login.microsoftonline.com/consumers/. 개인 Microsoft 계정만 사용하여 사용자를 로그인하는 데 사용됩니다(이전의 Windows Live ID 계정).

권한 설정은 응용 프로그램 등록 포털에 선언된 내용과 일치해야 합니다.

기관 URL은 인스턴스와 대상 번호로 구성됩니다.

권한은 다음과 같은 것입니다.
- Azure AD 클라우드 기관입니다.
- Azure AD B2C 기관입니다. [B2C 세부 사항을](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)참조하십시오.
- AD FS(활성 디렉터리 페더레이션 서비스) 권한입니다. [AD FS 지원을](https://aka.ms/msal-net-adfs-support)참조하십시오.

Azure AD 클라우드 기관에는 두 부분으로 구성됩니다.
- ID 공급자 *인스턴스*
- 앱의 로그인 *대상*

인스턴스와 대상 을 연결 하 고 기관 URL로 제공 될 수 있습니다. MSAL.NET 버전에서 MSAL 3보다 이전. *x,* 타겟팅하려는 클라우드와 로그인 대상을 기반으로 권한을 직접 구성해야 했습니다.  이 다이어그램은 기관 URL이 구성되는 방법을 보여 주며 있습니다.

![권한 URL구성 방법](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>클라우드 인스턴스

*인스턴스는* 앱이 Azure 퍼블릭 클라우드 또는 국가 클라우드에서 사용자를 서명하는지 지정하는 데 사용됩니다. 코드에서 MSAL을 사용하면 열거형 또는 URL을 `Instance` 멤버로 국가 클라우드 인스턴스에 전달하여 Azure 클라우드 [인스턴스를](authentication-national-cloud.md#azure-ad-authentication-endpoints) 설정할 수 있습니다(알고 있는 경우).

MSAL.NET 둘 다 `Instance` 지정 `AzureCloudInstance` 된 경우 명시적 예외를 throw 합니다.

인스턴스를 지정하지 않으면 앱에서 Azure 공용 클라우드 인스턴스(URL `https://login.onmicrosoftonline.com`인스턴스)를 대상으로 합니다.

## <a name="application-audience"></a>애플리케이션 대상

로그인 대상은 앱의 비즈니스 요구 사항에 따라 달라집니다.
- LOB(비즈니스 라인) 개발자인 경우 조직에서만 사용되는 단일 테넌트 응용 프로그램을 생성할 수 있습니다. 이 경우 테넌트 ID(Azure AD 인스턴스의 ID) 또는 Azure AD 인스턴스와 연결된 도메인 이름으로 조직을 지정해야 합니다.
- ISV인 경우 모든 조직 또는 일부 조직(다중 테넌트 앱)에서 사용자의 업무 및 학교 계정으로 로그인할 수 있습니다. 그러나 사용자가 개인 Microsoft 계정으로 로그인하도록 할 수도 있습니다.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>코드/구성에서 대상 을 지정하는 방법

코드에서 MSAL을 사용하여 다음 값 중 하나를 사용하여 대상 을 지정합니다.
- Azure AD 기관 대상 열거형
- 테넌트 ID는 다음과 같은 것입니다.
  - 단일 테넌트 응용 프로그램에 대한 GUID(Azure AD 인스턴스의 ID)
  - Azure AD 인스턴스와 연결된 도메인 이름(단일 테넌트 응용 프로그램의 경우)
- Azure AD 기관 대상 열거형 대신 테넌트 ID로 이러한 자리 표시자 중 하나:
    - `organizations`다중 테넌트 응용 프로그램에 대한
    - `consumers`개인 계정으로만 로그인하려면
    - `common`사용자 자신의 직장 및 학교 계정 또는 개인 Microsoft 계정으로 로그인하려면

MSAL은 Azure AD 기관 대상 과 테넌트 ID를 모두 지정하는 경우 의미 있는 예외를 throw합니다.

대상을 지정하지 않으면 앱에서 Azure AD 및 개인 Microsoft 계정을 대상으로 지정합니다. 즉, 지정된 것처럼 `common` 작동합니다.

### <a name="effective-audience"></a>효과적인 잠재고객

애플리케이션의 유효 대상은 앱에 설정한 대상과 앱 등록에 지정된 대상의 최소(교차점이 있는 경우)입니다. 실제로 앱 [등록](https://aka.ms/appregistrations) 환경을 사용하면 앱에 대한 대상(지원되는 계정 유형)을 지정할 수 있습니다. 자세한 내용은 [빠른 시작: Microsoft ID 플랫폼에 응용 프로그램 등록을](quickstart-register-app.md)참조하십시오.

현재 개인 Microsoft 계정만 으로 사용자에게 로그인하는 앱을 얻는 유일한 방법은 다음 설정을 모두 구성하는 것입니다.
- 앱 등록 대상을 `Work and school accounts and personal accounts`로 설정합니다.
- 코드/구성의 대상을 (또는 `AadAuthorityAudience.PersonalMicrosoftAccount` `TenantID` ="소비자")로 설정합니다.

## <a name="client-id"></a>클라이언트 ID

클라이언트 ID는 앱이 등록될 때 Azure AD에서 앱에 할당된 고유 응용 프로그램(클라이언트) ID입니다.

## <a name="redirect-uri"></a>리디렉션 URI

리디렉션 URI는 ID 공급자가 보안 토큰을 다시 보내는 URI입니다.

### <a name="redirect-uri-for-public-client-apps"></a>공용 클라이언트 앱에 대한 URI 리디렉션

MSAL을 사용하는 공용 클라이언트 앱 개발자인 경우:
- 데스크톱 또는 UWP `.WithDefaultRedirectUri()` 응용 프로그램(MSAL.NET 4.1 이상)에서 사용하려고 합니다. 이 메서드는 공용 클라이언트 응용 프로그램의 리디렉션 uri 속성을 공용 클라이언트 응용 프로그램에 대 한 기본 권장 리디렉션 uri로 설정 합니다.

  플랫폼  | 리디렉션 URI
  ---------  | --------------
  데스크톱 앱(.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | 의 `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`값입니다. 이렇게 하면 등록해야 하는 WebAuthenticationBroker.GetCurrentApplicationCallbackUri() 결과에 값을 설정하여 브라우저에서 SSO를 사용할 수 있습니다.
  .NET Core | `https://localhost`. 이렇게 하면 .NET Core에 현재 포함된 웹 보기에 대한 UI가 없기 때문에 대화형 인증을 위해 시스템 브라우저를 사용할 수 있습니다.

- 브로커를 지원하지 않는 Xamarin Android 및 iOS 응용 프로그램을 빌드하는 경우 리디렉션 URI를 추가할 필요가 없습니다(리디렉션 URI가 Xamarin Android 및 iOS용으로 `msal{ClientId}://auth` 자동으로 설정됩니다.

- [앱 등록에서](https://aka.ms/appregistrations)리디렉션 URI를 구성해야 합니다.

   ![앱 등록에서 URI 리디렉션](media/msal-client-application-configuration/redirect-uri.png)

`RedirectUri` 속성을 사용하여 리디렉션 URI를 재정의할 수 있습니다(예: 브로커를 사용하는 경우). 다음은 해당 시나리오에 대한 리디렉션 URI의 몇 가지 예입니다.

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.client.sample";
- `RedirectUriOnIos`= $"msauth. {번들.ID};인증";

추가 iOS 세부 정보는 ADAL.NET MSAL.NET 및 [iOS에서 브로커를 활용하는](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS) [Microsoft 인증기를 사용하는 iOS 응용 프로그램 마이그레이션을](msal-net-migration-ios-broker.md) 참조하십시오.
추가 안 드 로이드 세부 정보를 참조, [안 드 로이드에서 중개 인증](brokered-auth.md)을 참조 하십시오.

### <a name="redirect-uri-for-confidential-client-apps"></a>기밀 클라이언트 앱에 대한 URI 리디렉션

웹 앱의 경우 리디렉션 URI(또는 회신 URI)는 Azure AD가 토큰을 응용 프로그램으로 다시 보내는 데 사용하는 URI입니다. 기밀 앱이 이들 중 하나인 경우 이 URI는 웹 앱/웹 API의 URL이 될 수 있습니다. 리디렉션 URI는 앱 등록에 등록해야 합니다. 이 등록은 처음에 로컬에서 테스트한 앱을 배포할 때 특히 중요합니다. 그런 다음 응용 프로그램 등록 포털에 배포된 앱의 회신 URL을 추가해야 합니다.

데몬 앱의 경우 리디렉션 URI를 지정할 필요가 없습니다.

## <a name="client-secret"></a>클라이언트 암호

이 옵션은 기밀 클라이언트 앱에 대한 클라이언트 비밀을 지정합니다. 이 암호(앱 암호)는 응용 프로그램 등록 포털에서 제공되거나 PowerShell AzureAD, PowerShell AzureRM 또는 Azure CLI를 사용하여 앱을 등록하는 동안 Azure AD에 제공됩니다.

## <a name="logging"></a>로깅

다른 구성 옵션을 사용하면 로깅 및 문제 해결이 가능합니다. 사용 방법에 대한 자세한 내용은 [로깅](msal-logging.md) 문서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

MSAL.NET 를 [사용하여 클라이언트 응용 프로그램을 인스턴스화하는](msal-net-initializing-client-applications.md)방법에 대해 알아봅니다.
[MSAL.js 를 사용하여 클라이언트 응용 프로그램을 인스턴스화하는](msal-js-initializing-client-applications.md)방법에 대해 알아봅니다.

---
title: Apple 디바이스용 Microsoft Enterprise SSO 플러그 인
titleSuffix: Microsoft identity platform | Azure
description: IOS, iPadOS 및 macOS 장치에 대 한 Azure Active Directory SSO 플러그 인에 대해 알아봅니다.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559131"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인 (미리 보기)

>[!IMPORTANT]
> 이 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Apple 장치에 대 한 Microsoft Enterprise SSO 플러그* 인은 apple의 [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 기능을 지 원하는 모든 응용 프로그램에서 Macos, iOS 및 IPadOS의 Azure Active Directory (Azure AD) 계정에 대 한 Single Sign-On (SSO) 계정을 제공 합니다. 플러그 인은 비즈니스에서 의존할 수 있지만 아직 최신 id 라이브러리 또는 프로토콜을 지원 하지 않는 이전 응용 프로그램에 대 한 SSO를 제공 합니다. Microsoft는이 플러그 인을 사용 하 여 응용 프로그램의 유용성을 높이고 최상의 보호를 제공 하는 동시에 Apple과 긴밀 하 게 협력 했습니다.

Enterprise SSO 플러그 인은 현재 다음 앱의 기본 제공 기능입니다.

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): IOS, iPadOS
* Microsoft Intune [회사 포털](/mem/intune/apps/apps-company-portal-macos): macos

## <a name="features"></a>기능

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인은 다음과 같은 이점을 제공 합니다.

- Apple Enterprise SSO 기능을 지 원하는 모든 응용 프로그램에서 Azure AD 계정에 대 한 SSO를 제공 합니다.
- 모든 MDM (모바일 장치 관리) 솔루션에서 사용 하도록 설정할 수 있습니다.
- Microsoft id 플랫폼 라이브러리를 아직 사용 하지 않는 응용 프로그램에 대 한 SSO를 확장 합니다.
- OAuth 2, Openid connect Connect 및 SAML를 사용 하는 응용 프로그램에 대 한 SSO를 확장 합니다.

## <a name="requirements"></a>요구 사항

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 사용 하려면 다음을 수행 합니다.

- 장치는 Apple 장치용 Microsoft Enterprise SSO 플러그 인을 *지원* 하 고 설치 된 앱이 있어야 합니다.
  - iOS 13.0 이상: [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 이상: [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 이상: [Intune 회사 포털 앱](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- 장치를 *MDM에 등록* 해야 합니다 (예: Microsoft Intune).
- Enterprise SSO 플러그 인을 사용 하도록 설정 하려면 *장치에 구성을 푸시* 해야 합니다. Apple에는이 보안 제약 조건이 필요 합니다.

### <a name="ios-requirements"></a>iOS 요구 사항:
- iOS 13.0 이상이 장치에 설치 되어 있어야 합니다.
- Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 제공 하는 Microsoft 응용 프로그램을 장치에 설치 해야 합니다. 공개 미리 보기의 경우 이러한 응용 프로그램은 [Microsoft Authenticator 앱](/azure/active-directory/user-help/user-help-auth-app-overview)입니다.


### <a name="macos-requirements"></a>macOS 요구 사항:
- macOS 10.15 이상이 장치에 설치 되어 있어야 합니다. 
- Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 제공 하는 Microsoft 응용 프로그램을 장치에 설치 해야 합니다. 공개 미리 보기의 경우 이러한 응용 프로그램에는 [Intune 회사 포털 앱](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)이 포함 됩니다.

## <a name="enable-the-sso-plug-in"></a>SSO 플러그 인 사용

MDM을 사용 하 여 SSO 플러그 인을 사용 하도록 설정 하려면 다음 정보를 사용 합니다.
### <a name="microsoft-intune-configuration"></a>Microsoft Intune 구성

Microsoft Intune를 MDM 서비스로 사용 하는 경우 기본 제공 구성 프로필 설정을 사용 하 여 Microsoft Enterprise SSO 플러그 인을 사용 하도록 설정할 수 있습니다.

1. 구성 프로필의 [SSO 앱 확장](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) 설정을 구성 합니다. 
1. 프로필이 아직 할당 되지 않은 경우 [사용자 또는 장치 그룹에 프로필을 할당](/mem/intune/configuration/device-profile-assign)합니다.

SSO 플러그 인을 사용 하도록 설정 하는 프로필 설정은 다음에 각 장치가 Intune을 사용 하 여 체크 인할 때 그룹의 장치에 자동으로 적용 됩니다.

### <a name="manual-configuration-for-other-mdm-services"></a>기타 MDM 서비스의 수동 구성

MDM 용 Intune을 사용 하지 않는 경우 다음 매개 변수를 사용 하 여 Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 구성 합니다.

iOS 설정:

- **확장 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **팀 ID**:이 필드는 iOS에 필요 하지 않습니다.

macOS 설정:

- **확장 ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **팀 ID**: `UBF8T346G9`

일반 설정:

- **유형**: 리디렉션
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>추가 구성 옵션
다른 구성 옵션을 추가 하 여 SSO 기능을 다른 앱으로 확장할 수 있습니다.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft id 플랫폼 라이브러리를 사용 하지 않는 앱에 SSO 사용

SSO 플러그 인은 microsoft Authentication Library (MSAL)와 같은 Microsoft SDK를 사용 하 여 개발 되지 않은 경우에도 모든 응용 프로그램을 SSO에 참여할 수 있도록 허용 합니다.

SSO 플러그 인은 다음을 포함 하는 장치에 의해 자동으로 설치 됩니다.
* IOS 또는 iPadOS에서 Authenticator 앱을 다운로드 하거나 macOS에서 Intune 회사 포털 앱을 다운로드 했습니다.
* 사용자의 조직에 장치를 등록 했습니다. 

조직에서는 MFA (다단계 인증), 암호 없는 인증 및 조건부 액세스와 같은 시나리오에 Authenticator 앱을 사용 하는 경우가 많습니다. MDM 공급자를 사용 하 여 응용 프로그램에 대 한 SSO 플러그 인을 켤 수 있습니다. Microsoft는 Intune의 Microsoft Endpoint Manager 내에서 플러그 인을 쉽게 구성할 수 있도록 했습니다. Allowlist는 SSO 플러그 인을 사용 하도록 이러한 응용 프로그램을 구성 하는 데 사용 됩니다.

>[!IMPORTANT]
> Microsoft Enterprise SSO 플러그 인은 네이티브 Apple 네트워크 기술 또는 웹 보기를 사용 하는 앱만 지원 합니다. 자체 네트워크 계층 구현을 제공 하는 응용 프로그램은 지원 하지 않습니다.  

Microsoft id 플랫폼 라이브러리를 사용 하지 않는 앱에 대 한 Microsoft Enterprise SSO 플러그 인을 구성 하려면 다음 매개 변수를 사용 합니다.

특정 앱 목록을 제공 하려면 다음 매개 변수를 사용 합니다.

- **키**: `AppAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 응용 프로그램에 대 한 응용 프로그램 번들 id의 쉼표로 구분 된 목록입니다.
- **예**: `com.contoso.workapp, com.contoso.travelapp`

접두사 목록을 제공 하려면 다음 매개 변수를 사용 합니다.
- **키**: `AppPrefixAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 응용 프로그램에 대 한 응용 프로그램 번들 ID 접두사를 쉼표로 구분한 목록입니다. 이 매개 변수는 특정 접두사로 시작 하는 모든 앱이 SSO에 참여할 수 있도록 허용 합니다.
- **예**: `com.contoso., com.fabrikam.`

MDM 관리자가 SSO에 참여할 수 있도록 허용 하는 [동의한 앱](./application-consent-experience.md) 은 최종 사용자에 대 한 토큰을 자동으로 가져올 수 있습니다. 따라서 신뢰할 수 있는 응용 프로그램만 allowlist에 추가 합니다. 

>[!NOTE]
> MSAL 또는 ASWebAuthenticationSession을 사용 하는 응용 프로그램을 SSO에 참여할 수 있는 앱 목록에 추가할 필요가 없습니다. 이러한 응용 프로그램은 기본적으로 사용 하도록 설정 됩니다. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>IOS 장치에서 앱 번들 식별자 찾기

Apple은 앱 스토어에서 번들 Id를 가져오는 쉬운 방법을 제공 하지 않습니다. SSO에 사용 하려는 앱의 번들 Id를 얻는 가장 쉬운 방법은 공급 업체 또는 앱 개발자에 게 문의 하는 것입니다. 해당 옵션을 사용할 수 없는 경우 MDM 구성을 사용 하 여 번들 Id를 찾을 수 있습니다. 

1. MDM 구성에서 일시적으로 다음 플래그를 사용 하도록 설정 합니다.

    - **키**: `admin_debug_mode_enabled`
    - **형식**: `Integer`
    - **값**: 1 또는 0
1. 이 플래그가 설정 되 면 번들 ID를 알고 싶은 장치에서 iOS 앱에 로그인 합니다. 
1. Authenticator 앱에서 **도움말**  >  **로그 보내기**  >  **보기 로그** 를 선택 합니다. 
1. 로그 파일에서 다음 줄을 찾습니다 `[ADMIN MODE] SSO extension has captured following app bundle identifiers` .. 이 줄은 SSO 확장에 표시 되는 모든 응용 프로그램 번들 Id를 캡처해야 합니다. 

번들 Id를 사용 하 여 앱에 대 한 SSO를 구성 합니다. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>사용자가 알 수 없는 응용 프로그램 및 Safari 브라우저에서 로그인 할 수 있도록 허용

기본적으로 Microsoft Enterprise SSO 플러그 인은 MSAL 또는 ADAL (Azure Active Directory Authentication Library)과 같은 Microsoft id 플랫폼 라이브러리를 사용 하는 앱에서 사용자가 로그인 한 경우에만 권한 있는 앱에 대 한 SSO를 제공 합니다. Microsoft Enterprise SSO 플러그 인은 새로운 토큰 획득 중 Microsoft id 플랫폼 라이브러리를 사용 하는 다른 앱에서 호출 되는 경우 공유 자격 증명을 가져올 수도 있습니다.

플래그를 사용 하는 경우 `browser_sso_interaction_enabled` Microsoft id 플랫폼 라이브러리를 사용 하지 않는 앱은 초기 부트스트랩을 수행 하 고 공유 자격 증명을 가져올 수 있습니다. Safari 브라우저는 초기 부트스트래핑을 수행 하 고 공유 자격 증명을 가져올 수도 있습니다. 

Microsoft Enterprise SSO 플러그 인에 공유 자격 증명이 아직 없는 경우 Safari 브라우저, ASWebAuthenticationSession, SafariViewController 또는 다른 허용 된 네이티브 응용 프로그램 내의 Azure AD URL에서 로그인이 요청 될 때마다 하나를 가져오려고 시도 합니다. 

다음 매개 변수를 사용 하 여 플래그를 사용 하도록 설정 합니다. 

- **키**: `browser_sso_interaction_enabled`
- **형식**: `Integer`
- **값**: 1 또는 0

macOS는 모든 앱에서 일관 된 환경을 제공할 수 있도록이 설정이 필요 합니다. 대부분의 앱은 로그인에 인증자 응용 프로그램을 사용 하므로 iOS 및 iPadOS는이 설정이 필요 하지 않습니다. 하지만 일부 응용 프로그램에서는 iOS 또는 iPadOS에서 인증자 앱을 사용 하지 않는 경우이 플래그를 사용 하면 환경이 향상 되므로이 설정을 사용 하도록 설정 하는 것이 좋습니다. 설정은 기본적으로 사용 되지 않습니다.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>초기 부트스트랩 중에 MFA 요청을 사용 하지 않도록 설정

기본적으로 Microsoft Enterprise SSO 플러그 인은 초기 부트스트래핑 동안 및 공유 자격 증명을 가져오는 동안 사용자에 게 MFA를 묻는 메시지를 항상 표시 합니다. 사용자가 연 응용 프로그램에 필요 하지 않은 경우에도 사용자에 게 MFA를 입력 하 라는 메시지가 표시 됩니다. 이 동작은 나중에 MFA가 필요한 경우 사용자에 게 메시지를 표시 하지 않고도 다른 모든 응용 프로그램에서 공유 자격 증명을 쉽게 사용할 수 있도록 합니다. 사용자에 게는 전체 프롬프트가 더 적기 때문에 일반적으로이 설정이 좋은 결정입니다.

를 사용 하도록 설정 하면 `browser_sso_disable_mfa` 초기 부트스트랩 중에 및 공유 자격 증명을 가져오는 동안 MFA가 꺼집니다. 이 경우 응용 프로그램 또는 리소스에 MFA가 필요한 경우에만 사용자에 게 메시지가 표시 됩니다. 

플래그를 사용 하도록 설정 하려면 다음 매개 변수를 사용 합니다.

- **키**: `browser_sso_disable_mfa`
- **형식**: `Integer`
- **값**: 1 또는 0

사용자에 게 로그인 하 라는 메시지가 표시 되는 횟수를 줄이도록이 플래그를 사용 하지 않도록 설정 하는 것이 좋습니다. 조직에서 MFA를 거의 사용 하지 않는 경우 플래그를 사용 하도록 설정할 수 있습니다. 하지만 대신 MFA를 더 자주 사용 하는 것이 좋습니다. 이러한 이유로 플래그는 기본적으로 사용 하지 않도록 설정 됩니다.

#### <a name="disable-oauth-2-application-prompts"></a>OAuth 2 응용 프로그램 프롬프트 사용 안 함

Microsoft Enterprise SSO 플러그 인은 허용 된 응용 프로그램에서 제공 하는 네트워크 요청에 공유 자격 증명을 추가 하 여 SSO를 제공 합니다. 그러나 일부 OAuth 2 응용 프로그램은 프로토콜 계층에서 최종 사용자 프롬프트를 잘못 적용할 수 있습니다. 이 문제가 표시 되는 경우 해당 앱에 대해 공유 자격 증명이 무시 됨도 표시 됩니다. Microsoft Enterprise SSO 플러그 인이 다른 응용 프로그램에 대해 작동 하는 경우에도 사용자에 게 로그인 하 라는 메시지가 표시 됩니다.  

플래그를 사용 하도록 설정 하면 `disable_explicit_app_prompt` 기본 응용 프로그램 및 웹 응용 프로그램에서 프로토콜 계층에 대 한 최종 사용자 프롬프트를 강제로 실행 하 고 SSO를 우회 하는 기능이 제한 됩니다. 플래그를 사용 하도록 설정 하려면 다음 매개 변수를 사용 합니다.

- **키**: `disable_explicit_app_prompt`
- **형식**: `Integer`
- **값**: 1 또는 0

모든 앱에서 일관 된 환경을 제공 하기 위해이 플래그를 사용 하는 것이 좋습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>특정 응용 프로그램에 대 한 쿠키를 통해 SSO를 사용 하도록 설정

일부 앱은 SSO 확장과 호환 되지 않을 수 있습니다. 특히 고급 네트워크 설정이 있는 앱은 SSO를 사용 하는 경우 예기치 않은 문제가 발생할 수 있습니다. 예를 들어 네트워크 요청이 취소 되었거나 중단 되었음을 나타내는 오류가 표시 될 수 있습니다. 

[MSAL을 사용 하지 않는 응용 프로그램](#applications-that-dont-use-msal) 섹션에서 설명 하는 방법을 사용 하 여 로그인 하는 데 문제가 있는 경우 대체 구성을 시도 합니다. 다음 매개 변수를 사용 하 여 플러그 인을 구성 합니다.

- **키**: `AppCookieSSOAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 응용 프로그램에 대 한 응용 프로그램 번들 ID 접두사를 쉼표로 구분한 목록입니다. 나열 된 접두사로 시작 하는 모든 앱은 SSO에 참여할 수 있습니다.
- **예**: `com.contoso.myapp1, com.fabrikam.myapp2`

이 설치 프로그램을 사용 하 여 SSO에 대해 사용 하도록 설정 된 응용 프로그램은 및 둘 다에 추가 해야 `AppCookieSSOAllowList` `AppPrefixAllowList` 합니다.

예기치 않은 로그인 실패가 발생 한 응용 프로그램에 대해서만이 구성을 시도 하세요. 

#### <a name="use-intune-for-simplified-configuration"></a>간소화 된 구성에 Intune 사용

Intune을 MDM 서비스로 사용 하 여 Microsoft Enterprise SSO 플러그 인을 쉽게 구성할 수 있습니다. 예를 들어 Intune을 사용 하 여 플러그 인을 사용 하도록 설정 하 고 allowlist에 이전 앱을 추가 하 여 SSO를 가져올 수 있습니다. 

자세한 내용은 [Intune 구성 설명서](/intune/configuration/ios-device-features-settings)를 참조 하세요.

## <a name="use-the-sso-plug-in-in-your-application"></a>응용 프로그램에서 SSO 플러그 인 사용

[Apple 장치용 Msal](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 이상 버전은 apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 지원 합니다. Microsoft Enterprise SSO 플러그 인에 대 한 지원을 추가 하려면 권장 되는 방법입니다. Microsoft id 플랫폼의 전체 기능을 사용할 수 있도록 합니다.

Frontline 작업자 시나리오용 응용 프로그램을 빌드하는 경우 설치 정보는 [iOS 장치에 대 한 공유 장치 모드](msal-ios-shared-devices.md) 를 참조 하세요.

## <a name="understand-how-the-sso-plug-in-works"></a>SSO 플러그 인 작동 방식 이해

Microsoft Enterprise SSO 플러그 인은 [Apple Enterprise SSO 프레임 워크](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)에 의존 합니다. 프레임 워크를 조인 하는 id 공급자는 해당 도메인에 대 한 네트워크 트래픽을 가로채서 해당 요청을 처리 하는 방법을 개선 하거나 변경할 수 있습니다. 예를 들어, SSO 플러그 인은 최종 사용자 자격 증명을 안전 하 게 수집 하거나, MFA를 요구 하거나, 응용 프로그램에 토큰을 자동으로 제공 하는 추가 Ui를 표시할 수 있습니다.

또한 네이티브 응용 프로그램은 사용자 지정 작업을 구현 하 고 SSO 플러그 인과 직접 통신할 수 있습니다. 자세한 내용은 [Apple의이 2019 전 세계 개발자 회의 비디오](https://developer.apple.com/videos/play/tech-talks/301/)를 참조 하세요.

### <a name="applications-that-use-msal"></a>MSAL을 사용 하는 응용 프로그램

[Apple 장치용 Msal](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 이상 버전은 기본적으로 회사 및 학교 계정에 대 한 apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 지원 합니다. 

[모든 권장 단계](./quickstart-v2-ios.md) 를 따르고 기본 [리디렉션 URI 형식을](./redirect-uris-ios.md)사용한 경우 특별 한 구성이 필요 하지 않습니다. SSO 플러그 인을 포함 하는 장치에서 MSAL은 모든 대화형 및 자동 토큰 요청에 대해이를 자동으로 호출 합니다. 또한 계정 열거 및 계정 제거 작업에 대해 호출 합니다. MSAL은 사용자 지정 작업을 사용 하는 네이티브 SSO 플러그 인 프로토콜을 구현 하기 때문에이 설치 프로그램은 최종 사용자에 게 가장 부드럽습니다 기본 환경을 제공 합니다. 

SSO 플러그 인이 MDM에서 사용 하도록 설정 되어 있지 않지만 Microsoft Authenticator 앱이 장치에 있는 경우 MSAL은 대신 모든 대화형 토큰 요청에 인증자 앱을 사용 합니다. SSO 플러그 인은 인증자 앱과 SSO를 공유 합니다.

### <a name="applications-that-dont-use-msal"></a>MSAL을 사용 하지 않는 응용 프로그램

MSAL과 같은 Microsoft id 플랫폼 라이브러리를 사용 하지 않는 응용 프로그램은 관리자가 이러한 응용 프로그램을 allowlist에 추가 하는 경우에도 SSO를 가져올 수 있습니다. 

다음 조건이 충족 되는 경우 해당 앱의 코드를 변경할 필요가 없습니다.

- 응용 프로그램은 Apple 프레임 워크를 사용 하 여 네트워크 요청을 실행 합니다. 이러한 프레임 워크는 [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 및 [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)를 포함 합니다 (예:). 
- 응용 프로그램은 표준 프로토콜을 사용 하 여 Azure AD와 통신 합니다. 이러한 프로토콜에는 OAuth 2, SAML, WS-FEDERATION 등이 있습니다.
- 응용 프로그램은 네이티브 UI에서 일반 텍스트 사용자 이름 및 암호를 수집 하지 않습니다.

이 경우 응용 프로그램에서 네트워크 요청을 만들고 사용자에 게 로그인 할 웹 브라우저를 열 때 SSO가 제공 됩니다. 사용자가 Azure AD 로그인 URL로 리디렉션되는 경우 SSO 플러그 인은 URL의 유효성을 검사 하 고 해당 URL에 대 한 SSO 자격 증명을 확인 합니다. 자격 증명을 찾은 경우 SSO 플러그 인은이를 Azure AD에 전달 합니다. 그러면 사용자에 게 자격 증명을 입력 하 라는 메시지 없이 응용 프로그램에서 네트워크 요청을 완료할 수 있는 권한을 부여 합니다. 또한 장치가 Azure AD에 알려진 경우 SSO 플러그 인은 장치 기반 조건부 액세스 검사를 충족 하기 위해 장치 인증서를 전달 합니다. 

MSAL이 아닌 앱에 대해 SSO를 지원 하기 위해 SSO 플러그 인은 [기본 새로 고침 토큰 이란?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)에 설명 된 Windows 브라우저 플러그 인과 비슷한 프로토콜을 구현 합니다. 

MSAL 기반 앱에 비해 SSO 플러그 인은 MSAL이 아닌 앱에 대해 더 투명 하 게 작동 합니다. 앱이 제공 하는 기존 브라우저 로그인 환경과 통합 됩니다. 

최종 사용자에 게 친숙 한 환경이 표시 되며 각 응용 프로그램에서 다시 로그인 할 필요가 없습니다. 예를 들어, 기본 계정 선택기를 표시 하는 대신 SSO 플러그 인은 웹 기반 계정 선택 환경에 SSO 세션을 추가 합니다. 

## <a name="next-steps"></a>다음 단계

[IOS 장치에 대 한 공유 장치 모드](msal-ios-shared-devices.md)에 대해 알아봅니다.

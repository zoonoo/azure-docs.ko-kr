---
title: Apple 디바이스용 Microsoft Enterprise SSO 플러그 인
titleSuffix: Microsoft identity platform | Azure
description: IOS 및 macOS 장치에 대 한 Microsoft의 Azure Active Directory SSO 플러그 인에 대해 알아봅니다.
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
ms.openlocfilehash: ec0ab4601e15129ecd8917e0e750a3e1661dc558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530700"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인 (미리 보기)

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

*Apple 장치에 대 한 Microsoft Enterprise SSO 플러그* 인은 apple의 [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 기능을 지 원하는 모든 응용 프로그램에서 Azure Active Directory (Azure AD) 계정에 대 한 Single Sign-On (SSO) 계정을 제공 합니다. Microsoft는 apple과 긴밀 하 게 협력 하 여 Apple 및 Microsoft가 제공할 수 있는 최상의 보호 기능을 제공 하는 동시에 응용 프로그램의 유용성을 향상 시키기 위해이 플러그 인을 개발 했습니다.

이 공개 미리 보기 릴리스에서 Enterprise SSO 플러그 인은 iOS 장치에만 사용할 수 있으며 특정 Microsoft 응용 프로그램에 배포 됩니다.

## <a name="features"></a>기능

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인은 다음과 같은 이점을 제공 합니다.

- Apple의 Enterprise Single Sign-On 기능을 지 원하는 모든 응용 프로그램에서 Azure AD 계정에 대 한 SSO를 제공 합니다.
- Microsoft Authenticator에서 자동으로 배달 되며 모든 MDM (모바일 장치 관리) 솔루션에서 사용 하도록 설정할 수 있습니다.

## <a name="requirements"></a>요구 사항

Apple 장치에 Microsoft Enterprise SSO 플러그 인을 사용 하려면 다음을 수행 합니다.

- iOS 13.0 이상이 장치에 설치 되어 있어야 합니다.
- Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 제공 하는 Microsoft 응용 프로그램을 장치에 설치 해야 합니다. 공개 미리 보기의 경우 이러한 응용 프로그램에는 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)이 포함 됩니다.
- 장치가 MDM에 등록 되어야 합니다 (예: Microsoft Intune).
- 장치에서 Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 사용 하도록 설정 하려면 장치에 구성을 푸시 해야 합니다. 이 보안 제약 조건은 Apple에 필요 합니다.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>MDM (모바일 장치 관리)을 사용 하 여 SSO 플러그 인 사용

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 사용 하도록 설정 하려면 장치에서 MDM 서비스를 통해 신호를 전송 해야 합니다. Microsoft는 [Microsoft Authenticator 앱](..//user-help/user-help-auth-app-overview.md)에 Enterprise SSO 플러그 인을 포함 하므로 microsoft Enterprise SSO 플러그 인을 사용 하도록 앱을 구성 하려면 MDM을 사용 합니다.

다음 매개 변수를 사용 하 여 Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 구성 합니다.

- **유형**: 리디렉션
- **확장 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **팀 ID**: (이 필드는 iOS에는 필요 하지 않음)
- **Url**:
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>추가 구성 옵션
추가 구성 옵션을 추가 하 여 SSO 기능을 추가 앱으로 확장할 수 있습니다.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>MSAL을 사용 하지 않는 앱에 SSO 사용

SSO 플러그 인은 MSAL (Microsoft 인증 라이브러리)과 같은 Microsoft SDK를 사용 하 여 개발 되지 않은 경우에도 모든 응용 프로그램 Single Sign-On에 참여할 수 있도록 허용 합니다.

SSO 플러그 인은 Microsoft Authenticator 앱을 다운로드 하 고 자신의 장치를 조직에 등록 한 장치에 의해 자동으로 설치 됩니다. 조직에서 multi-factor authentication, 암호 없는 인증 및 조건부 액세스와 같은 시나리오에 대해 현재 Authenticator 앱을 사용 하 고 있습니다. 모든 MDM 공급자를 사용 하 여 응용 프로그램에 대해 설정할 수 있습니다. Microsoft는 Intune의 Microsoft Endpoint Manager 내에서 쉽게 구성할 수 있었습니다. 허용 목록은 인증자 앱에서 설치한 SSO 플러그 인을 사용 하도록 이러한 응용 프로그램을 구성 하는 데 사용 됩니다.

네이티브 Apple 네트워크 기술 또는 웹 보기를 사용 하는 앱만 지원 됩니다. 응용 프로그램에서 자체 네트워크 계층 구현을 제공 하는 경우 Microsoft Enterprise SSO 플러그 인은 지원 되지 않습니다.  

MSAL을 사용 하지 않는 앱에 대 한 Microsoft Enterprise SSO 플러그 인을 구성 하려면 다음 매개 변수를 사용 합니다.

- **키**: `AppAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 응용 프로그램에 대 한 응용 프로그램 번들 id의 쉼표로 구분 된 목록입니다.
- **예**: `com.contoso.workapp, com.contoso.travelapp`

MDM 관리자가 SSO에 참여할 수 있도록 허용 하는 [동의한 apps](./application-consent-experience.md) 는 최종 사용자에 대 한 토큰을 자동으로 가져올 수 있습니다. 따라서 신뢰할 수 있는 응용 프로그램만 허용 목록에 추가 하는 것이 중요 합니다. 

MSAL 또는 ASWebAuthenticationSession을 사용 하는 응용 프로그램을이 목록에 추가 하지 않아도 됩니다. 이러한 응용 프로그램은 기본적으로 사용 하도록 설정 됩니다. 

#### <a name="allow-creating-sso-session-from-any-application"></a>모든 응용 프로그램에서 SSO 세션 만들기 허용

기본적으로 Microsoft Enterprise SSO 플러그 인은 SSO 플러그 인에 이미 공유 자격 증명이 있는 경우에만 권한 있는 앱에 대 한 SSO를 제공 합니다. 토큰 획득 중에 다른 ADAL 또는 MSAL 기반 응용 프로그램에서 호출 하는 경우 Microsoft Enterprise SSO 플러그 인에서 공유 자격 증명을 가져올 수 있습니다. 대부분의 Microsoft 앱은 Microsoft Authenticator 또는 SSO 플러그 인을 사용 합니다. 즉, 기본적으로 네이티브 앱 흐름의 외부에 있는 SSO를 사용 하는 것이 가장 좋습니다.  

플래그를 사용 하도록 설정 하면 `browser_sso_interaction_enabled` MSAL 앱 및 Safari 브라우저에서 초기 부트스트래핑을 수행 하 고 공유 자격 증명을 가져올 수 있습니다. Microsoft Enterprise SSO 플러그 인에 공유 자격 증명이 아직 없는 경우 Safari 브라우저, ASWebAuthenticationSession, SafariViewController 또는 다른 허용 되는 네이티브 응용 프로그램 내에서 Azure AD URL의 로그인이 요청 될 때마다 하나를 가져오려고 시도 합니다.  

- **키**: `browser_sso_interaction_enabled`
- **형식**: `Integer`
- **값**: 1 또는 0

이 플래그를 사용 하 여 모든 앱에서 보다 일관 된 환경을 얻는 것이 좋습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 

#### <a name="disable-oauth2-application-prompts"></a>OAuth2 응용 프로그램 프롬프트 사용 안 함

Microsoft Enterprise SSO 플러그 인은 허용 된 응용 프로그램에서 들어오는 네트워크 요청에 공유 자격 증명을 추가 하 여 SSO를 제공 합니다. 일부 OAuth2 응용 프로그램은 프로토콜 계층에서 최종 사용자 프롬프트를 적용 하 고 있을 수 있습니다. 공유 자격 증명은 해당 앱에 대해 무시 됩니다.  

플래그를 사용 하도록 설정 하면 `disable_explicit_app_prompt` 기본 및 웹 응용 프로그램에서 프로토콜 계층에 대 한 최종 사용자 프롬프트를 실행 하 고 SSO를 우회 하는 기능이 제한 됩니다.

- **키**: `disable_explicit_app_prompt`
- **형식**: `Integer`
- **값**: 1 또는 0

이 플래그를 사용 하 여 모든 앱에서 보다 일관 된 환경을 얻는 것이 좋습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 

#### <a name="use-intune-for-simplified-configuration"></a>간소화 된 구성에 Intune 사용

Microsoft Intune를 MDM 서비스로 사용 하 여 Microsoft Enterprise SSO 플러그 인을 쉽게 구성할 수 있습니다. 자세한 내용은 [Intune 구성 설명서](/intune/configuration/ios-device-features-settings)를 참조 하세요.

## <a name="using-the-sso-plug-in-in-your-application"></a>응용 프로그램에서 SSO 플러그 인 사용

[Apple 장치용 MSAL (Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-objc) ) 버전 1.1.0 이상에서는 Apple 장치용 microsoft Enterprise SSO 플러그 인을 지원 합니다.

Frontline Worker 시나리오용 응용 프로그램을 빌드하는 경우 기능의 추가 설정을 보려면 [iOS 장치용 공유 장치 모드](msal-ios-shared-devices.md) 를 참조 하세요.

## <a name="how-the-sso-plug-in-works"></a>SSO 플러그 인의 작동 방법

Microsoft Enterprise SSO 플러그 인은 [Apple의 엔터프라이즈 단일 Sign-On 프레임 워크](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)에 의존 합니다. 프레임 워크에 등록 하는 id 공급자는 해당 도메인에 대 한 네트워크 트래픽을 가로채 해당 요청을 처리 하는 방법을 개선 하거나 변경할 수 있습니다. 예를 들어, SSO 플러그 인은 최종 사용자 자격 증명을 안전 하 게 수집 하거나, MFA를 요구 하거나, 응용 프로그램에 토큰을 자동으로 제공 하는 추가 UI를 표시할 수 있습니다.

또한 네이티브 응용 프로그램은 사용자 지정 작업을 구현 하 고 SSO 플러그 인과 직접 통신할 수 있습니다.
[Apple의이 2019 WWDC 비디오](https://developer.apple.com/videos/play/tech-talks/301/) 에서 Single sign-on 프레임 워크에 대해 알아볼 수 있습니다.

### <a name="applications-that-use-msal"></a>MSAL을 사용 하는 응용 프로그램

[Apple 장치용 MSAL (Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-objc) ) 버전 1.1.0 이상에서는 회사 및 학교 계정에 대해 기본적으로 apple 장치에 대 한 microsoft Enterprise SSO 플러그 인을 지원 합니다. 

[권장 단계를 모두 수행](./quickstart-v2-ios.md) 하 고 기본 [리디렉션 URI 형식을](./redirect-uris-ios.md)사용한 경우에는 특별 한 구성이 필요 하지 않습니다. SSO 플러그 인이 있는 장치에서 실행 되는 경우 MSAL은 계정 열거 및 계정 제거 작업 뿐만 아니라 모든 대화형 및 자동 토큰 요청에 대해이를 자동으로 호출 합니다. MSAL은 사용자 지정 작업을 사용 하는 네이티브 SSO 플러그 인 프로토콜을 구현 하므로이 설치 프로그램은 최종 사용자에 게 가장 부드럽습니다 기본 환경을 제공 합니다. 

SSO 플러그 인이 MDM에서 사용 하도록 설정 되어 있지 않지만 Microsoft Authenticator 앱이 장치에 있는 경우 MSAL은 대신 모든 대화형 토큰 요청에 대해 Microsoft Authenticator 앱을 사용 합니다. SSO 플러그 인은 Microsoft Authenticator 앱과 SSO를 공유 합니다.

### <a name="applications-that-dont-use-msal"></a>MSAL을 사용 하지 않는 응용 프로그램

MSAL을 사용 하지 않는 응용 프로그램은 관리자가 명시적으로 허용 목록에 추가 하는 경우에도 SSO를 가져올 수 있습니다. 

다음 조건이 충족 되는 경우 해당 앱에 필요한 코드 변경이 없습니다.

- 응용 프로그램에서 Apple 프레임 워크를 사용 하 여 네트워크 요청을 실행 합니다 (예: [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)). 
- 응용 프로그램에서 표준 프로토콜을 사용 하 여 Azure AD와 통신 하는 경우 (예: OAuth2, SAML, WS-FEDERATION)
- 응용 프로그램이 네이티브 UI에서 일반 텍스트 사용자 이름 및 암호를 수집 하지 않습니다.

이 경우 응용 프로그램에서 네트워크 요청을 만들고 사용자에 게 로그인 할 웹 브라우저를 열 때 SSO가 제공 됩니다. 사용자가 Azure AD 로그인 URL로 리디렉션되는 경우 SSO 플러그 인은 URL의 유효성을 검사 하 고 해당 URL에 사용할 수 있는 SSO 자격 증명이 있는지 확인 합니다. Sso 플러그 인이 있으면 sso 자격 증명을 Azure AD에 전달 합니다. 그러면 응용 프로그램에서 사용자에 게 자격 증명을 입력 하도록 요청 하지 않고 네트워크 요청을 완료할 수 있는 권한을 부여 합니다. 또한 장치가 Azure AD에 알려진 경우 SSO 플러그 인은 장치 기반 조건부 액세스 검사를 충족 하기 위해 장치 인증서를 전달 합니다. 

MSAL이 아닌 앱에 대해 SSO를 지원 하기 위해 SSO 플러그 인은 [기본 새로 고침 토큰 이란?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)에 설명 된 Windows 브라우저 플러그 인과 비슷한 프로토콜을 구현 합니다. 

MSAL 기반 앱에 비해 SSO 플러그 인은 앱에서 제공 하는 기존 브라우저 로그인 환경과 통합 되어 MSAL이 아닌 앱에 대해 더 투명 하 게 작동 합니다. 최종 사용자에 게 친숙 한 환경이 표시 되며 각 응용 프로그램에서 추가 로그인을 수행할 필요가 없다는 이점도 있습니다. 예를 들어, 기본 계정 선택기를 표시 하는 대신 SSO 플러그 인은 웹 기반 계정 선택 환경에 SSO 세션을 추가 합니다. 

## <a name="next-steps"></a>다음 단계

IOS의 공유 장치 모드에 대 한 자세한 내용은 [ios 장치에 대 한 공유 장치 모드](msal-ios-shared-devices.md)를 참조 하세요.

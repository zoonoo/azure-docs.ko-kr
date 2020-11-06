---
title: 개발 하는 mobile apps의 Single Sign-On 및 앱 보호 정책 지원 | Microsoft
titleSuffix: Microsoft identity platform
description: Single Sign-On 및 앱 보호 정책을 지 원하는 모바일 응용 프로그램 빌드에 대 한 설명 및 개요
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 09fc29b54951d2f33acc725c734e01e3d3364ff0
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331609"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>개발 하는 mobile apps의 Single Sign-On 및 앱 보호 정책 지원

SSO (Single sign-on)는 앱 사용자에 게 쉽고 안전한 로그인을 제공 합니다. 앱 보호 정책 (앱)을 사용 하면 사용자의 데이터를 안전 하 게 유지 하는 키 보안 정책을 지원할 수 있습니다. 이러한 기능을 함께 사용 하면 앱 데이터를 안전 하 게 사용자 로그인 및 관리할 수 있습니다.

이 문서에서는 SSO와 앱이 중요 한 이유를 설명 하 고 이러한 기능을 지 원하는 모바일 응용 프로그램을 빌드하기 위한 개략적인 지침을 제공 합니다. 이는 휴대폰 및 태블릿 앱 모두에 적용 됩니다. 조직의 Azure Active Directory 테 넌 트에 SSO를 배포 하려는 IT 관리자 인 경우 [Single Sign-On 배포 계획에 대 한 지침](../manage-apps/plan-sso-deployment.md) 을 확인 하세요.

## <a name="about-single-sign-on-and-app-protection-policies"></a>Single Sign-On 및 앱 보호 정책 정보

[Sso (Single sign-on)](../manage-apps/plan-sso-deployment.md) 를 사용 하면 사용자가 한 번 로그인 하 고 자격 증명을 다시 입력 하지 않고 다른 응용 프로그램에 액세스할 수 있습니다. 이렇게 하면 앱에 더 쉽게 액세스할 수 있고 사용자가 긴 사용자 이름 및 암호 목록을 기억할 필요가 없습니다. 앱에서 구현 하면 앱에 쉽게 액세스 하 고 사용할 수 있습니다.

또한 앱에서 Single Sign-On를 사용 하도록 설정 하면 [암호 없는 로그인](../authentication/concept-authentication-passwordless.md)과 같은 최신 인증과 함께 제공 되는 새 인증 메커니즘이 잠금 해제 됩니다. 사용자 이름 및 암호는 응용 프로그램에 대해 가장 널리 사용 되는 공격 벡터 중 하나 이며 SSO를 사용 하도록 설정 하면 보안을 강화 하거나 보다 안전한 인증 메커니즘을 사용 하는 조건부 액세스 또는 암호 없는 로그인을 적용 하 여이 위험을 완화할 수 있습니다. 마지막으로 Single Sign-On를 사용 하도록 설정 하면 [단일 로그 아웃](v2-protocols-oidc.md#single-sign-out)도 사용할 수 있습니다. 이는 공유 장치에서 사용 되는 회사 응용 프로그램과 같은 상황에서 유용 합니다.

[앱 보호 정책 (앱)](/mem/intune/apps/app-protection-policy) 은 조직의 데이터가 안전 하 게 유지 되 고 포함 되어 있는지 확인 합니다. 이를 통해 회사는 앱 내에서 데이터를 관리 하 고 보호할 수 있으며 앱 및 해당 데이터에 액세스할 수 있는 사용자를 제어할 수 있습니다. 앱 보호 정책을 구현 하면 앱이 조건부 액세스 정책에 의해 보호 되는 리소스에 사용자를 연결 하 고 보호 된 다른 앱 간에 데이터를 안전 하 게 전송할 수 있습니다. 앱 보호 정책에 의해 잠금 해제 된 시나리오에는 앱을 열 때 PIN을 요구 하 고, 앱 간의 데이터 공유를 제어 하 고, 회사 앱 데이터가 개인 저장소 위치에 저장 되지 않도록 하는 작업이 포함 됩니다.

## <a name="implementing-single-sign-on"></a>Single Sign-On 구현

앱이 Single Sign-On를 활용할 수 있도록 하려면 다음을 권장 합니다.

### <a name="use-microsoft-authentication-library-msal"></a>MSAL (Microsoft 인증 라이브러리) 사용

응용 프로그램에서 Single Sign-On을 구현 하는 가장 좋은 방법은 [MSAL (Microsoft 인증 라이브러리)](msal-overview.md)을 사용 하는 것입니다. MSAL을 사용 하면 최소한의 코드 및 API 호출을 사용 하 여 앱에 인증을 추가 하 고 [microsoft id 플랫폼](/azure/active-directory/develop/)의 전체 기능을 활용할 수 있으며 microsoft에서 보안 인증 솔루션의 유지 관리를 처리할 수 있습니다. 기본적으로 MSAL은 응용 프로그램에 대 한 SSO 지원을 추가 합니다. 또한 앱 보호 정책을 구현 하려는 경우 MSAL을 사용 하는 것이 요구 사항입니다.

> [!NOTE]
> MSAL을 구성 하 여 포함 된 웹 보기를 사용할 수 있습니다. 그러면 Single Sign-On 되지 않습니다. 기본 동작 (즉, 시스템 웹 브라우저)을 사용 하 여 SSO가 작동 하는지 확인 합니다.

현재 응용 프로그램에서 [adal 라이브러리](../azuread-dev/active-directory-authentication-libraries.md) 를 사용 하 고 있는 경우 [adal이 더 이상](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)사용 되지 않으므로 [msal으로 마이그레이션하](msal-migration.md)는 것이 좋습니다.

IOS 응용 프로그램의 경우 MSAL을 사용 하 여 로그인을 설정 하는 방법 및 [다양 한 SSO 시나리오에 대 한 MSAL을 구성](single-sign-on-macos-ios.md)하는 지침을 보여 주는 [빠른](quickstart-v2-ios.md) 시작을 제공 합니다.

Android 응용 프로그램의 경우 MSAL을 사용 하 여 로그인을 설정 하는 방법과 [MSAL을 사용 하 여 android에서 앱 간 SSO를 사용 하도록](msal-android-single-sign-on.md)설정 하는 방법에 대 한 지침을 보여 주는 [빠른](quickstart-v2-android.md) 시작을 제공 합니다.

### <a name="use-the-system-web-browser"></a>시스템 웹 브라우저 사용

대화형 인증에는 웹 브라우저가 필요 합니다. MSAL 이외의 최신 인증 라이브러리를 사용 하는 모바일 앱 (즉, 다른 Openid connect Connect 또는 SAML 라이브러리)의 경우 또는 자체 인증 코드를 구현 하는 경우 시스템 브라우저를 인증 화면으로 사용 하 여 SSO를 사용 하도록 설정 해야 합니다.

Google에는 Android 응용 프로그램: [Chrome 사용자 지정 탭-Google Chrome](https://developer.chrome.com/multidevice/android/customtabs)에서이 작업을 수행 하기 위한 지침이 있습니다.

Apple에는 iOS 응용 프로그램에서 [웹 서비스를 통해 사용자 인증 |을 수행 하기 위한 지침이 있습니다. Apple 개발자 설명서](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service)

> [!TIP]
> [Apple 장치에 대 한 sso 플러그](apple-sso-plugin.md) 인은 Intune을 사용 하 여 관리 되는 장치에서 포함 된 웹 보기를 사용 하는 iOS 앱에 sso를 허용 합니다. 모든 사용자에 대해 SSO를 사용 하도록 설정 하는 앱을 개발 하는 데 가장 적합 한 옵션인 MSAL 및 시스템 브라우저를 사용 하는 것이 좋지만, 그렇지 않은 일부 시나리오에서는 SSO를 사용할 수 있습니다.

## <a name="enable-app-protection-policies"></a>앱 보호 정책 사용

앱 보호 정책을 사용 하도록 설정 하려면 [MSAL (Microsoft 인증 라이브러리)](msal-overview.md)을 사용 합니다. MSAL은 Microsoft id 플랫폼의 인증 및 권한 부여 라이브러리 이며 Intune SDK는 함께 작동 하도록 개발 되었습니다.

또한 인증을 위해 broker 앱을 사용 해야 합니다. Broker를 사용 하려면 앱이 앱을 준수 하도록 응용 프로그램 및 장치 정보를 제공 해야 합니다. iOS 사용자는 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-sign-in.md) 을 사용 하며, Android 사용자는 Microsoft Authenticator 앱 또는 [회사 포털 앱](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 을 사용 하 여 조정 된 [인증](brokered-auth.md)을 받습니다. 기본적으로 MSAL은 인증 요청을 수행 하기 위한 첫 번째 옵션으로 broker를 사용 하므로, MSAL을 사용 하는 경우 broker를 사용 하 여 자동으로 앱에 대 한 인증을 사용할 수 있습니다.

마지막으로 앱에 [INTUNE SDK를 추가](/mem/intune/developer/app-sdk-get-started) 하 여 앱 보호 정책을 사용 하도록 설정 합니다. 대부분의 파트에 대 한 SDK는 가로채기 모델을 따르며 앱 보호 정책을 자동으로 적용 하 여 앱에서 수행 하는 작업이 허용 되는지 여부를 확인 합니다. 특정 작업에 대 한 제한이 있는 경우 앱에 알리기 위해 수동으로 호출할 수 있는 Api도 있습니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory Single Sign-On 배포 계획](../manage-apps/plan-sso-deployment.md)
- [방법: macOS 및 iOS에서 SSO 구성](single-sign-on-macos-ios.md)
- [Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인 (미리 보기)](apple-sso-plugin.md)
- [Android에서 조정 된 인증](brokered-auth.md)
- [권한 부여 에이전트 및 사용 하도록 설정 하는 방법](authorization-agents.md)
- [Microsoft Intune 앱 SDK 시작](/mem/intune/developer/app-sdk-get-started)
- [Intune 앱 SDK에 대한 설정 구성](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Microsoft Intune 보호 앱](/mem/intune/apps/apps-supported-intune-apps)

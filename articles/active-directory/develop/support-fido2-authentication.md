---
title: 개발 하는 앱에서 FIDO2 키를 사용 하 여 암호 없는 인증 지원 | Microsoft
titleSuffix: Microsoft identity platform
description: 이 배포 가이드에서는 개발 중인 응용 프로그램에서 FIDO2 보안 키로 암호 없는 인증을 지 원하는 방법을 설명 합니다.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174601"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>개발 하는 앱에서 FIDO2 키로 암호 없는 인증 지원

이러한 구성과 모범 사례는 응용 프로그램 사용자가 [FIDO2 암호 없는 인증](../../active-directory/authentication/concept-authentication-passwordless.md) 을 사용할 수 없도록 차단 하는 일반적인 시나리오를 피하는 데 도움이 됩니다.

## <a name="general-best-practices"></a>일반적인 유용한 정보

### <a name="domain-hints"></a>도메인 힌트

도메인 힌트를 사용 하 여 [홈 영역 검색](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)을 바이패스 하지 마십시오. 이 기능은 로그인을 보다 효율적으로 만들기 위한 것 이지만 페더레이션 id 공급자는 암호 없는 인증을 지원 하지 않을 수 있습니다.

### <a name="requiring-specific-credentials"></a>특정 자격 증명 요구

SAML을 사용 하는 경우 [RequestedAuthnContext 요소를 사용 하 여](single-sign-on-saml-protocol.md#requestauthncontext)암호가 필요 하도록 지정 하지 마십시오.

RequestedAuthnContext 요소는 선택 사항 이므로이 문제를 해결 하려면 SAML 인증 요청에서 해당 요소를 제거할 수 있습니다. 이 요소를 사용 하면 다단계 인증과 같은 다른 인증 옵션이 제대로 작동 하지 않을 수도 있으므로이 방법은 일반적인 모범 사례입니다.

### <a name="using-the-most-recently-used-authentication-method"></a>가장 최근에 사용한 인증 방법 사용

사용자가 가장 최근에 사용한 로그인 방법이 먼저 표시 됩니다. 사용자가 제공 하는 첫 번째 옵션을 사용 해야 한다고 생각 하는 경우 혼동을 일으킬 수 있습니다. 그러나 아래와 같이 "다른 로그인 방법"을 선택 하 여 다른 옵션을 선택할 수 있습니다.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="사용자가 인증 방법을 변경할 수 있도록 하는 단추를 강조 표시 하는 사용자 인증 경험 이미지입니다.":::

## <a name="platform-specific-best-practices"></a>플랫폼별 모범 사례

### <a name="desktop"></a>데스크톱

인증을 구현 하는 데 권장 되는 옵션은 다음과 같습니다.

- MSAL (Microsoft 인증 라이브러리)을 사용 하는 .NET 데스크톱 응용 프로그램은 WAM (Windows 인증 관리자)를 사용 해야 합니다. 이러한 통합 및 해당 혜택은 [GitHub에 설명](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam)되어 있습니다.
- [WebView2](/microsoft-edge/webview2/) 를 사용 하 여 포함 된 브라우저에서 FIDO2을 지원 합니다.
- 시스템 브라우저를 사용 합니다. 데스크톱 플랫폼용 MSAL 라이브러리는 기본적으로이 방법을 사용 합니다. FIDO2 브라우저 호환성 페이지를 참조 하 여 사용 하는 브라우저가 FIDO2 인증을 지원 하는지 확인할 수 있습니다.

### <a name="mobile"></a>휴대폰

2021 년 2 월부터 FIDO2는 현재 네이티브 iOS 또는 Android 앱에 대해 지원 되지 않지만 개발 중입니다.

응용 프로그램의 가용성을 위해 응용 프로그램을 준비 하는 일반적인 방법으로, iOS 및 Android 응용 프로그램은 시스템 웹 브라우저를 사용 하는 기본 구성으로 MSAL을 사용 해야 합니다.

MSAL을 사용 하지 않는 경우에도 인증을 위해 시스템 웹 브라우저를 사용 해야 합니다. Single Sign-On 및 조건부 액세스와 같은 기능은 시스템 웹 브라우저에서 제공 하는 공유 웹 화면을 사용 합니다. 이는 [Chrome 사용자 지정 탭](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android)을 사용 하거나 [웹 서비스를 통해 사용자를 인증 하는 것을 의미 합니다. | Apple 개발자 설명서](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>웹 및 단일 페이지 앱

웹 브라우저에서 실행 되는 응용 프로그램에 대 한 FIDO2 암호 없는 인증의 가용성은 브라우저와 플랫폼의 조합에 따라 달라 집니다. [FIDO2 호환성 매트릭스](../authentication/fido2-compatibility.md) 를 참조 하 여 사용자에 게 발생 하는 조합이 지원 되는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory에 대 한 암호 없는 인증 옵션](../../active-directory/authentication/concept-authentication-passwordless.md)
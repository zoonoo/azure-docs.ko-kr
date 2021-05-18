---
title: 웹 API를 호출하는 모바일 앱 빌드 | Azure
titleSuffix: Microsoft identity platform | Azure
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다(개요).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a678e8545f761e98abbdf026886afa624f9d2b4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753651"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 모바일 애플리케이션

웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다.

## <a name="getting-started"></a>시작

아직 애플리케이션이 없는 경우 빠른 시작을 완료하여 첫 번째 애플리케이션을 만듭니다.

- [빠른 시작: Android 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-android.md)
- [빠른 시작: iOS 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-ios.md)
- [빠른 시작: Xamarin iOS와 Android 앱에서 토큰 획득 및 Microsoft Graph API 호출](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>개요

모바일 앱에는 개인화된 원활한 사용자 환경이 필요합니다. Microsoft ID 플랫폼을 사용하여 모바일 개발자가 iOS 및 Android 사용자를 위한 환경을 만들 수 있습니다. 애플리케이션에 Azure AD(Azure Active Directory) 사용자, 개인 Microsoft 계정 사용자 및 Azure AD B2C 사용자로 로그인할 수 있습니다. 웹 API를 대신 호출하기 위해 토큰을 획득합니다. 이러한 흐름을 구현하려면 MSAL(Microsoft 인증 라이브러리)을 사용합니다. MSAL은 업계 표준 [OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 구현합니다.

![디먼 앱](./media/scenarios/mobile-app.svg)

모바일 앱에 대한 고려 사항:

- **사용자 환경의 중요성**: 사용자가 로그인을 요청하기 전에 앱의 값을 확인할 수 있도록 허용합니다. 필요한 권한만 요청합니다.
- **모든 사용자 구성 지원**: 많은 모바일 비즈니스 사용자가 조건부 액세스 정책 및 디바이스 규정 준수 정책을 따라야 합니다. 이러한 주요 시나리오를 지원해야 합니다.
- **SSO(Single Sign-On) 구현**: MSAL 및 Microsoft ID 플랫폼을 사용하여 디바이스의 브라우저 또는 Microsoft Authenticator(및 Android의 Intune 회사 포털)를 통해 Single Sign-On을 사용하도록 설정할 수 있습니다.
- **공유 디바이스 모드 구현**: 병원, 제조, 소매, 재무 등의 공유 디바이스 시나리오에서 애플리케이션을 사용하도록 설정합니다. [공유 디바이스 모드 지원에 대해 더 자세히 알아봅니다](msal-shared-devices.md).

## <a name="specifics"></a>특수 적용 사항

Microsoft ID 플랫폼에서 모바일 앱을 빌드하는 경우 다음 사항을 고려해야 합니다.

- 플랫폼에 따라 사용자가 처음으로 로그인할 때 일부 사용자 조작이 필요할 수 있습니다. 예를 들어 iOS에서는 앱이 Microsoft Authenticator(및 Android의 Intune 회사 포털)를 통해 처음으로 SSO를 사용할 때 사용자 조작을 표시해야 합니다.
- iOS 및 Android에서 MSAL은 외부 브라우저를 사용하여 사용자를 로그인할 수 있습니다. 외부 브라우저가 앱 위에 표시될 수 있습니다.
- 모바일 애플리케이션에서는 비밀을 사용하지 마세요. 이러한 애플리케이션에서는 모든 사용자가 비밀에 액세스할 수 있습니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 등록](scenario-mobile-app-registration.md)으로 이동합니다.

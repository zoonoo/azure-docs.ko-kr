---
title: 웹 API를 호출하는 모바일 앱 빌드 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법 알아보기(개요)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132428"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 모바일 응용 프로그램

웹 API를 호출하는 모바일 앱을 빌드하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

첫 번째 모바일 응용 프로그램을 만들고 빠른 시작을 시도하십시오.

> [!div class="nextstepaction"]
> [빠른 시작 : 토큰을 획득하고 안드로이드 응용 프로그램에서 마이크로 소프트 그래프 API를 호출](./quickstart-v2-android.md)
>
> [빠른 시작: 토큰을 획득하고 iOS 앱에서 Microsoft 그래프 API를 호출합니다.](./quickstart-v2-ios.md)
>
> [빠른 시작: 토큰을 획득 하 고 자마린 iOS와 안 드 로이드 애플 리 케이 션에서 마이크로소프트 그래프 API를 호출](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>개요

개인화되고 원활한 사용자 환경은 모바일 앱에 필수적입니다.  Microsoft ID 플랫폼을 사용하면 모바일 개발자가 iOS 및 Android 사용자를 위한 환경을 만들 수 있습니다. 응용 프로그램은 Azure Active Directory(Azure AD) 사용자, 개인 Microsoft 계정 사용자 및 Azure AD B2C 사용자에 로그인할 수 있습니다. 또한 토큰을 획득하여 대신 웹 API를 호출할 수도 있습니다. 이러한 흐름을 구현하려면 MSAL(Microsoft 인증 라이브러리)을 사용합니다. MSAL은 업계 표준 [OAuth2.0 권한 부여 코드 흐름을](v2-oauth2-auth-code-flow.md)구현합니다.

![디먼 앱](./media/scenarios/mobile-app.svg)

모바일 앱의 고려 사항:

- **사용자 환경이 핵심입니다:** 사용자가 로그인을 요청하기 전에 앱의 가치를 볼 수 있도록 합니다. 필요한 권한만 요청합니다.
- **모든 사용자 구성 지원**: 많은 모바일 비즈니스 사용자는 조건부 액세스 정책 및 장치 준수 정책을 준수해야 합니다. 이러한 주요 시나리오를 지원해야 합니다.
- **단일 사인온(SSO) 구현**: MSAL 및 Microsoft ID 플랫폼을 사용하여 장치의 브라우저 또는 Microsoft 인증자(Android의 Intune 회사 포털)를 통해 단일 사인온을 활성화할 수 있습니다.

## <a name="specifics"></a>구체적인

Microsoft ID 플랫폼에서 모바일 앱을 빌드할 때 다음 사항을 고려해야 합니다.

- 플랫폼에 따라 사용자가 처음 로그인할 때 일부 사용자 상호 작용이 필요할 수 있습니다. 예를 들어 iOS는 Microsoft 인증자(및 Android의 Intune 회사 포털)를 통해 처음으로 SSO를 사용할 때 사용자 상호 작용을 표시하는 앱이 필요합니다.
- iOS 및 Android에서 MSAL은 외부 브라우저를 사용하여 사용자를 로그인할 수 있습니다. 외부 브라우저가 앱 위에 나타날 수 있습니다. 대신 인앱 WebViews를 사용하도록 구성을 사용자 지정할 수 있습니다.
- 모바일 응용 프로그램에서 비밀을 사용하지 마십시오. 이러한 응용 프로그램에서는 모든 사용자가 비밀에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-mobile-app-registration.md)

---
title: 웹 Api를 호출 하는 모바일 앱 개요 | Microsoft id 플랫폼
description: 모바일 앱을 빌드하는 방법을 알아보려면 웹 Api (개요)를 호출 되는
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076497"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>시나리오: 웹 Api를 호출 하는 모바일 응용 프로그램

웹 Api를 호출 하는 모바일 앱을 만드는 데 필요한 모든에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

첫 번째 모바일 응용 프로그램을 만들고 빠른 시작을 사용해 보십시오!

> [!div class="nextstepaction"]
> [빠른 시작: 토큰을 획득 하 고 Android 앱에서 Microsoft Graph API 호출](./quickstart-v2-android.md)
>
> [빠른 시작: 토큰을 획득 하 고 iOS 앱에서 Microsoft Graph API 호출](./quickstart-v2-ios.md)
>
> [빠른 시작: 토큰을 획득 하 고 Xamarin iOS 및 Android 앱에서 Microsoft Graph API 호출](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>개요

모바일 앱을 빌드하는 경우 개인 설정 된, 원활 하 게 최종 사용자 경험을 반드시 필요 합니다.  Microsoft id 플랫폼에는 모바일 개발자가 iOS 및 Android 사용자에 대 한 정확 하 게이 작업을 수행할 수 있습니다. 응용 프로그램이 Azure AD에 개인 Microsoft 계정으로 로그인 할 수 있습니다 및 Azure AD B2C 사용자 본인을 대신해 web API를 호출 하는 토큰을 가져옵니다. Microsoft 인증 라이브러리 (MSAL)는 업계 표준을 구현 하는 이러한 흐름을 구현 하려면 사용 [oauth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)합니다.

![디먼 앱](./media/scenarios/mobile-app.svg)

모바일 앱 고려 사항:

- ***사용자 환경은 키***: 사용자가 로그인을 요청 하기 전에 앱의 값을 확인할 수 있도록 하 고만 필요한 권한을 요청 합니다.
- ***모든 사용자 구성을 지원***: 많은 모바일 비즈니스 사용자가 장치 준수 정책 및 조건부 액세스 됩니다. 이러한 주요 시나리오를 지원 해야 합니다.
- ***구현에서 single sign-on (SSO)***: MSAL 및 Microsoft id 플랫폼 간단 하 게에서 single sign-on 사용 하도록 설정 하면 장치의 브라우저 또는 Microsoft Authenticator (및 Android에서 Intune 회사 포털)을 통해.

## <a name="specifics"></a>세부 정보

Microsoft id 플랫폼에서 모바일 앱을 빌드하는 경우 종단 간 환경에 몇 가지 고려 사항이 있습니다.

- 플랫폼에 따라서는 상호 작용 없이 로그인 하지 못할 첫 번째 로그인 합니다. 예를 들어 iOS에서는 앱이 Microsoft Authenticator와 Android에서 Intune 회사 포털을 통해 SSO 첫 번째 시간을 가져올 때 사용자 상호 작용을 표시 합니다.
- IOS 및 Android에서 MSAL 사용자 로그인에 외부를 사용 하는 브라우저 (에 앱을 기반으로 나타날 수 있음)를 사용할 수 있습니다. 이 앱은 웹 보기를 대신 사용 하도록 사용자 지정할 수 있습니다.
- 모바일 응용 프로그램에서 비밀을 사용 하지, 모든 사용자가 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-mobile-app-registration.md)

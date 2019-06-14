---
title: Microsoft id 플랫폼 (개요)-사용자가 로그인 하는 웹 앱
description: (개요) 사용자가 로그인 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833094"
---
# <a name="scenario-web-app-that-signs-in-users"></a>시나리오: 사용자가 로그인하는 웹앱

하기만 하면 Microsoft id 플랫폼을 사용 하 여 로그인 사용자가 웹 앱 빌드에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

사용자가 로그인 하는 이식 가능한 첫 번째 (ASP.NET Core) 웹 앱을 만들려는 경우이 빠른 시작을 수행 합니다.

> [!div class="nextstepaction"]
> [빠른 시작: ASP.NET Core 웹 앱 로그인 사용자](quickstart-v2-aspnet-core-webapp.md)

ASP.NET을 사용 하 여 상태를 유지 하려는 경우 다음 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [빠른 시작: ASP.NET 웹 앱 로그인 사용자](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>개요

사용자가 로그인 할 수 있도록 웹 앱에 인증을 추가 합니다. 추가 인증 웹 앱이 제한 된 프로필 정보를 액세스 하 고 예를 들어 사용자에 게 제공 환경을 사용자 지정할 수 있도록 합니다. 웹 브라우저에서 사용자를 인증 하는 웹 앱입니다. 이 시나리오에서는 웹 애플리케이션이 사용자의 브라우저를 Azure AD에 로그인하도록 지시합니다. Azure AD는 보안 토큰에 사용자에 대한 클레임이 포함된 로그인 응답을 사용자의 브라우저를 통해 반환합니다. 로그인 한 사용자가 활용 합니다 [Open ID Connect](./v2-protocols-oidc.md) 미들웨어를 사용 하 여 간소화 된 자체 표준 프로토콜 [라이브러리](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)합니다.

![웹 앱 로그인 사용자](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

두 번째 단계에서 로그인 한 사용자 대신 Web Api를 호출 하도록 응용 프로그램을 사용할 수도 있습니다. 이 다음 단계에서 찾을 수 있는 다른 시나리오는 [웹 Api를 호출 하는 웹 앱](scenario-web-app-call-api-overview.md)

> [!NOTE]
> 웹 앱은 웹 앱을 보호 하는 방법에 대 한 로그인 추가 및 사용자 토큰의 유효성을 검사 하는 것 **미들웨어** 라이브러리입니다. 이 시나리오에 필요 하지 않습니다 하지만 인증 라이브러리 (MSAL (Microsoft)를 호출 하는 토큰을 획득 하는 방법에 대 한는 Api를 보호 합니다. 웹 앱에서 web Api를 호출 해야 하는 경우에 인증 라이브러리 후속 시나리오에서 소개 합니다.

## <a name="specifics"></a>세부 정보

- 응용 프로그램 등록 중 하나를 제공 해야 하거나 (여러 위치에 앱 배포) 하는 경우 여러 회신 Uri. 일부 사례 (ASP.NET/ASP.NET 코어)는 IDToken 사용 하도록 설정 해야 합니다. 마지막으로 사용자가 서명 스케일 아웃에 반응 하는 응용 프로그램 로그 아웃 URI를 설정 합니다.
- 응용 프로그램에 대 한 코드에서 웹 앱 로그인 대리자는 권한을 제공 해야 합니다. 토큰 유효성 검사 (ISV 시나리오에서 특히)에서 사용자 지정 하려는 경우.
- 웹 응용 프로그램에는 모든 계정 유형을 지원 합니다. 자세한 내용은 참조 하세요. [지원 되는 계정 유형](v2-supported-account-types.md)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-web-app-sign-user-app-registration.md)

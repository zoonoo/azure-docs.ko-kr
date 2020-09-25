---
title: 웹 Api를 호출 하는 웹 앱 빌드-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱을 빌드하는 방법 알아보기 (개요)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 174db339079de91a87cef9551bc79e6f03701605
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257132"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>시나리오: 웹 Api를 호출 하는 웹 앱

Microsoft id 플랫폼에 사용자를 로그인 하는 웹 앱을 빌드한 다음 로그인 한 사용자를 대신 하 여 web Api를 호출 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 시나리오 [에서는 사용자에 게 로그인 하는 웹 앱 시나리오](scenario-web-app-sign-user-overview.md)를 이미 완료 했다고 가정 합니다.

## <a name="overview"></a>개요

로그인 한 사용자를 대신 하 여 사용자를 로그인 하 고 web API를 호출할 수 있도록 웹 앱에 인증을 추가 합니다.

![Web API를 호출하는 웹앱](./media/scenario-webapp/web-app.svg)

웹 Api를 호출 하는 웹 앱은 기밀 클라이언트 응용 프로그램입니다.
따라서 Azure Active Directory (Azure AD)를 사용 하 여 비밀 (응용 프로그램 암호 또는 인증서)을 등록 합니다. 이 암호는 토큰을 가져오기 위해 Azure AD를 호출 하는 동안 전달 됩니다.

## <a name="specifics"></a>특수 적용 사항

> [!NOTE]
> 웹 앱에 로그인을 추가 하는 것은 웹 앱 자체를 보호 하는 것입니다. 이러한 보호는 MSAL (Microsoft 인증 라이브러리)이 아닌 *미들웨어* 라이브러리를 사용 하 여 구현 됩니다. 앞의 시나리오에서는 [사용자를 로그인 하는 웹 앱](scenario-web-app-sign-user-overview.md)이 해당 주제에 대해 설명 했습니다.
>
> 이 시나리오에서는 웹 앱에서 web Api를 호출 하는 방법을 설명 합니다. 이러한 웹 Api에 대 한 액세스 토큰을 가져와야 합니다. MSAL 라이브러리를 사용 하 여 이러한 토큰을 가져옵니다.

이 시나리오에 대 한 개발에는 다음과 같은 특정 작업이 포함 됩니다.

- [응용 프로그램을 등록](scenario-web-app-call-api-app-registration.md)하는 동안 Azure AD와 공유할 회신 URI, 비밀 또는 인증서를 제공 해야 합니다. 앱을 여러 위치에 배포 하는 경우 각 위치에 대해 회신 URI를 제공 합니다.
- 응용 프로그램 [구성](scenario-web-app-call-api-app-configuration.md) 에서는 응용 프로그램을 등록 하는 동안 Azure AD와 공유 된 클라이언트 자격 증명을 제공 해야 합니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 웹 앱: 앱 등록](scenario-web-app-call-api-app-registration.md)

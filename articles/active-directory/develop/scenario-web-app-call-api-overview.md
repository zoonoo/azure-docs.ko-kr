---
title: 웹 Api를 호출 하는 웹 앱 빌드-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱을 빌드하는 방법 알아보기 (개요)
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
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962137"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>시나리오: 웹 Api를 호출 하는 웹 앱

Microsoft id 플랫폼에서 사용자에 게 로그인 하 고 로그인 한 사용자를 대신 하 여 web Api를 호출 하는 웹 앱을 빌드하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

이 시나리오는 다음과 같은 경우에 발생 합니다.

> [!div class="nextstepaction"]
> [사용자를 로그인 하는 웹 앱](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>개요

웹 앱에 인증을 추가 하 여 로그인 한 사용자를 대신 하 여 사용자를 로그인 하 고 web API를 호출할 수 있습니다.

![Web API를 호출하는 웹앱](./media/scenario-webapp/web-app.svg)

웹 Api를 호출 하는 Web Apps:

- 기밀 클라이언트 응용 프로그램입니다.
- 이러한 이유로 Azure AD에 비밀 (응용 프로그램 암호 또는 인증서)을 등록 했습니다. 이 암호는 토큰을 가져오기 위해 Azure AD를 호출 하는 동안 전달 됩니다.

## <a name="specifics"></a>자세히

> [!NOTE]
> 웹 앱에 로그인을 추가 하는 것은 웹 앱을 보호 하는 것 이기 때문에 MSAL 라이브러리를 사용 하지 않습니다. 라이브러리 보호는 미들웨어 라는 라이브러리를 통해 수행 됩니다. [웹 앱에 대](scenario-web-app-sign-user-overview.md) 한 이전 시나리오 로그인 사용자의 개체입니다.
>
> 웹 앱에서 웹 Api를 호출 하는 경우 이러한 웹 Api에 대 한 액세스 토큰을 가져와야 합니다. MSAL 라이브러리를 사용 하 여 이러한 토큰을 가져올 수 있습니다.

이 시나리오에 대 한 개발자의 종단 간 환경은 다음과 같이 특정 측면을 포함 합니다.

- [응용 프로그램을 등록](scenario-web-app-call-api-app-registration.md)하는 동안 여러 위치에 앱을 배포 하는 경우 Azure AD와 공유 해야 하는 회신 uri, 비밀 또는 인증서를 제공 해야 합니다.
- 응용 프로그램 [구성](scenario-web-app-call-api-app-configuration.md) 에서 응용 프로그램을 등록 하는 동안 Azure AD와 공유 되는 클라이언트 자격 증명을 제공 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-web-app-call-api-app-registration.md)

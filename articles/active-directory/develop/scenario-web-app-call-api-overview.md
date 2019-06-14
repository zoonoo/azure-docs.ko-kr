---
title: 호출 웹 Api (개요)-Microsoft id 플랫폼에는 웹 앱
description: 웹 앱을 빌드하는 방법을 알아보려면 웹 Api (개요)를 호출 되는
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076302"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>시나리오: Web API를 호출하는 웹앱

Microsoft id 플랫폼에서 웹 앱에 로그인 한 사용자를 빌드하는 방법을 알아보고에서 로그인 한 사용자 대신 웹 Api를 호출 하는 합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

이 시나리오는 다음 시나리오를 통해 겪고 supposes:

> [!div class="nextstepaction"]
> [웹 앱 로그인 사용자](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>개요

따라서 사용자가에 로그인에서 로그인 한 사용자 대신 web API를 호출 하는 웹 앱에 인증을 추가 합니다.

![Web API를 호출하는 웹앱](./media/scenario-webapp/web-app.svg)

Web Api를 호출 하는 웹 앱:

- 기밀 클라이언트 응용 프로그램입니다.
- 이유는 Azure AD를 사용 하 여 비밀 (응용 프로그램 암호 또는 인증서) 등록 했습니다. 이 비밀 토큰을 가져오려면 Azure AD 호출 하는 동안 전달 됩니다.

## <a name="specifics"></a>세부 정보

> [!NOTE]
> 웹 앱에 로그인 추가 웹 앱을 보호 하는 방법에 대 한이 그대로 MSAL 라이브러리를 사용 하지 않습니다. 라이브러리 보호 라는 미들웨어 라이브러리를 통해 이루어집니다. 이 이전 시나리오의 개체 [웹 앱에 사용자 로그인](scenario-web-app-sign-user-overview.md)
>
> 웹 앱에서 web Api를 호출할 때 이러한 웹 Api에 대 한 액세스 토큰을 가져올 해야 합니다. 이러한 토큰을 획득 하 MSAL 라이브러리를 사용할 수 있습니다.

이 시나리오에 대 한 개발자의 종단 간 환경에 따라서 특정 한 측면으로:

- 중 합니다 [응용 프로그램 등록](scenario-web-app-call-api-app-registration.md), 제공 해야 (여러 위치에 앱 배포) 하는 경우 여러 회신 Uri, 암호 또는 인증서를 Azure AD와 공유 해야 합니다.
- 합니다 [응용 프로그램 구성](scenario-web-app-call-api-app-configuration.md) 응용 프로그램 등록 하는 동안 Azure AD를 사용 하 여 공유로 클라이언트 자격 증명을 제공 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-web-app-call-api-app-registration.md)

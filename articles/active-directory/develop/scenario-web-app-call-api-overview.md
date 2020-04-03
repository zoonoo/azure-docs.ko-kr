---
title: 웹 API를 호출하는 웹 앱 빌드 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 웹 앱을 빌드하는 방법 알아보기(개요)
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
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617158"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 웹 앱

사용자를 Microsoft ID 플랫폼에 로그인한 다음 로그인한 사용자를 대신하여 웹 API를 호출하는 웹 앱을 빌드하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

이 시나리오에서는 다음 시나리오를 이미 거쳤다고 가정합니다.

> [!div class="nextstepaction"]
> [사용자에 서명하는 웹 앱](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>개요

로그인한 사용자를 대신하여 로그인하고 웹 API를 호출할 수 있도록 웹 앱에 인증을 추가합니다.

![Web API를 호출하는 웹앱](./media/scenario-webapp/web-app.svg)

웹 API를 호출하는 웹 앱은 기밀 클라이언트 응용 프로그램입니다.
그래서 Azure Active Directory(Azure AD)에 비밀(응용 프로그램 암호 또는 인증서)을 등록합니다. 이 비밀은 토큰을 얻기 위해 Azure AD를 호출하는 동안 전달됩니다.

## <a name="specifics"></a>구체적인

> [!NOTE]
> 웹 앱에 로그인을 추가하는 것은 웹 앱 자체를 보호하는 것입니다. 이러한 보호는 MSAL(Microsoft 인증 라이브러리)이 아닌 *미들웨어* 라이브러리를 사용하여 이뤄질 수 있습니다. 앞의 시나리오에서는 [사용자에 서명하는 웹 앱이](scenario-web-app-sign-user-overview.md)해당 주제를 다루었습니다.
>
> 이 시나리오에서는 웹 앱에서 웹 API를 호출하는 방법을 설명합니다. 이러한 웹 API에 대한 액세스 토큰을 받아야 합니다. MSAL 라이브러리를 사용하여 이러한 토큰을 획득합니다.

이 시나리오에 대한 개발에는 다음과 같은 특정 작업이 포함됩니다.

- [응용 프로그램 등록](scenario-web-app-call-api-app-registration.md)중에 Azure AD와 공유할 회신 URI, 비밀 또는 인증서를 제공해야 합니다. 앱을 여러 위치에 배포하는 경우 각 위치에 대해 이 정보를 제공합니다.
- [응용 프로그램 구성은](scenario-web-app-call-api-app-configuration.md) 응용 프로그램 등록 중에 Azure AD와 공유된 클라이언트 자격 증명을 제공해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹 앱: 앱 등록](scenario-web-app-call-api-app-registration.md)

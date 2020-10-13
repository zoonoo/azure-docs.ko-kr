---
title: JavaScript 단일 페이지 앱 시나리오-Microsoft identity platform | Microsoft
description: Microsoft id 플랫폼을 사용 하 여 단일 페이지 응용 프로그램 (시나리오 개요)을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc0aee2e6c1b06850ffd0385626955a9798e7aeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257319"
---
# <a name="scenario-single-page-application"></a>시나리오: 단일 페이지 응용 프로그램

SPA (단일 페이지 응용 프로그램)를 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="getting-started"></a>시작

아직 없는 경우 JavaScript SPA 빠른 시작을 완료 하 여 첫 번째 앱을 만듭니다.

[빠른 시작: 단일 페이지 응용 프로그램](./quickstart-v2-javascript.md)

## <a name="overview"></a>개요

많은 최신 웹 응용 프로그램은 클라이언트 쪽 단일 페이지 응용 프로그램으로 빌드됩니다. 개발자는 JavaScript를 사용 하 여, 또는의 SPA 프레임 워크 (예: 각도, Vue 및 반응)를 사용 하 여 작성 합니다. 이러한 응용 프로그램은 웹 브라우저에서 실행 되며 기존 서버 쪽 웹 응용 프로그램과는 다른 인증 특징이 있습니다.

Microsoft id 플랫폼은 단일 페이지 응용 프로그램이 사용자를 로그인 하 고 백 엔드 서비스 또는 웹 Api에 액세스 하는 토큰을 가져올 수 있도록 하는 **두 가지** 옵션을 제공 합니다.

- [OAuth 2.0 인증 코드 흐름 (PKCE 포함)](./v2-oauth2-auth-code-flow.md). 권한 부여 코드 흐름을 사용 하면 응용 프로그램이 **ID** 토큰에 대 한 권한 부여 코드를 교환 하 여 보호 된 api를 호출 하는 데 필요한 인증 된 사용자 및 **액세스** 토큰을 나타낼 수 있습니다. 또한 사용자를 대신 하 여 리소스에 대 한 장기 액세스를 제공 하는 **새로 고침** 토큰을 반환 합니다. 이것이 **권장** 되는 방법입니다.

![단일 페이지 응용 프로그램-인증](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 암시적 흐름](./v2-oauth2-implicit-grant-flow.md)입니다. 암시적 권한 부여 흐름을 사용 하면 응용 프로그램에서 **ID** 및 **액세스** 토큰을 가져올 수 있습니다. 권한 부여 코드 흐름과 달리 암시적 허용 흐름은 **새로 고침 토큰**을 반환 하지 않습니다.

![단일 페이지 응용 프로그램-암시적](./media/scenarios/spa-app.svg)

이 인증 흐름에는 전자 및 반응-네이티브와 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하는 응용 프로그램 시나리오가 포함 되지 않습니다. 네이티브 플랫폼과 상호 작용 하려면 추가 기능이 필요 합니다.

## <a name="specifics"></a>특수 적용 사항

응용 프로그램에 대해이 시나리오를 사용 하려면 다음이 필요 합니다.

* Azure Active Directory (Azure AD)를 사용 하 여 응용 프로그램 등록 등록 단계는 암시적 부여 흐름과 권한 부여 코드 흐름 간에 다릅니다.
* 응용 프로그램 ID와 같은 등록 된 응용 프로그램 속성이 포함 된 응용 프로그램 구성
* JavaScript 용 Microsoft Authentication Library (MSAL.js)를 사용 하 여 로그인 하 고 토큰을 획득 하는 인증 흐름을 수행 합니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-spa-app-registration.md)

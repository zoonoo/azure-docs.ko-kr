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
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 17acb2bc5e96a136f31371c0be912c2c758c0f76
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443860"
---
# <a name="scenario-single-page-application"></a>시나리오: 단일 페이지 애플리케이션

SPA (단일 페이지 응용 프로그램)를 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="getting-started"></a>시작

아직 없는 경우 JavaScript SPA 빠른 시작을 완료 하 여 첫 번째 앱을 만듭니다.

[빠른 시작: 단일 페이지 응용 프로그램](./quickstart-v2-javascript.md)

## <a name="overview"></a>개요

많은 최신 웹 응용 프로그램은 클라이언트 쪽 단일 페이지 응용 프로그램으로 빌드됩니다. 개발자는 JavaScript 또는 Angular, Vue, React 같은 SPA 프레임워크를 사용하여 단일 페이지 애플리케이션을 작성합니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다.

Microsoft id 플랫폼은 단일 페이지 응용 프로그램이 사용자를 로그인 하 고 백 엔드 서비스 또는 웹 Api에 액세스 하는 토큰을 가져올 수 있도록 하는 **두 가지** 옵션을 제공 합니다.

- [OAuth 2.0 인증 코드 흐름(PKCE 사용)](./v2-oauth2-auth-code-flow.md). 권한 부여 코드 흐름을 사용하면 인증된 사용자를 나타내는 **ID** 토큰과 보호된 API를 호출하는 데 필요한 **액세스** 토큰의 권한 부여 코드를 애플리케이션에서 교환할 수 있습니다. 뿐만 아니라 애플리케이션은 해당 사용자와의 상호 작용을 요구하지 않고 사용자 대신 리소스에 대한 장기 액세스 권한을 제공하는 **새로 고침** 토큰을 반환합니다. 이 방법을 사용하는 것이 **좋습니다**.

![단일 페이지 애플리케이션 인증](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 암시적 흐름](./v2-oauth2-implicit-grant-flow.md). 암시적 권한 부여 흐름을 사용하면 애플리케이션에서 **ID** 및 **액세스** 토큰을 얻을 수 있습니다. 권한 부여 코드 흐름과 달리, 암시적 허용 흐름은 **새로 고침 토큰** 을 반환하지 않습니다.

![단일 페이지 애플리케이션 암시적](./media/scenarios/spa-app.svg)

이 인증 흐름에는 Electron 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 애플리케이션 시나리오가 포함되지 않습니다. 네이티브 플랫폼과 상호 작용 하려면 추가 기능이 필요 합니다.

## <a name="specifics"></a>특수 적용 사항

응용 프로그램에 대해이 시나리오를 사용 하려면 다음이 필요 합니다.

* Azure Active Directory (Azure AD)를 사용 하 여 응용 프로그램 등록 등록 단계는 암시적 부여 흐름과 권한 부여 코드 흐름 간에 다릅니다.
* 응용 프로그램 ID와 같은 등록 된 응용 프로그램 속성이 포함 된 응용 프로그램 구성
* JavaScript 용 Microsoft Authentication Library (MSAL.js)를 사용 하 여 로그인 하 고 토큰을 획득 하는 인증 흐름을 수행 합니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서 [앱 등록](scenario-spa-app-registration.md)으로 이동 합니다.

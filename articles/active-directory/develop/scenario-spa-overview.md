---
title: JavaScript 단일 페이지 응용 프로그램 시나리오 개요-Microsoft identity platform
description: Microsoft id 플랫폼을 통합 하는 단일 페이지 응용 프로그램 (시나리오 개요)을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852510"
---
# <a name="scenario-single-page-application"></a>시나리오: 단일 페이지 애플리케이션

SPA (단일 페이지 응용 프로그램)를 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

JavaScript SPA 빠른 시작을 수행 하 여 첫 번째 응용 프로그램을 만들 수 있습니다.

> [!div class="nextstepaction"]
> [빠른 시작: 단일 페이지 응용 프로그램](./quickstart-v2-javascript.md)

## <a name="overview"></a>개요

많은 최신 웹 애플리케이션은 JavaScript 또는 Angular, Vue.js, React.js 같은 SPA 프레임워크를 사용하여 작성되는 클라이언트 쪽 단일 페이지 애플리케이션으로 빌드됩니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다. Microsoft id 플랫폼을 사용 하면 단일 페이지 응용 프로그램이 사용자에 게 로그인 하 고 토큰을 가져와서 [OAuth 2.0 암시적 흐름](./v2-oauth2-implicit-grant-flow.md)을 사용 하 여 백 엔드 서비스 또는 web api에 액세스할 수 있습니다. 암시적 흐름을 사용 하면 응용 프로그램에서 ID 토큰을 가져와서 인증 된 사용자를 나타낼 수 있으며, 보호 된 Api를 호출 하는 데 필요한 토큰에도 액세스할 수 있습니다.

![단일 페이지 애플리케이션](./media/scenarios/spa-app.svg)

이 인증 흐름에는 전자 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하는 응용 프로그램 시나리오 (예: 전자, 반응-네이티브 등)는 포함 되지 않습니다. 네이티브 플랫폼과의 상호 작용을 위해 추가 기능이 필요 하기 때문입니다.

## <a name="specifics"></a>자세히

응용 프로그램에 대해이 시나리오를 사용 하도록 설정 하려면 다음 요소가 필요 합니다.

* Azure AD로 응용 프로그램을 등록 하려면 암시적 흐름을 사용 하도록 설정 하 고 토큰이 반환 되는 리디렉션 URI를 설정 해야 합니다.
* 응용 프로그램 ID와 같은 등록 된 응용 프로그램 속성을 사용 하는 응용 프로그램 구성
* MSAL 라이브러리를 사용 하 여 로그인 하 고 토큰을 획득 하는 인증 흐름을 수행 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-spa-app-registration.md)

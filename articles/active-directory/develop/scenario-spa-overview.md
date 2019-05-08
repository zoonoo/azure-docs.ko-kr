---
title: JavaScript 단일 페이지 응용 프로그램 시나리오 개요-Microsoft id 플랫폼
description: Microsoft id 플랫폼과 통합 하는 단일 페이지 응용 프로그램 (시나리오 개요)를 빌드하는 방법에 알아봅니다.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076362"
---
# <a name="scenario-single-page-application"></a>시나리오: 단일 페이지 애플리케이션

하기만 하면 단일 페이지 응용 프로그램 (SPA) 빌드에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

JavaScript SPA 빠른 시작을 수행 하 여 첫 번째 응용 프로그램을 만들 수 있습니다.

> [!div class="nextstepaction"]
> [빠른 시작: 단일 페이지 응용 프로그램](./quickstart-v2-javascript.md)

## <a name="overview"></a>개요

많은 최신 웹 응용 프로그램은 JavaScript 또는 Angular, Vue.js, React.js 등 SPA 프레임 워크를 사용 하 여 작성 하는 클라이언트 쪽 단일 페이지 응용 프로그램으로 빌드됩니다. 이러한 응용 프로그램이은 웹 브라우저에서 실행 하 고 기존 서버 쪽 웹 응용 프로그램 보다 다양 한 인증 특징이. Microsoft id 플랫폼에 단일 페이지 응용 프로그램 백 엔드 서비스 또는 웹 Api를 사용 하 여 액세스 토큰을 가져오고 사용자 로그인을 사용 하도록 설정 합니다 [OAuth 2.0 암시적 흐름](./v2-oauth2-implicit-grant-flow.md)합니다. 암시적 흐름을 응용 프로그램을을 보호 된 Api를 호출 하는 데 필요한 토큰에 액세스할 수도 및 인증된 된 사용자를 나타내는 ID 토큰을 가져올 수 있습니다.

![단일 페이지 애플리케이션](./media/scenarios/spa-app.svg)

이 인증 흐름 등 전자, React Native 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하 여 응용 프로그램 시나리오를 다루지 않습니다. 이후 필요 추가 기능 네이티브 플랫폼과 상호 작용 합니다.

## <a name="specifics"></a>세부 정보

다음과 같은 측면을 응용 프로그램에 대 한이 시나리오를 사용 하도록 설정 하려면 필요 합니다.

* Azure AD 사용 하 여 응용 프로그램 등록 암시적 흐름을 사용 하도록 설정 하 고 토큰 반환 되는 리디렉션 URI를 설정 해야 합니다.
* 응용 프로그램 id입니다. 같은 등록된 응용 프로그램 속성을 사용 하 여 응용 프로그램 구성
* MSAL 라이브러리를 사용 하 여 로그인 하 고 토큰을 획득 하는 인증 흐름을 수행 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-spa-app-registration.md)

---
title: 단일 페이지 앱 등록-Microsoft identity platform | Microsoft
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (앱 등록)
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
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701895"
---
# <a name="single-page-application-app-registration"></a>단일 페이지 응용 프로그램: 앱 등록

이 페이지에서는 SPA (단일 페이지 응용 프로그램)에 대 한 앱 등록 세부 사항을 설명 합니다.

[Microsoft id 플랫폼을 사용 하 여 새 응용 프로그램을 등록](quickstart-register-app.md)하는 단계를 수행 하 고 응용 프로그램에 대해 지원 되는 계정을 선택 합니다. SPA 시나리오는 조직의 계정 또는 조직 및 개인 Microsoft 계정에 대 한 인증을 지원할 수 있습니다.

다음으로, 단일 페이지 응용 프로그램에 적용 되는 응용 프로그램 등록의 특정 측면에 대해 알아봅니다.

## <a name="register-a-redirect-uri"></a>리디렉션 URI 등록

암시적 흐름은 웹 브라우저에서 실행 되는 단일 페이지 응용 프로그램에 대 한 리디렉션 토큰을 보냅니다. 따라서 응용 프로그램에서 토큰을 받을 수 있는 리디렉션 URI를 등록 하는 것이 중요 합니다. 리디렉션 URI가 응용 프로그램에 대 한 URI와 정확히 일치 하는지 확인 합니다.

[Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)에서 등록 된 응용 프로그램으로 이동 합니다. 응용 프로그램의 **인증** 페이지에서 **웹** 플랫폼을 선택 합니다. **리디렉션 uri** 필드에 응용 프로그램에 대 한 리디렉션 uri 값을 입력 합니다.

## <a name="enable-the-implicit-flow"></a>암시적 흐름 사용

동일한 **인증** 페이지의 **고급 설정**에서 **암시적 부여**도 사용 하도록 설정 해야 합니다. 응용 프로그램이 사용자 에게만 로그인 하 고 ID 토큰을 가져오는 경우 **id 토큰** 확인란을 선택 하는 것으로 충분 합니다.

응용 프로그램에서 Api를 호출 하기 위한 액세스 토큰을 가져와야 하는 경우 **액세스 토큰** 확인란도 선택 해야 합니다. 자세한 내용은 [ID 토큰](./id-tokens.md) 및 [액세스 토큰](./access-tokens.md)을 참조 하세요.

## <a name="api-permissions"></a>API 사용 권한

단일 페이지 응용 프로그램은 로그인 한 사용자를 대신 하 여 Api를 호출할 수 있습니다. 위임 된 권한을 요청 해야 합니다. 자세한 내용은 [웹 api에 액세스 하기 위한 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-spa-app-configuration.md)

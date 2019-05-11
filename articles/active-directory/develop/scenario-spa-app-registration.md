---
title: 단일 페이지 응용 프로그램 (앱 등록)-Microsoft id 플랫폼
description: 단일 페이지 응용 프로그램 (앱 등록)을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074832"
---
# <a name="single-page-application---app-registration"></a>단일 페이지 응용 프로그램-앱 등록

이 페이지는 단일 페이지 응용 프로그램 (SPA)에 대 한 앱 등록 세부 정보를 설명합니다.

다음 단계에 따라 [Microsoft id 플랫폼을 사용 하 여 새 응용 프로그램을 등록](quickstart-register-app.md), 응용 프로그램에 대 한 지원 되는 계정을 선택 합니다. SPA 시나리오는 개인 Microsoft 계정 또는 조직 및 조직에 계정 사용 하 여 인증을 지원할 수 있습니다.

다음으로, 단일 페이지 응용 프로그램에 적용 되는 응용 프로그램 등록의 특정 측면에 알아봅니다.

## <a name="register-a-redirect-uri"></a>리디렉션 URI를 등록 합니다.

암시적 흐름을 웹 브라우저에서 실행 중인 단일 페이지 응용 프로그램에 토큰 리디렉션을 보냅니다. 따라서 리디렉션 응용 프로그램 토큰을 받을 수 있는 URI를 등록 하는 중요 한 요구 사항을 것입니다. 리디렉션 URI는 응용 프로그램에 대 한 URI를 사용 하 여 정확히 일치를 확인 하세요.

에 [Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908)의 등록 된 응용 프로그램으로 이동할를 **인증** 선택 응용 프로그램 페이지를 **웹** 플랫폼 값을 입력 하 고는 응용 프로그램에 대 한 리디렉션 URI는 **리디렉션 URI** 필드입니다.

## <a name="enable-the-implicit-flow"></a>암시적 흐름을 사용 하도록 설정

동일한 **인증** 페이지의 **고급 설정**을 설정할 수도 있습니다는 **암시적 권한 부여**합니다. 응용 프로그램에서 사용자 및 ID 토큰을 가져오기의 로그인을 수행만 경우 사용 하도록 설정 하는 데 충분 **ID 토큰** 확인란을 선택 합니다.

응용 프로그램에 Api를 호출할 액세스 토큰을 가져오는 데 필요한 경우 사용 하도록 설정 되었는지 확인 합니다 **액세스 토큰을** 도 확인란을 선택 합니다. 자세한 내용은 [ID 토큰](./id-tokens.md) 하 고 [액세스 토큰을](./access-tokens.md)입니다.

## <a name="api-permissions"></a>API 사용 권한

단일 페이지 응용 프로그램에서 로그인 한 사용자를 대신 하 여 Api를 호출할 수 있습니다. 위임 된 권한을 요청 해야 합니다. 세부 정보를 참조 하세요. [웹 Api에 액세스 권한을 추가 합니다.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-spa-app-configuration.md)

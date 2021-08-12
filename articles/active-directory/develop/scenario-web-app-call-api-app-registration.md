---
title: 웹 API를 호출하는 웹앱 등록 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹앱을 등록하는 방법 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bb9a1ca6c2c81e3b0d5dbeff06f4de012446cf79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756320"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>웹 API를 호출하는 웹앱 - 앱 등록

웹 API를 호출하는 웹앱은 사용자를 로그인 시키는 웹앱과 동일하게 등록합니다. 따라서 [사용자를 등록하는 웹앱: 앱 등록](scenario-web-app-sign-user-app-registration.md)의 지침을 따릅니다.

그러나 이제 웹앱은 웹 API도 호출하므로 기밀 클라이언트 응용 프로그램이 됩니다. 따라서 몇 가지 추가 등록이 필요합니다. 앱은 Microsoft ID 플랫폼과 클라이언트 자격 증명 또는 *비밀* 을 공유해야 합니다.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹앱은 로그인한 사용자를 대신하여 API를 호출합니다. 이렇게 하려면 *위임된 권한* 을 요청해야 합니다. 자세히 알아보려면 [웹 API에 액세스할 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서 [코드 구성](scenario-web-app-call-api-app-configuration.md)으로 이동합니다.

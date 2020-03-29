---
title: 웹 API를 호출하는 웹 API 등록 - Microsoft ID 플랫폼 | Azure
description: 다운스트림 웹 API(앱 등록)를 호출하는 웹 API를 빌드하는 방법을 알아봅니다.
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701793"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>웹 API를 호출하는 웹 API: 앱 등록

다운스트림 웹 API를 호출하는 웹 API는 보호된 웹 API와 동일한 등록을 가합니다. 따라서 [보호된 웹 API: 앱 등록의](scenario-protected-web-api-app-registration.md)지침을 따라야 합니다.

이제 웹 앱이 웹 API를 호출하므로 기밀 클라이언트 응용 프로그램이 됩니다. 따라서 앱에서 Microsoft ID 플랫폼과 비밀(클라이언트 자격 증명)을 공유해야 합니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹 앱은 보유자 토큰을 받은 사용자를 대신하여 API를 호출합니다. 웹 앱은 위임된 권한을 요청해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가를](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)참조하십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹 API: 코드 구성](scenario-web-api-call-api-app-configuration.md)

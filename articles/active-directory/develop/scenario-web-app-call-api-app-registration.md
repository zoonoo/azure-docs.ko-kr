---
title: 웹 Api를 호출 하는 웹 앱 등록-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱을 등록 하는 방법 알아보기
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
ms.openlocfilehash: f94a3da96243e30faa90277ce86efec037f54672
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436472"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>웹 Api를 호출 하는 웹 앱: 앱 등록

웹 Api를 호출 하는 웹 앱은 사용자를 로그인 하는 웹 앱과 동일한 등록을 갖습니다. 따라서 [사용자에 게 로그인 하는 웹 앱](scenario-web-app-sign-user-app-registration.md)의 지침에 따라 앱을 등록 합니다.

그러나 웹 앱은 이제 웹 Api를 호출 하므로 기밀 클라이언트 응용 프로그램이 됩니다. 이 때문에 몇 가지 추가 등록이 필요 합니다. 앱은 Microsoft id 플랫폼과 클라이언트 자격 증명 또는 *암호*를 공유 해야 합니다.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹 앱은 로그인 한 사용자를 대신 하 여 Api를 호출 합니다. 이렇게 하려면 *위임 된 권한을*요청 해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹앱: 코드 구성](scenario-web-app-call-api-app-configuration.md)

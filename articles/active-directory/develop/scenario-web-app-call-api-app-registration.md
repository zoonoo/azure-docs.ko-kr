---
title: 웹 API를 호출하는 웹 앱 등록 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 웹 앱을 등록하는 방법 알아보기
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759060"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>웹 API를 호출하는 웹 앱: 앱 등록

웹 API를 호출하는 웹 앱은 사용자에게 서명하는 웹 앱과 동일한 등록을 가수 있습니다. 따라서 [사용자에 서명하는 웹 앱의](scenario-web-app-sign-user-app-registration.md)지침을 따르십시오.

그러나 웹 앱이 이제 웹 API를 호출하기 때문에 기밀 클라이언트 응용 프로그램이 됩니다. 그래서 몇 가지 추가 등록이 필요합니다. 앱은 Microsoft ID 플랫폼과 클라이언트 자격 증명 또는 *비밀을*공유해야 합니다.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹 앱은 로그인한 사용자를 대신하여 API를 호출합니다. 이렇게 하려면 *위임된 권한을*요청해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가를](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)참조하십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹 앱: 코드 구성](scenario-web-app-call-api-app-configuration.md)

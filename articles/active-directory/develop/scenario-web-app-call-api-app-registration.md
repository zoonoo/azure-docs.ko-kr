---
title: 웹 Api를 호출 하는 웹 앱 등록-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱을 등록 하는 방법 알아보기
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 784de823e94aace6f91222c19c1ff8130c3f995f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962936"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>웹 Api를 호출 하는 웹 앱-앱 등록

웹 Api를 호출 하는 웹 앱은 웹 앱 로그인 사용자와 동일한 등록을 갖습니다. 따라서 [사용자가 로그인 하는 웹 앱](scenario-web-app-sign-user-app-registration.md) 의 지침에 따라 앱을 등록 해야 합니다.

그러나 웹 앱은 이제 웹 Api를 호출 하므로 기밀 클라이언트 응용 프로그램이 됩니다. 그 이유는 약간의 추가 등록이 필요 합니다. 암호 (클라이언트 자격 증명)를 Microsoft id 플랫폼과 공유 해야 합니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹 응용 프로그램은 로그인 한 사용자를 대신 하 여 Api를 호출 합니다. 위임 된 권한을 요청 해야 합니다. 자세한 내용은 [웹 api에 액세스 하기 위한 권한 추가를](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-app-call-api-app-configuration.md)

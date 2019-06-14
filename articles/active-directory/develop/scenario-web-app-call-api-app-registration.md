---
title: 웹 앱 호출 웹 Api (응용 프로그램 등록)-되는 Microsoft id 플랫폼
description: 웹 앱을 빌드하는 방법을 알아봅니다 호출 웹 Api (응용 프로그램 등록) 되는
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075192"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>웹 앱 등록 Api를 호출 되는 웹 앱

웹 앱 호출 웹 Api가 사용자 웹 앱에 로그인으로 동일한 등록 되었습니다. 지침에 따라 해야 하므로 [웹 앱 로그인 사용자-앱 등록](scenario-web-app-sign-user-app-registration.md)

그러나 이후 웹 앱 web Api를 호출 하는 이제 되기 기밀 클라이언트 응용 프로그램입니다. 이유는 약간의 추가 등록 필요: Microsoft id 플랫폼을 사용 하 여 비밀 (클라이언트 자격 증명)을 공유 해야 합니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹 응용 프로그램에서 로그인 한 사용자를 대신 하 여 Api를 호출합니다. 위임 된 권한을 요청 해야 합니다. 자세한 내용은 참조 하십시오 [웹 Api에 액세스 권한을 추가 합니다.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-app-call-api-app-configuration.md)

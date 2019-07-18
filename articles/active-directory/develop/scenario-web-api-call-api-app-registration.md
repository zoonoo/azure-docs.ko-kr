---
title: 웹 API는 호출 다운스트림 웹 Api (응용 프로그램 등록)-Microsoft id 플랫폼
description: Web API는 호출 다운스트림 웹 Api (응용 프로그램 등록)를 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075387"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Web API 웹 앱 등록 Api를 호출 되는

다운스트림 웹 Api를 호출 하는 web API가 보호 된 web API와 같은 등록 되었습니다. 지침에 따라 해야 하므로 [Web API 보호-앱 등록](scenario-protected-web-api-app-registration.md)합니다.

그러나 이후 웹 앱 web Api를 호출 하는 이제 되기 기밀 클라이언트 응용 프로그램입니다. 때문에 필요한 추가 등록 정보는: 앱에서 Microsoft id 플랫폼을 사용 하 여 비밀 (클라이언트 자격 증명)을 공유 해야 합니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹 응용 프로그램에 대 한 전달자 토큰을 받은 사용자를 대신 하 여 Api를 호출 합니다. 위임 된 권한을 요청 해야 합니다. 자세한 내용은 참조 하세요 [웹 Api에 액세스 권한을 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-api-call-api-app-configuration.md)

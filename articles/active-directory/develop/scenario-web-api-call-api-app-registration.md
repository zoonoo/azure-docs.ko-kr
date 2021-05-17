---
title: 웹 API를 호출하는 웹 API 등록 | Azure
titleSuffix: Microsoft identity platform
description: 다운스트림 웹 API(앱 등록)를 호출하는 웹 API를 빌드하는 방법을 알아봅니다.
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
ms.openlocfilehash: af1047c5f890b1b88ae6d043a30704e84b8dc079
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584317"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>웹 API를 호출하는 웹 API: 웹 등록

다운스트림 웹 API를 호출하는 웹 API는 보호된 웹 API와 동일한 등록을 포함합니다. [보호된 웹 API: 앱 등록](scenario-protected-web-api-app-registration.md)의 지침을 따릅니다.

이제 웹앱이 웹 API를 호출하므로 기밀 클라이언트 애플리케이션이 됩니다. 이 때문에 추가 등록 정보가 필요합니다. 앱은 Microsoft ID 플랫폼과 비밀(클라이언트 자격 증명)을 공유해야 합니다.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 사용 권한

웹앱은 전달자 토큰을 받은 사용자를 대신하여 API를 호출합니다. 웹앱은 위임된 권한을 요청해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 코드 구성](scenario-web-api-call-api-app-configuration.md)으로 이동합니다.

---
title: 디먼 앱 웹 Api 호출 (웹 Api 호출)-Microsoft id 플랫폼
description: 디먼 앱을 작성 하는 방법은 웹 Api (웹 Api 호출)를 호출 되는
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076272"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>디먼 앱 웹 Api를 호출 되는 앱에서 web API 호출

디먼 앱을.NET 디먼 응용 프로그램에서 web API를 호출 하거나 몇 가지 사전 승인 된 web Api를 호출할 수 있습니다.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>.NET 디먼 응용 프로그램에서 web API 호출

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>여러 Api를 호출합니다.

디먼 앱, 웹 Api 호출 하는 사전 승인이 필요 합니다. 디먼 앱 (사용자 개입 없이)를 사용 하 여 모든 증분 동의 하지 않습니다. 테 넌 트 관리자는 응용 프로그램 및 모든 API 사용 권한을 사전 동의 해야 합니다. 각 리소스에 대 한 토큰을 획득 해야 여러 Api를 호출 하려는 경우 각 시간 호출 `AcquireTokenForClient`합니다. MSAL은 불필요 한 서비스 호출을 방지 하려면 해당 응용 프로그램 토큰 캐시를 사용 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [디먼 앱-프로덕션으로 이동](./scenario-daemon-production.md)
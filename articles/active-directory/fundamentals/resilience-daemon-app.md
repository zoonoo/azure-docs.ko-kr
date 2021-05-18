---
title: 개발하는 디먼 애플리케이션에서 인증 및 권한 부여의 복원력 향상
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼을 사용하여 디먼 애플리케이션에서 인증 및 권한 부여의 복원력을 높이기 위한 지침
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724844"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>개발하는 디먼 애플리케이션에서 인증 및 권한 부여의 복원력 향상

이 문서에서는 개발자가 Microsoft ID 플랫폼 및 Azure Active Directory를 사용하여 디먼 애플리케이션의 복원력을 높일 수 있는 방법에 대한 지침을 제공합니다. 여기에는 백그라운드 프로세스, 서비스, 서버 간 앱 및 사용자가 없는 애플리케이션이 포함됩니다.

![Microsoft ID를 호출하는 디먼 애플리케이션](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID 사용

Microsoft Azure에서 디먼 앱을 빌드하는 개발자는 [Azure 리소스의 관리 ID](../managed-identities-azure-resources/overview.md)를 사용할 수 있습니다. 관리 ID를 사용하면 개발자는 암호 및 자격 증명을 관리할 필요가 없습니다. 이 기능은 인증서 만료, 회전 오류 또는 신뢰와 관련된 실수를 방지하여 복원력을 향상시킵니다. 또한 복원력을 특히 높이기 위한 몇 가지 기본 제공 기능도 있습니다.

관리 ID는 오래 지속되는 액세스 토큰 및 Microsoft ID 정보를 사용하여 기존 토큰이 만료되기 전에 긴 기간 내에 새 토큰을 사전에 획득합니다. 새 토큰을 획득하려고 시도하는 동안 앱을 계속 실행할 수 있습니다.

또한 관리 ID는 지역 엔드포인트를 통해 지역 외부의 장애에 대비하여 성능 및 복원력을 향상시킵니다. 지역 엔드포인트를 사용하면 모든 트래픽을 지리적 영역 내에 유지할 수 있습니다. 예를 들어 Azure 리소스가 WestUS2에 있는 경우 Microsoft ID가 생성한 트래픽을 비롯한 모든 트래픽이 WestUS2에 유지되어야 합니다. 이렇게 하면 서비스의 종속성을 통합하여 가능한 오류 지점이 제거됩니다.

## <a name="use-the-microsoft-authentication-library"></a>Microsoft 인증 라이브러리 사용

관리 ID를 사용하지 않는 디먼 앱 개발자는 [MSAL(Microsoft 인증 라이브러리)](../develop/msal-overview.md)을 사용하여 인증 및 권한 부여를 간단하게 구현하고 복원력을 위한 모범 사례를 자동으로 사용할 수 있습니다. MSAL은 필요한 클라이언트 자격 증명을 보다 쉽게 제공하는 프로세스를 수행합니다. 예를 들어 인증서 기반 자격 증명을 사용하는 경우 애플리케이션에서 JSON Web Token 어설션을 만들고 서명하는 것을 구현할 필요가 없습니다.

### <a name="use-microsoftidentityweb-for-net-developers"></a>.NET 개발자를 위한 Microsoft.Identity.Web을 사용합니다.

ASP.NET Core에서 디먼 앱을 빌드하는 개발자는 [Microsoft.Identity.Web](../develop/microsoft-identity-web.md) 라이브러리를 사용할 수 있습니다. 이 라이브러리는 MSAL을 기반으로 하여 ASP.NET Core 앱에 대한 권한 부여를 훨씬 쉽게 구현할 수 있도록 합니다. 여러 지역에서 실행될 수 있는 분산된 앱에 대한 여러 [분산 토큰 캐시](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) 전략이 포함되어 있습니다.

## <a name="cache-and-store-tokens"></a>캐시 및 스토리지 토큰

MSAL을 사용하여 인증 및 권한 부여를 구현하지 않는 경우에는 토큰을 캐싱하고 저장하기 위한 몇 가지 모범 사례를 구현할 수 있습니다. MSAL은 이러한 모범 사례를 자동으로 구현하고 따릅니다.

애플리케이션은 ID 공급자로부터 토큰을 획득하여 애플리케이션이 보호된 API를 호출할 수 있도록 권한을 부여합니다. 앱에서 토큰을 수신할 때 토큰을 포함하는 응답에는 토큰을 캐싱하고 다시 사용하는 데 걸리는 시간을 애플리케이션에 알리는 "expires\_in" 속성도 포함됩니다. 애플리케이션은 "expires\_in" 속성을 사용하여 토큰의 수명을 확인해야 합니다. 애플리케이션은 API 액세스 토큰을 디코딩하려고 해서는 안 됩니다. 캐싱된 토큰을 사용하면 앱과 Microsoft ID 사이에 불필요한 트래픽이 방지됩니다. 사용자는 해당 토큰 수명의 길이만큼 애플리케이션에 로그인 상태를 유지할 수 있습니다.

## <a name="properly-handle-service-responses"></a>서비스 응답을 올바르게 처리합니다.

마지막으로, 애플리케이션에서 모든 오류 응답을 처리하는 동안 복원력에 영향을 줄 수 있는 몇 가지 응답이 있습니다. 애플리케이션이 HTTP 429 응답 코드(너무 많은 요청)를 수신하는 경우 Microsoft ID에서 요청을 제한합니다. 앱은 계속해서 너무 많은 요청을 수신하는 경우 앱이 토큰을 수신하지 못하도록 계속 제한됩니다. 애플리케이션은 "다음 시간 후 다시 시도" 응답 필드의 시간(초)이 경과할 때까지 토큰을 다시 획득하려고 시도하면 안 됩니다. 429 응답을 수신하는 것은 애플리케이션에서 토큰을 올바르게 캐싱하지 않고 다시 사용하고 있기 때문인 경우가 많습니다. 개발자는 애플리케이션에서 토큰을 캐싱하고 다시 사용하는 방법을 검토해야 합니다.

애플리케이션에서 HTTP 5xx 응답 코드를 수신하는 경우 앱은 빠른 다시 시도 루프를 시작하면 안 됩니다. 해당하는 경우 애플리케이션은 429 응답에 대해 수행하는 것과 동일한 "다음 시간 후 다시 시도" 처리를 적용해야 합니다. 응답에서 "다음 시간 후 다시 시도" 헤더를 제공하지 않는 경우에는 응답 후 5초 이상이 지나고 처음 다시 시도하여 지수 백오프 재시도를 구현하는 것이 좋습니다.

요청 시간을 초과한 애플리케이션은 즉시 다시 시도하면 안 됩니다. 응답 후 5초 이상이 지났을 때 다시 시도하여 지수 백오프 재시도를 구현합니다.

## <a name="next-steps"></a>다음 단계

- [사용자를 로그인하는 애플리케이션에 복원력 빌드](resilience-client-app.md)
- [ID 및 액세스 관리 인프라에서 복원력 빌드](resilience-in-infrastructure.md)
- [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)
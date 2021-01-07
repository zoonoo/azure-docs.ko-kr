---
title: 개발 하는 디먼 응용 프로그램에서 인증 및 권한 부여의 복원 력 향상
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼을 사용 하 여 디먼 응용 프로그램에서 인증 및 권한 부여의 복원 력을 높이기 위한 지침
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 74bfc9eeeb8375fca2c88a3fd3c31f17e130fc99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919992"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>개발 하는 디먼 응용 프로그램에서 인증 및 권한 부여의 복원 력 향상

이 문서에서는 개발자가 Microsoft id 플랫폼 및 Azure Active Directory를 사용 하 여 디먼 응용 프로그램의 복원 력을 높일 수 있는 방법에 대 한 지침을 제공 합니다. 여기에는 백그라운드 프로세스, 서비스, 서버-서버 앱 및 사용자가 없는 응용 프로그램이 포함 됩니다.

![Microsoft id를 호출 하는 디먼 응용 프로그램](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 관리 되는 Id 사용

Microsoft Azure에서 디먼 앱을 빌드하는 개발자는 [Azure 리소스에 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 사용할 수 있습니다. 관리 되는 Id를 통해 개발자는 암호 및 자격 증명을 관리할 필요가 없습니다. 이 기능은 인증서 만료, 회전 오류 또는 트러스트와 관련 된 실수를 방지 하 여 복원 력을 향상 시킵니다. 또한 복원 력을 높이기 위한 몇 가지 기본 제공 기능도 있습니다.

관리 되는 Id는 오래 지속 되는 액세스 토큰 및 Microsoft Id의 정보를 사용 하 여 기존 토큰이 만료 되기 전에 긴 기간 내에 새 토큰을 사전에 획득 합니다. 새 토큰을 획득 하려고 시도 하는 동안 앱을 계속 실행할 수 있습니다.

또한 관리 되는 Id는 지역 끝점을 사용 하 여 지역 장애에 대비 하 여 성능 및 복원 력을 향상 시킵니다. 지역 끝점을 사용 하면 모든 트래픽을 지리적 영역 내에 유지할 수 있습니다. 예를 들어 Azure 리소스가 WestUS2에 있는 경우 Microsoft Identity generated traffic를 비롯 한 모든 트래픽이 WestUS2에 유지 되어야 합니다. 이렇게 하면 서비스의 종속성을 통합 하 여 가능한 오류 지점이 제거 됩니다.

## <a name="use-the-microsoft-authentication-library"></a>Microsoft 인증 라이브러리 사용

관리 되는 Id를 사용 하지 않는 디먼 앱 개발자는 [MSAL (Microsoft 인증 라이브러리)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)을 사용 하 여 인증 및 권한 부여를 간단 하 게 구현 하 고 복원 력을 위한 모범 사례를 자동으로 사용할 수 있습니다. MSAL은 필요한 클라이언트 자격 증명을 보다 쉽게 제공 하는 프로세스를 수행 합니다. 예를 들어 인증서 기반 자격 증명을 사용 하는 경우 응용 프로그램에서 JSON Web Token 어설션을 만들고 서명 하는 것을 구현할 필요가 없습니다.

### <a name="use-microsoftidentityweb-for-net-developers"></a>.NET 개발자를 위한 Microsoft. Identity를 사용 합니다.

ASP.NET Core에서 디먼 앱을 빌드하는 개발자는 [Microsoft. Identity. 웹](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web) 라이브러리를 사용할 수 있습니다. 이 라이브러리는 MSAL을 기반으로 하 여 ASP.NET Core 앱에 대 한 권한 부여를 훨씬 쉽게 구현할 수 있도록 합니다. 여러 지역에서 실행 될 수 있는 분산 된 앱에 대 한 여러 [분산 토큰 캐시](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) 전략이 포함 되어 있습니다.

## <a name="cache-and-store-tokens"></a>캐시 및 저장소 토큰

MSAL을 사용 하 여 인증 및 권한 부여를 구현 하지 않는 경우에는 토큰을 캐시 하 고 저장 하기 위한 몇 가지 모범 사례를 구현할 수 있습니다. MSAL은 이러한 모범 사례를 자동으로 구현 하 고 따릅니다.

응용 프로그램은 Id 공급자 로부터 토큰을 가져와 응용 프로그램이 보호 된 Api를 호출할 수 있도록 권한을 부여 합니다. 앱에서 토큰을 수신 하는 경우 토큰을 포함 하는 응답에는 \_ 토큰을 캐시 하 고 다시 사용 하는 데 걸리는 시간을 응용 프로그램에 알리는 "만료" 속성이 포함 됩니다. 응용 프로그램은 "만료" 속성을 사용 \_ 하 여 토큰의 수명을 확인 하는 것이 중요 합니다. 응용 프로그램은 API 액세스 토큰을 디코딩하 려 면 안 됩니다. 캐시 된 토큰을 사용 하면 앱과 Microsoft Id 사이에 불필요 한 트래픽이 방지 됩니다. 사용자는 해당 토큰 수명의 길이에 맞게 응용 프로그램에 로그인 상태를 유지할 수 있습니다.

## <a name="properly-handle-service-responses"></a>서비스 응답을 올바르게 처리 합니다.

마지막으로, 응용 프로그램에서 모든 오류 응답을 처리 하는 동안 복원 력에 영향을 줄 수 있는 몇 가지 응답이 있습니다. 응용 프로그램이 HTTP 429 응답 코드, 너무 많은 요청을 수신 하는 경우 Microsoft Id에서 요청을 제한 합니다. 앱이 계속 해 서 너무 많은 요청을 하는 경우 앱이 토큰을 수신 하지 못하도록 계속 제한 됩니다. 응용 프로그램은 "다시 시도" 응답 필드가 경과할 때까지 (초) 후에 토큰을 다시 얻으려고 시도 하지 않아야 합니다. 429 응답을 수신 하는 것은 응용 프로그램에서 토큰을 올바르게 캐시 하지 않고 다시 사용 하 고 있음을 나타내는 경우가 많습니다. 개발자는 응용 프로그램에서 토큰을 캐시 하 고 다시 사용 하는 방법을 검토 해야 합니다.

응용 프로그램에서 HTTP 5xx 응답 코드를 수신 하는 경우 앱은 빠른 다시 시도 루프를 시작 하면 안 됩니다. 응용 프로그램은 있는 경우 429 응답에 대해 수행 되는 것과 동일한 "다시 시도" 처리를 인식 해야 합니다. 응답에서 "다시 시도" 헤더를 제공 하지 않는 경우 응답 후 최소 5 초 이상 다시 시도 하 여 지 수 백오프 재시도를 구현 하는 것이 좋습니다.

요청 시간이 초과 되는 응용 프로그램은 즉시 다시 시도 하면 안 됩니다. 응답 후 5 초 이상 다시 시도 하 여 지 수 백오프 다시 시도를 구현 합니다.

## <a name="next-steps"></a>다음 단계

- [사용자가 로그인 하는 응용 프로그램에 복원 력 빌드](resilience-client-app.md)
- [Id 및 액세스 관리 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)

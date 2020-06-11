---
title: Azure Event Grid를 사용한 Auth0 파트너 토픽
description: Azure Event Grid를 사용하여 Auth0에서 Azure 서비스로 이벤트를 보냅니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: e193b272460fe97f599adff414fcac6e80648104
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738373"
---
# <a name="auth0-partner-topics"></a>Auth0 파트너 토픽
![Auth0 로고](./media/auth0-overview/auth0-logo.png)

애플리케이션 빌더의 ID 플랫폼인 Auth0은 개발자 및 엔터프라이즈에 애플리케이션을 보호하는 데 필요한 구성 요소를 제공합니다.

Auth0 파트너 토픽을 통해 Auth0 시스템에서 내보낸 이벤트를 사용하여 많은 작업을 수행할 수 있습니다. 인증 후에 의미 있는 방식으로 사용자와 관계를 유지하거나 보안 및 인프라 작업을 자동화합니다.

통합을 통해 신뢰성이 높은 Auth0 로그 이벤트를 Azure로 스트리밍할 수 있습니다. 여기에서 즐겨 사용하는 Azure 리소스와 함께 이벤트를 사용할 수 있습니다. 이러한 통합을 통해 이벤트에 대응하고, 인사이트를 얻고, 보안 문제를 모니터링하고, 다른 강력한 데이터 파이프라인과 상호 작용할 수 있습니다.

Auth0 및 Azure를 사용하는 조직의 경우 이 통합을 통해 전체 스택에서 데이터를 원활하게 통합할 수 있습니다. 
 
## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형
사용 가능한 Auth0 이벤트 유형 및 설명의 전체 목록이 [여기](https://auth0.com/docs/logs/references/log-event-type-codes)에 나와 있습니다.

## <a name="use-cases"></a>사용 사례

### <a name="engage-with-your-users"></a>사용자와의 관계 유지
이탈을 줄이고 사용자를 유지하려면 강력한 사용자 환경을 제공하는 것이 중요합니다. Azure Functions 및 Logic Apps와 함께 Auth0 이벤트를 사용하여 더 많은 사용자 지정 애플리케이션 환경을 제공합니다. 

### <a name="understand-user-behavior"></a>사용자 동작 이해
사용자가 제품에 액세스하는 시간, 로그인한 위치 및 사용하는 디바이스를 파악합니다. 이러한 신호를 추적하여 가장 중요한 제품 영역에 대한 이해를 발전시킵니다. 이러한 신호를 통해 지원할 브라우저 및 디바이스, 앱을 현지화할 언어 및 최대 트래픽 발생 시간을 확인할 수 있습니다. 

### <a name="manage-user-data"></a>사용자 데이터 관리
사용자 동작을 유지하고 감사하는 것은 보안을 유지 관리하고 업계 규정을 준수하는 데 매우 중요합니다. 사용자 데이터를 편집, 제거 또는 내보내는 기능은 유럽 연합의 GDPR(일반 데이터 보호 규정)과 같은 개인 정보 보호법을 준수하기 위해 점점 더 중요해지고 있습니다.

### <a name="secure-your-application"></a>애플리케이션 보안
분산 시스템을 보호할 때에는 보안 모니터링 및 인시던트 대응 절차를 조합해야 합니다. 따라서 모든 데이터를 한 곳에 유지하고 전체 스택을 모니터링해야 합니다. 

## <a name="next-steps"></a>다음 단계

- [파트너 토픽 개요](partner-topics-overview.md)
- [Auth0 파트너 토픽을 사용하는 방법](auth0-how-to.md)
- [Auth0 설명서](https://auth0.com/docs/azure-tutorial)
- [Event Grid 파트너 되기](partner-onboarding-overview.md)


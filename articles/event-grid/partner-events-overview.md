---
title: Azure Event Grid - 파트너 이벤트
description: Azure Event Grid를 사용하여 타사 Event Grid SaaS 및 PaaS 파트너의 이벤트를 Azure 서비스로 직접 보냅니다.
ms.topic: conceptual
ms.date: 06/15/2021
ms.openlocfilehash: 5a215d8d007f411066d25d8751299ae6a73038dc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538134"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Azure Event Grid의 파트너 이벤트(미리 보기)
**파트너 이벤트** 기능을 이용하면 타사 SaaS 공급자가 해당 서비스에서 이벤트를 게시하여 소비자가 해당 이벤트를 구독할 수 있습니다. 이 기능은 [항목](concepts.md#topics) 유형인 **파트너 토픽** 을 노출하여 타사 이벤트 원본에 자사 환경을 제공 합니다. 구독자는 이 항목에 대한 구독을 만들어 이벤트를 이용합니다. 또한 이벤트 게시자와 구독자가 사용하는 리소스의 소유권 및 관심 영역을 구분하여 정리된 게시-구독 모델을 제공합니다.

> [!NOTE]
> Event Grid 사용이 처음인 경우 [개요](overview.md), [개념](concepts.md), 그리고 [이벤트 처리기](event-handlers.md)를 참조하세요.

## <a name="what-is-partner-events-to-a-publisher"></a>게시자에게 파트너 이벤트란?
이벤트 게시자의 경우 파트너 이벤트 기능을 통해 게시자는 다음 작업을 수행할 수 있습니다.

- Event Grid에 이벤트 원본 온보딩
- 이벤트를 게시할 수 있는 네임스페이스(엔드포인트) 만들기
- 구독자들이 소유하고 이벤트를 소비하기 위해 이용하는 파트너 토픽을 Azure에 만들기

## <a name="what-is-partner-events-to-a-subscriber"></a>구독자에게 파트너 이벤트란?
구독자는 파트너 이벤트 기능을 통해 Azure에 파트너 토픽을 만들어 타사 이벤트 원본의 이벤트를 이용할 수 있습니다. 이벤트 사용은 구독자의 이벤트 처리기에 이벤트를 보내는(푸시) 이벤트 구독을 만들어 실현됩니다.

## <a name="why-should-i-use-partner-events"></a>왜 파트너 이벤트를 사용해야 하나요?
다음 요구 사항 중 하나 이상에 해당되는 경우 파트너 이벤트를 사용하는 것이 좋습니다.

### <a name="for-publishers"></a>게시자

- Azure에서 이벤트를 사용할 수 있도록 하는 메커니즘을 원합니다. 사용자는 자신이 소유하고 관리하는 파트너 토픽 및 이벤트 구독을 사용하여 이러한 이벤트를 필터링하고 라우팅할 수 있습니다. [토픽](custom-topics.md) 및 [도메인](event-domains.md)과 같은 기타 통합 방법을 사용할 수 있습니다. 그러나 리소스(파트너 토픽) 소유권, 관리 및 게시자와 구독자 간의 청구를 명확하게 구분하는 것은 허용되지 않습니다. 또한 이 방법은 파트너 토픽을 쉽게 검색하고 사용할 수 있는 보다 직관적인 사용자 환경을 제공합니다.
- 네임스페이스의 엔드포인트인 단일 엔드포인트에 이벤트를 게시하려고 합니다. 또한 해당 이벤트의 하위 집합만 사용할 수 있도록 해당 이벤트를 필터링하는 기능을 원합니다. 
- 게시된 이벤트와 관련된 메트릭을 표시하려고 합니다.
- 이벤트에 [클라우드 이벤트 1.0](https://cloudevents.io/) 스키마를 사용하려고 합니다.

### <a name="for-subscribers"></a>구독자

- [타사 게시자](#available-third-party-event-publishers)의 이벤트를 구독하고 Azure 또는 다른 곳에 있는 이벤트 처리기를 사용하여 이벤트를 처리하려고 합니다.
- 다양한 라우팅 기능 집합과 [대상/이벤트 처리기](overview.md#event-handlers) 를 이용하여 타사 원본의 이벤트를 처리하려고 합니다. 
- 구독자/이벤트 처리기가 사용된 메시지 브로커의 존재를 인식하지 못하는 느슨하게 결합된 아키텍처를 구현하려고 합니다. 
- 송신-재시도 지원 및 한 번 이상의 의미 체계를 갖춘 복원력 있는 푸시 배달 메커니즘이 필요합니다.
- 이벤트에 [클라우드 이벤트 1.0](https://cloudevents.io/) 스키마를 사용하려고 합니다. 


## <a name="available-third-party-event-publishers"></a>사용 가능한 타사 이벤트 게시자
구독자가 이벤트 소비를 시작하려면 먼저 타사 이벤트 게시자가 [온보딩 프로세스](partner-onboarding-overview.md)를 거쳐야 합니다. 


### <a name="auth0"></a>Auth0
**Auth0** 는 사용 가능한 첫 번째 파트너 게시자입니다. [Auth0 파트너 토픽](auth0-overview.md)을 만들어 Auth0와 Azure 계정을 연결할 수 있습니다. 이 통합으로 Auth0 이벤트를 실시간으로 반응, 기록 및 모니터링할 수 있습니다. 사용해 보려면 [Auto0과 Azure Event Grid 통합](auth0-how-to.md)을 참조하세요

 
## <a name="resources-managed-by-event-publishers"></a>이벤트 게시자가 관리하는 리소스
이벤트 게시자는 다음 리소스를 만들고 관리합니다.

### <a name="partner-registration"></a>파트너 등록
등록에는 게시자와 관련된 일반 정보가 있습니다. 사용자가 파트너 토픽을 만들려고 할 때 옵션으로 Azure Portal에 표시되는 파트너 토픽의 유형을 정의합니다. 게시자는 구독자의 요구에 맞게 하나 이상의 파트너 토픽 유형을 공개할 수 있습니다. 즉, 게시자가 여러 서비스의 이벤트에 대해 별도의 등록(파트너 토픽 유형)을 만들 수 있습니다. 예를 들어 HR(인적 자원) 서비스의 경우 게시자는 직원 입사, 직원 승진 및 직원의 퇴사와 같은 이벤트에 대한 파트너 항목을 정의할 수 있습니다. 

다음 사항을 주의하세요.

- Azure 승인 파트너 등록만 표시됩니다. 
- 등록은 전역적입니다. 즉, 특정 Azure 지역에 연결되지 않습니다.
- 등록은 선택적 리소스입니다. 하지만 게시자로 등록을 만드는 것이 좋습니다. 이를 통해 사용자는 **파트너 토픽 만들기** 페이지, [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic)에 있는 페이지에서 토픽을 검색할 수 있습니다. 그런 다음 이벤트 구독을 만드는 동안 사용자가 이벤트 유형(예: 직원 입사, 직원 퇴사 등)을 선택할 수 있습니다.

### <a name="namespace"></a>네임스페이스
[사용자 지정 토픽](custom-topics.md) 및 [도메인](event-domains.md)과 마찬가지로 파트너 네임스페이스는 이벤트를 게시 하는 지역 엔드포인트입니다. 게시자가 이벤트 채널을 만들고 관리하는 네임스페이스를 통하는 것입니다. 네임스페이스는 이벤트 채널의 컨테이너 리소스로도 작동 합니다.

### <a name="event-channels"></a>이벤트 채널
이벤트 채널은 파트너 토픽에 대해 미러된 리소스입니다. 게시자가 게시자의 Azure 구독에서 이벤트 채널을 만들면 구독자의 Azure 구독에도 파트너 토픽이 생성됩니다. 이벤트 채널(GET 제외)에 대해 수행되는 작업은 해당 구독자 파트너 토픽에도 적용됩니다(삭제에도 적용). 그러나 파트너 토픽만이 구독과 이벤트 배달을 구성할 수 있는 리소스 유형입니다.

## <a name="resources-managed-by-subscribers"></a>구독자가 관리하는 리소스 
구독자는 게시자가 정의한 파트너 토픽을 사용할 수 있으며 이 항목은 보고 관리하는 유일한 리소스 유형입니다. 파트너 토픽이 만들어지면 구독자 사용자는 [대상/이벤트 처리기](overview.md#event-handlers)에 대해 필터 규칙을 정의하는 이벤트 구독을 만들 수 있습니다. 구독자에게, 파트너 토픽 및 관련 이벤트 구독은 [사용자 지정 토픽](custom-topics.md)과 동일한 풍부한 기능을 제공하고, 관련 구독은 한 가지 특이한 차이점이 있습니다. 파트너 토픽은 지원되는 기타 스키마보다 다양한 기능 집합을 제공하는 [클라우드 이벤트 1.0 스키마](cloudevents-schema.md)만 지원합니다.

다음 이미지는 컨트롤 플레인 작업의 흐름을 보여줍니다.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="파트너 이벤트 - 컨트롤 플레인 흐름":::

1. 게시자는 **파트너 등록** 을 만듭니다. 파트너 등록은 전역적입니다. 즉, 특정 Azure 지역과 연결되지 않습니다. 이 단계는 선택 사항입니다.
1. 게시자는 특정 지역에 **파트너 네임스페이스** 를 만듭니다.
1. 구독자 1이 파트너 토픽을 만들려고 하면 **이벤트 채널** 인 이벤트 채널 1이 게시자의 Azure 구독에서 먼저 만들어집니다.
1. 그런 다음 **파트너 토픽** 인 파트너 토픽 1이 구독자의 Azure 구독에 만들어집니다. 구독자는 파트너 토픽을 활성화해야 합니다. 
1. 구독자 1은 파트너 토픽 1에 **Azure Logic Apps 구독** 을 만듭니다.
1. 구독자 1은 파트너 토픽 1에 **Azure Blob Storage 구독** 을 만듭니다. 
1. 구독자 2가 파트너 토픽을 만들려고 하면 게시자의 Azure 구독에서 먼저 다른 **이벤트 채널** 인 이벤트 채널 2가 만들어집니다. 
1. 그런 다음 **파트너 토픽** 인 파트너 토픽 2가 두 번째 구독자의 Azure 구독에서 만들어집니다. 구독자는 파트너 토픽을 활성화해야 합니다. 
1. 구독자 2는 파트너 토픽 2에 **Azure Functions 구독** 을 만듭니다. 

## <a name="pricing"></a>가격 책정
파트너 토픽은 Event Grid를 사용할 때 수행되는 작업 수에 따라 요금이 청구됩니다. 청구 및 가격 세부 정보에 대한 기준으로 사용되는 모든 유형의 작업에 대한 자세한 정보는 [Event Grid 가격 책정](https://azure.microsoft.com/pricing/details/event-grid/)을 참조하세요.

## <a name="limits"></a>제한
파트너 토픽에 대한 제한 사항에 대한 세부 정보는 [Event Grid 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Auth0 파트너 토픽](auth0-overview.md)
- [Auth0 파트너 토픽을 사용하는 방법](auth0-how-to.md)
- [Event Grid 파트너 되기](partner-onboarding-overview.md)
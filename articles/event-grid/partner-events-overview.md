---
title: Azure Event Grid-파트너 이벤트
description: Azure Event Grid를 사용하여 타사 Event Grid SaaS 및 PaaS 파트너의 이벤트를 Azure 서비스로 직접 보냅니다.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506149"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Azure Event Grid의 파트너 이벤트 (미리 보기)
**파트너 이벤트** 기능을 사용 하면 타사 SaaS 공급자가 해당 서비스에서 이벤트를 게시 하 여 소비자가 해당 이벤트를 구독할 수 있습니다. 이 기능은 [항목](concepts.md#topics) 유형인 **파트너 토픽** 을 노출 하 여 타사 이벤트 원본에 대 한 자사 환경을 제공 합니다. 구독자는이 항목에 대 한 구독을 만들어 이벤트를 사용 합니다. 또한 이벤트 게시자 및 구독자에서 사용 하는 리소스의 관심사 및 소유권을 구분 하 여 정리 된 pub sub 모델을 제공 합니다.

> [!NOTE]
> Event Grid 사용을 처음 접하는 경우 [개요](overview.md), [개념](concepts.md)및 [이벤트 처리기](event-handlers.md)를 참조 하세요.

## <a name="what-is-partner-events-to-a-publisher"></a>게시자에 대 한 파트너 이벤트 란?
이벤트 게시자의 경우 파트너 이벤트 기능을 통해 게시자는 다음 작업을 수행할 수 있습니다.

- Event Grid에 이벤트 원본 등록
- 이벤트를 게시할 수 있는 네임 스페이스 (끝점) 만들기
- Azure에서 구독자가 이벤트를 사용 하 고 사용 하는 파트너 항목 만들기

## <a name="what-is-partner-events-to-a-subscriber"></a>구독자에 대 한 파트너 이벤트 란?
구독자는 파트너 이벤트 기능을 통해 Azure에서 파트너 항목을 만들어 타사 이벤트 원본의 이벤트를 사용할 수 있습니다. 이벤트 소비는 구독자의 이벤트 처리기에 이벤트를 보내는 (푸시) 이벤트 구독을 만들어 실현 됩니다.

## <a name="why-should-i-use-partner-events"></a>파트너 이벤트를 사용 해야 하는 이유는 무엇 인가요?
다음 요구 사항 중 하나 이상이 있는 경우 파트너 이벤트를 사용 하는 것이 좋습니다.

### <a name="for-publishers"></a>게시자

- Azure에서 이벤트를 사용할 수 있도록 하는 메커니즘을 원합니다. 사용자는 자신이 소유 하 고 관리 하는 파트너 토픽 및 이벤트 구독을 사용 하 여 이러한 이벤트를 필터링 하 고 라우팅할 수 있습니다. [토픽](custom-topics.md) 및 [도메인과](event-domains.md)같은 다른 통합 방법을 사용할 수 있습니다. 그러나 리소스 (파트너 토픽) 소유권, 관리 및 게시자와 구독자 간의 청구를 명확 하 게 구분 하는 것은 허용 되지 않습니다. 또한이 방법은 더 직관적인 사용자 환경을 제공 하 여 파트너 토픽을 쉽게 검색 하 고 사용할 수 있게 해줍니다.
- 단일 끝점, 즉 네임 스페이스의 끝점에 이벤트를 게시 하려고 합니다. 그리고 이러한 이벤트의 하위 집합만 사용할 수 있도록 해당 이벤트를 필터링 하는 기능이 필요 합니다. 
- 게시 된 이벤트와 관련 된 메트릭을 표시 하려고 합니다.
- 이벤트에 대 한 [Cloud Events 1.0](https://cloudevents.io/) 스키마를 사용 하려고 합니다.

### <a name="for-subscribers"></a>구독자의 경우

- [타사 게시자](#available-third-party-event-publishers) 의 이벤트를 구독 하 고 Azure 또는 다른 위치에 있는 이벤트 처리기를 사용 하 여 이벤트를 처리 하려고 합니다.
- 다양 한 라우팅 기능 집합을 활용 하 고 [대상/이벤트 처리기](overview.md#event-handlers) 를 활용 하 여 타사 소스의 이벤트를 처리 하려고 합니다. 
- 구독자/이벤트 처리기에서 사용 된 메시지 브로커가 있음을 인식 하지 못하는 느슨하게 결합 된 아키텍처를 구현 하려고 합니다. 
- 전송-재시도 지원과 최소 한 번의 의미 체계가 있는 복원 력 푸시 제공 메커니즘이 필요 합니다.
- 이벤트에 대 한 [Cloud Events 1.0](https://cloudevents.io/) 스키마를 사용 하려고 합니다. 


## <a name="available-third-party-event-publishers"></a>사용 가능한 타사 이벤트 게시자
구독자가 이벤트 사용을 시작 하려면 타사 이벤트 게시자가 [온 보 딩 프로세스](partner-onboarding-overview.md) 를 진행 해야 합니다. 

구독자이 고 타사 서비스에서 Event Grid를 통해 해당 이벤트를 노출 하려는 경우 

### <a name="auth0"></a>Auth0
**좋아하게 되었습니다** 는 사용할 수 있는 첫 번째 파트너 게시자입니다. [좋아하게 되었습니다 partner 항목](auth0-overview.md) 을 만들어 좋아하게 되었습니다 및 Azure 계정을 연결할 수 있습니다. 이러한 통합을 통해 실시간으로 좋아하게 되었습니다 이벤트에 반응, 기록 및 모니터링할 수 있습니다. 이를 [사용 하려면 Auto0와 Azure Event Grid 통합](auth0-how-to.md) 을 참조 하세요.

타사 서비스에서 Event Grid를 통해 해당 이벤트를 노출 하려면 [사용자 의견 포털](https://feedback.azure.com/forums/909934-azure-event-grid)에서 아이디어를 제출 하세요.
 
## <a name="resources-managed-by-event-publishers"></a>이벤트 게시자가 관리 하는 리소스
이벤트 게시자는 다음 리소스를 만들고 관리 합니다.

### <a name="partner-registration"></a>파트너 등록
등록은 게시자와 관련 된 일반 정보를 포함 합니다. 사용자가 파트너 토픽을 만들려고 할 때 옵션으로 Azure Portal에 표시 되는 파트너 토픽의 유형을 정의 합니다. 게시자는 구독자의 요구에 맞게 하나 이상의 파트너 토픽 유형을 노출할 수 있습니다. 즉, 게시자가 여러 서비스의 이벤트에 대해 별도의 등록 (파트너 토픽 유형)을 만들 수 있습니다. 예를 들어 HR (인적 자원) 서비스의 경우 게시자는 직원 조인, 직원 홍보 및 직원의 퇴사와 같은 이벤트에 대 한 파트너 항목을 정의할 수 있습니다. 

다음 사항을 주의하세요.

- Azure 승인 파트너 등록만 표시 됩니다. 
- 등록은 전역적입니다. 즉, 특정 Azure 지역에 연결 되지 않습니다.
- 등록은 선택적 리소스입니다. 하지만 게시자로 등록을 만드는 것이 좋습니다. 이를 통해 사용자는 [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic)의 **파트너 만들기 항목** 페이지에서 항목을 검색할 수 있습니다. 그런 다음 이벤트 구독을 만드는 동안 사용자가 이벤트 유형 (예: 직원 조인, 직원 왼쪽 등)을 선택할 수 있습니다.

### <a name="namespace"></a>네임스페이스
[사용자 지정 토픽](custom-topics.md) 및 [도메인과](event-domains.md)마찬가지로 파트너 네임 스페이스는 이벤트를 게시 하는 지역 끝점입니다. 게시자가 이벤트 채널을 만들고 관리 하는 네임 스페이스를 통하는 것입니다. 네임 스페이스는 이벤트 채널의 컨테이너 리소스로도 작동 합니다.

### <a name="event-channels"></a>이벤트 채널
이벤트 채널은 파트너 항목에 대 한 미러된 리소스입니다. 게시자가 게시자의 Azure 구독에서 이벤트 채널을 만들면 구독자의 Azure 구독에도 파트너 토픽이 생성 됩니다. 이벤트 채널 (GET 제외)에 대해 수행 되는 작업은 해당 구독자 파트너 토픽에도 적용 됩니다 (삭제에도 적용 됨). 그러나 파트너 항목만 구독과 이벤트 배달을 구성할 수 있는 리소스 종류입니다.

## <a name="resources-managed-by-subscribers"></a>구독자가 관리 하는 리소스 
구독자는 게시자에 의해 정의 된 파트너 항목을 사용할 수 있으며 해당 항목은 보고 관리 하는 유일한 리소스 유형입니다. 파트너 토픽을 만든 후에는 구독자 사용자가 [대상/이벤트 처리기](overview.md#event-handlers)에 대 한 필터 규칙을 정의 하는 이벤트 구독을 만들 수 있습니다. 구독자에 대 한 파트너 토픽 및 관련 된 이벤트 구독은 [사용자 지정 토픽](custom-topics.md) 과 동일한 풍부한 기능을 제공 하 고 관련 된 구독은 중요 한 차이점이 있습니다. 파트너 토픽은 지원 되는 다른 스키마 보다 풍부한 기능 집합을 제공 하는 [클라우드 이벤트 1.0 스키마](cloudevents-schema.md)만 지원 합니다.

다음 이미지는 제어 평면 작업의 흐름을 보여 줍니다.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="파트너 이벤트-제어 평면 흐름":::

1. 게시자는 **파트너 등록** 을 만듭니다. 파트너 등록은 전역입니다. 즉, 특정 Azure 지역에 연결 되지 않습니다. 이 단계는 선택 사항입니다.
1. 게시자는 특정 지역에 **파트너 네임 스페이스** 를 만듭니다.
1. 구독자 1이 파트너 토픽을 만들려고 할 때 먼저 게시자의 Azure 구독에 이벤트 **채널 인 이벤트** 채널 1이 생성 됩니다.
1. 그런 다음 파트너 **항목인** 파트너 항목 1이 구독자의 Azure 구독에 생성 됩니다. 구독자는 파트너 항목을 활성화 해야 합니다. 
1. 구독자 1은 파트너 항목 1에 대 한 **Azure Logic Apps 구독** 을 만듭니다.
1. 구독자 1은 파트너 항목 1에 대 한 **Azure Blob Storage 구독** 을 만듭니다. 
1. 구독자 2에서 파트너 토픽을 만들려고 하면 먼저 게시자의 Azure 구독에 다른 **이벤트** 채널 인 이벤트 채널 2가 만들어집니다. 
1. 그런 다음 두 번째 구독자의 Azure 구독에 **파트너 항목인 partner** 항목 2가 만들어집니다. 구독자는 파트너 항목을 활성화 해야 합니다. 
1. 구독자 2는 파트너 항목 2에 대 한 **Azure Functions 구독** 을 만듭니다. 

## <a name="pricing"></a>가격 책정
파트너 토픽은 Event Grid를 사용 하는 경우 수행 되는 작업의 수에 따라 요금이 청구 됩니다. 청구 및 자세한 가격 정보에 대 한 기준으로 사용 되는 모든 유형의 작업에 대 한 자세한 내용은 [Event Grid 가격 책정](https://azure.microsoft.com/pricing/details/event-grid/)을 참조 하세요.

## <a name="limits"></a>제한
파트너에 대 한 제한 항목에 대 한 자세한 내용은 [Event Grid 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [좋아하게 되었습니다 partner 항목](auth0-overview.md)
- [좋아하게 되었습니다 partner 항목을 사용 하는 방법](auth0-how-to.md)
- [Event Grid 파트너 되기](partner-onboarding-overview.md)
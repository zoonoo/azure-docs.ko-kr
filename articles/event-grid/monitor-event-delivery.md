---
title: Azure Event Grid 메트릭 보기 및 경고 설정
description: 이 문서에서는 Azure Portal를 사용 하 여 토픽 및 구독에 대 한 메트릭을 확인 하 고이에 대 Azure Event Grid 한 경고를 만드는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114886"
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid 메시지 배달 모니터링 
이 문서에서는 포털을 사용 하 여 토픽 및 구독 Event Grid에 대 한 메트릭을 확인 하 고이에 대 한 경고를 만드는 방법을 설명 합니다. 

## <a name="metrics"></a>메트릭

포털에는 이벤트 메시지 배달 상태에 대한 메트릭이 표시됩니다.

항목의 경우 몇 가지 메트릭이 있습니다.

* **게시 성공**: 이벤트가 성공적으로 토픽으로 전송되고 2xx 응답으로 처리되었습니다.
* **게시 실패**: 이벤트가 토픽으로 전송되었지만 거부되고 오류 코드가 표시되었습니다.
* **일치하지 않음**: 이벤트가 성공적으로 토픽에 게시되었지만 이벤트 구독과 일치하지 않습니다. 이벤트가 삭제되었습니다.

구독의 경우 다음 몇 가지 메트릭이 있습니다.

* **배달 성공**: 이벤트가 성공적으로 구독 엔드포인트로 배달되고 2xx 응답을 받았습니다.
* **배달 실패**: 서비스가 배달을 시도할 때마다 이벤트 처리기가 성공 2xx 코드를 반환 하지 않으면 **배달 실패** 카운터가 증가 합니다. 동일한 이벤트를 여러 번 배달 하려고 시도 하 고 실패 하는 경우 각 오류에 대해 **배달 실패** 카운터가 증가 합니다.
* **만료된 이벤트**: 이벤트가 배달되지 않았으며 다시 시도 횟수가 모두 전송되었습니다. 이벤트가 삭제되었습니다.
* **일치된 이벤트**: 토픽의 이벤트가 이벤트 구독과 일치되었습니다.

    > [!NOTE]
    > 메트릭의 전체 목록은 [Azure Event Grid에서 지 원하는 메트릭](metrics.md)을 참조 하세요.

## <a name="view-custom-topic-metrics"></a>사용자 지정 토픽 메트릭 보기

사용자 지정 항목을 게시한 경우 해당 메트릭을 볼 수 있습니다. 

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 항목의 검색 표시줄에 **Event Grid 항목**을 입력 한 다음 드롭다운 목록에서 **항목 Event Grid** 를 선택 합니다. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid 토픽 검색 및 선택":::
3. 항목 목록에서 사용자 지정 항목을 선택 합니다. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="사용자 지정 항목 선택":::
4. **Event Grid 항목** 페이지에서 사용자 지정 이벤트 항목에 대 한 메트릭을 봅니다. 다음 그림에서 리소스 그룹, 구독 등을 보여 주는 **Essentials** 섹션은 최소화 되어 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="이벤트 메트릭 보기":::

**Event Grid 토픽** 페이지의 **메트릭** 탭을 사용 하 여 지원 되는 메트릭을 포함 하는 차트를 만들 수 있습니다.

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="토픽-메트릭 페이지":::

메트릭에 대해 자세히 알아보려면 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md) 을 참조 하세요.

예를 들어 **게시 된 이벤트** 메트릭에 대 한 메트릭 차트를 참조 하세요.

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="게시 된 이벤트 메트릭":::


## <a name="view-subscription-metrics"></a>구독 메트릭 보기
1. 이전 섹션의 단계를 수행 하 여 **Event Grid 토픽** 페이지로 이동 합니다. 
2. 아래 예제와 같이 아래쪽 창에서 구독을 선택 합니다. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="이벤트 구독 선택":::    

    Azure Portal의 검색 표시줄에서 **Event Grid 구독** 을 검색 하 고 **토픽 유형**, **구독**및 **위치** 를 선택 하 여 이벤트 구독을 확인할 수도 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Event Grid 구독 페이지에서 이벤트 구독을 선택 합니다.":::        

    사용자 지정 항목의 경우 **토픽 형식**으로 **Event Grid 항목** 을 선택 합니다. 시스템 항목의 경우 **저장소 계정 (Blob, GPv2)** 과 같은 Azure 리소스의 유형을 선택 합니다. 
3. 차트에서 구독의 홈 페이지에서 구독에 대 한 메트릭을 확인 하세요. 지난 1 시간, 6 시간, 12 시간, 1 일, 7 일 또는 30 일 동안 **일반**, **오류**, **대기 시간**및 **배달 못 한 편지** 메트릭을 볼 수 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="구독 홈 페이지의 메트릭":::    

## <a name="view-system-topic-metrics"></a>시스템 항목 메트릭 보기

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 항목의 검색 표시줄에 **Event Grid 시스템 항목**을 입력 한 다음 드롭다운 목록에서 **시스템 항목 Event Grid** 를 선택 합니다. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid 시스템 항목을 검색 하 고 선택 합니다.":::
3. 항목 목록에서 시스템 항목을 선택 합니다. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="시스템 항목 선택":::
4. **Event Grid 시스템 항목** 페이지에서 시스템 항목에 대 한 메트릭을 봅니다. 다음 그림에서 리소스 그룹, 구독 등을 보여 주는 **Essentials** 섹션은 최소화 되어 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="개요 페이지에서 시스템 항목 메트릭 보기":::

**Event Grid 토픽** 페이지의 **메트릭** 탭을 사용 하 여 지원 되는 메트릭을 포함 하는 차트를 만들 수 있습니다.

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="시스템 항목-메트릭 페이지":::

메트릭에 대해 자세히 알아보려면 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- 메트릭 및 활동 로그 작업에 대 한 경고를 만드는 방법을 알아보려면 [경고 설정](set-alerts.md)을 참조 하세요.
- 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.

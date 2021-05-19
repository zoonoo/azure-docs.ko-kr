---
title: Azure Event Grid 메트릭 보기 및 경고 설정
description: 이 문서에서는 Azure Portal을 사용하여 Azure Event Grid 토픽 및 구독에 대한 메트릭을 확인하고 이에 대한 경고를 만드는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598561"
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid 메시지 배달 모니터링 
이 문서에서는 포털을 사용하여 Event Grid 토픽 및 구독에 대한 메트릭을 확인하고 이에 대한 경고를 만드는 방법을 설명합니다. 

> [!IMPORTANT]
> Azure Event Grid 지원 메트릭 목록은 [메트릭](metrics.md)을 참조하세요.

## <a name="view-custom-topic-metrics"></a>사용자 지정 토픽 메트릭 보기

사용자 지정 항목을 게시한 경우 해당 메트릭을 볼 수 있습니다. 

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 토픽의 검색 창에 **Event Grid 토픽** 을 입력한 다음, 드롭다운 목록에서 **Event Grid 토픽** 을 선택합니다. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid 토픽 검색 및 선택":::
3. 토픽 목록에서 사용자 지정 토픽을 선택합니다. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="사용자 지정 토픽 선택":::
4. **Event Grid 토픽** 페이지에서 사용자 지정 이벤트 토픽에 대한 메트릭을 봅니다. 다음 그림에서 리소스 그룹, 구독 등을 보여 주는 **Essentials** 섹션은 최소화되어 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="이벤트 메트릭 보기":::

    **Event Grid 토픽** 페이지의 **메트릭** 탭을 사용하여 지원되는 메트릭을 포함한 차트를 만들 수 있습니다.

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="토픽 - 메트릭 페이지":::

    예를 들어 **게시된 이벤트** 메트릭에 대한 메트릭 차트를 참조하세요.

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="게시된 이벤트 메트릭":::


## <a name="view-subscription-metrics"></a>구독 메트릭 보기
1. 이전 섹션의 단계를 따라 **Event Grid 토픽** 페이지로 이동합니다. 
2. 아래 예제와 같이 아래쪽 창에서 구독을 선택합니다. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text=" ":::    

    Azure Portal의 검색 표시줄에서 **Event Grid 구독** 을 검색하고 **토픽 유형**, **구독**, **위치** 를 선택하여 이벤트 구독을 확인할 수도 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Event Grid 구독 페이지에서 이벤트 구독 선택":::        

    사용자 지정 토픽의 경우 **토픽 형식** 으로 **Event Grid 토픽** 을 선택합니다. 시스템 토픽의 경우 **스토리지 계정(Blob, GPv2)** 과 같은 Azure 리소스의 유형을 선택합니다. 
3. 차트에 있는 구독 홈페이지에서 구독에 대한 메트릭을 확인하세요. 지난 1시간, 6시간, 12시간, 1일, 7일 또는 30일에 대한 **일반**, **오류**, **대기 시간** 메트릭을 볼 수 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="구독 홈페이지의 메트릭":::    

## <a name="view-system-topic-metrics"></a>시스템 토픽 메트릭 보기

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 항목의 검색 창에 **Event Grid System 토픽** 을 입력한 다음, 드롭다운 목록에서 **Event Grid System 토픽** 을 선택합니다. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid System 토픽 검색 및 선택":::
3. 토픽 목록에서 시스템 토픽을 선택합니다. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="시스템 토픽 선택":::
4. **Event Grid 시스템 토픽** 페이지에서 시스템 토픽에 대한 메트릭을 봅니다. 다음 그림에서 리소스 그룹, 구독 등을 보여 주는 **Essentials** 섹션은 최소화되어 있습니다. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="개요 페이지에서 시스템 토픽 메트릭 보기":::

    **Event Grid 토픽** 페이지의 **메트릭** 탭을 사용하여 지원되는 메트릭을 포함한 차트를 만들 수 있습니다.

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="시스템 토픽 - 메트릭 페이지":::

    > [!IMPORTANT]
    > Azure Event Grid 지원 메트릭 목록은 [메트릭](metrics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- 메트릭 및 활동 로그 작업에 대한 경고를 만드는 방법을 알아보려면 [경고 설정](set-alerts.md)을 참조하세요.
- 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.

---
title: Marketplace 계량 서비스 Api-FAQ | Azure Marketplace
description: Azure Marketplace에서 SaaS 제품의 사용 현황을 내보냅니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275784"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace 계량 서비스 API - FAQ

Azure 사용자가 요금제를 포함 하는 SaaS 서비스를 구독 하면 고객이 사용 하는 각 청구 차원에 대 한 사용량을 추적 하 게 됩니다. 고객이 선택한 용어에 대 한 포함 된 수량 집합을 초과 하는 경우 서비스는 사용 이벤트를 Microsoft에 내보냅니다.

## <a name="emit-usage-events"></a>사용 이벤트 내보내기

>[!Note]
>이 섹션은 SaaS 제품에만 적용 되며, 하나 이상의 계획에는 제품을 게시할 때 정의 된 계량 서비스 차원이 있습니다.

![사용 이벤트 내보내기](media/isv-emits-usage-event.png)

사용 이벤트를 내보내는 API 계약에 대 한 자세한 내용은 [SaaS batch 사용 이벤트 api](./marketplace-metering-service-apis.md#batch-usage-event) 를 참조 하세요.

### <a name="how-often-is-it-expected-to-emit-usage"></a>얼마나 자주 사용량을 내보낼 예정 인가요?

이전 시간에 사용량이 있는 경우에만 과거 시간에 대 한 시간 마다 사용을 내보내야 합니다.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>이벤트가 발생 하는 시간과 사용 이벤트를 Microsoft로 내보내는 시간 사이의 최대 지연 시간은 어떻게 되나요?

가장 이상적으로는 과거 시간에 발생 한 이벤트에 대해 1 시간 마다 사용 이벤트를 내보냅니다. 그러나 지연 시간이 필요 합니다. 허용 되는 최대 지연은 24 시간입니다. 그 후에는 사용 이벤트가 허용 되지 않습니다.

예를 들어, 하루에 오후 1 시에 사용 이벤트가 발생 하는 경우이 이벤트와 관련 된 사용 이벤트를 내보낼 수 있도록 다음 날 오후 1 시까지 실행 됩니다. 즉, 시스템을 내보내는 데 사용 되는 경우 작동 중단 시간이 발생 하 고,이를 복구한 후 사용 이벤트를 사용 하 여 사용 이벤트를 복구할 수 있습니다.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>같은 시간에 둘 이상의 사용 이벤트를 보내면 어떻게 되나요?

시간 간격에 대 한 사용 이벤트는 하나만 허용 됩니다. 시간 간격은 분 0에서 시작 하 고 59 분에 끝납니다.  같은 시간 간격으로 둘 이상의 사용 이벤트를 내보내는 경우 후속 사용 이벤트는 모두 중복으로 삭제 됩니다.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>이미 구독 취소 된 SaaS 구독에 대 한 사용 현황을 내보내는 경우는 어떻게 되나요?

SaaS 구독이 삭제 된 후에 marketplace 플랫폼으로 내보내는 모든 사용 이벤트는 허용 되지 않습니다.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>활성 구독 및 구독 취소 된 구독을 포함 하 여 모든 SaaS 구독 목록을 가져올 수 있나요?

예, API를 `GET /saas/subscriptions` 호출 하면 모든 SaaS 구독 목록이 포함 됩니다. 각 SaaS 구독에 대 한 응답의 상태 필드는 구독이 활성화 되었는지 아니면 구독 취소 되었는지를 캡처합니다. 구독 목록에 대 한 호출은 시간에 최대 100 개의 구독을 반환 합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Marketplace 계량 서비스 api](./marketplace-metering-service-apis.md) 를 참조 하세요.

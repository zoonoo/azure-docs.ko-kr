---
title: 마켓플레이스 계량 서비스 API - 자주 묻는 질문 | Azure 마켓플레이스
description: Azure 마켓플레이스에서 SaaS 제품의 사용을 내보전합니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275784"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace 계량 서비스 API - FAQ

Azure 사용자가 유료 청구가 포함된 SaaS 서비스를 구독하면 고객이 사용하는 각 청구 차원에 대한 사용량을 추적합니다. 사용량이 고객이 선택한 용어에 대해 설정된 포함된 수량을 초과하는 경우 서비스는 Microsoft에 사용 이벤트를 내보전합니다.

## <a name="emit-usage-events"></a>사용 이벤트 내보전

>[!Note]
>이 섹션은 계획 중 하나 이상이 오퍼를 게시할 때 정의된 계량 서비스 차원이 있는 SaaS 오퍼에만 적용됩니다.

![사용 이벤트 내보전](media/isv-emits-usage-event.png)

사용 이벤트를 내보내는 API 계약에 대한 자세한 내용은 [SaaS 일괄 사용 이벤트 API를](./marketplace-metering-service-apis.md#batch-usage-event) 참조하십시오.

### <a name="how-often-is-it-expected-to-emit-usage"></a>사용량을 얼마나 자주 방출해야 합니까?

이상적으로는 이전 시간에 사용량이 있는 경우에만 지난 시간 동안 매시간 사용량을 내포해야 합니다.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>이벤트가 발생하는 시간과 사용 이벤트가 Microsoft로 내보내는 시간 사이의 최대 지연은 얼마입니까?

이상적으로는 지난 시간에 발생한 이벤트에 대해 매시간 사용 이벤트가 내보내지는 이상적입니다. 그러나 지연이 예상됩니다. 허용되는 최대 지연 시간은 24시간이며, 이 후에는 사용 이벤트가 허용되지 않습니다.

예를 들어, 하루에 오후 1시에 사용 이벤트가 발생하는 경우 이 이벤트와 관련된 사용 이벤트를 내보내고 다음 날 오후 1시까지 사용해야 합니다. 즉, 사용량을 내보내는 시스템의 경우 가동 중지 시간이 있으며, 충실도를 잃지 않고 사용량이 발생한 시간 간격에 대해 사용 이벤트를 복구한 다음 보낼 수 있습니다.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>같은 시간에 두 개 이상의 사용 이벤트를 보내면 어떻게 되나요?

시간 간격동안 하나의 사용 이벤트만 허용됩니다. 시간 간격은 분 0에서 시작하여 59분에 끝납니다.  동일한 시간 간격으로 둘 이상의 사용 이벤트가 내보내지면 후속 사용 이벤트가 중복으로 삭제됩니다.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>이미 구독을 취소한 SaaS 구독에 대해 사용량을 내하면 어떻게 되나요?

SaaS 구독이 삭제된 후에는 마켓플레이스 플랫폼으로 내보내지는 모든 사용 이벤트가 허용되지 않습니다.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>활성 및 구독 취소 구독을 포함한 모든 SaaS 구독 목록을 얻을 수 있습니까?

예. API를 `GET /saas/subscriptions` 호출하면 모든 SaaS 구독 목록이 포함됩니다. 각 SaaS 구독에 대한 응답의 상태 필드는 구독이 활성 상태인지 구독 취소인지를 캡처합니다. 구독을 나열하기 위한 호출은 당시 최대 100개의 구독을 반환합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [마켓플레이스 계량 서비스 API를](./marketplace-metering-service-apis.md) 참조하십시오.

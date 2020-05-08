---
title: 계량 서비스 Api FAQ-Microsoft 상업적 marketplace
description: Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품에 대 한 계량 서비스 Api에 대 한 질문과 대답입니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857907"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace 계량 서비스 API - FAQ

Azure 사용자가 데이터 요금 청구를 포함 하는 SaaS 서비스를 구독 하는 경우 고객이 사용 하는 각 청구 차원에 대 한 사용량을 추적 합니다. 고객이 선택한 용어에 대 한 포함 된 수량 집합을 초과 하는 경우 서비스는 사용 이벤트를 Microsoft에 내보냅니다.

## <a name="emit-usage-events"></a>사용 이벤트 내보내기

>[!Note]
>이 섹션은 SaaS 제품에만 적용 되며, 하나 이상의 계획에는 제품을 게시할 때 정의 된 계량 서비스 차원이 있습니다.

![사용 이벤트 내보내기](media/isv-emits-usage-event.png)

사용 이벤트를 내보내는 API 계약에 대 한 자세한 내용은 [SaaS batch 사용 이벤트 api](./marketplace-metering-service-apis.md#batch-usage-event) 를 참조 하세요.

### <a name="how-often-is-it-expected-to-emit-usage"></a>얼마나 자주 사용량을 내보낼 예정 인가요?

이전 시간에 사용량이 있는 경우에만 과거 시간에 대 한 시간 마다 사용을 내보내야 합니다.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>이벤트가 발생 하는 시간과 사용 이벤트를 Microsoft로 내보내는 시간 사이의 최대 지연 시간은 어떻게 되나요?

가장 이상적으로는 과거 시간에 발생 한 이벤트에 대해 1 시간 마다 사용 이벤트를 내보냅니다. 그러나 지연 시간이 필요 합니다. 허용 되는 최대 지연은 24 시간입니다. 그 후에는 사용 이벤트가 허용 되지 않습니다.

예를 들어, 하루에 오후 1 시에 사용 이벤트가 발생 하는 경우이 이벤트와 관련 된 사용 이벤트를 내보낼 수 있도록 다음 날 오후 1 시까지 실행 됩니다. 사용량을 내보내는 시스템의 작동이 중단 되 면이를 복구 하 고 사용 이벤트를 사용 하는 시간 간격에 대 한 사용 이벤트를 그대로 보냅니다.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>같은 시간에 둘 이상의 사용 이벤트를 보내면 어떻게 되나요?

시간 간격에 대 한 사용 이벤트는 하나만 허용 됩니다. 시간 간격은 분 0에서 시작 하 고 59 분에 끝납니다.  같은 시간 간격으로 둘 이상의 사용 이벤트를 내보내는 경우 후속 사용 이벤트는 모두 중복으로 삭제 됩니다.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>이미 구독 취소 된 SaaS 구독에 대 한 사용 현황을 내보내는 경우는 어떻게 되나요?

SaaS 구독이 삭제 된 후에 marketplace 플랫폼으로 내보내는 모든 사용 이벤트는 허용 되지 않습니다.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>활성 구독 및 구독 취소 된 구독을 포함 하 여 모든 SaaS 구독 목록을 가져올 수 있나요?

예, API를 `GET /saas/subscriptions` 호출 하면 모든 SaaS 구독 목록이 포함 됩니다. 각 SaaS 구독에 대 한 응답의 상태 필드는 구독이 활성화 되었는지 아니면 구독 취소 되었는지를 캡처합니다. 구독 목록에 대 한 호출은 시간에 최대 100 개의 구독을 반환 합니다.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Marketplace 계량 서비스의 작동이 중단 되 면 어떻게 되나요?

ISV가 사용자 지정 측정기를 전송 하 고 오류를 수신 하는 경우 ISV는 기다린 후 다시 시도 해야 합니다.

오류가 계속 발생 하면 다음 시간 (수량 누적)으로 해당 사용자 지정 측정기를 다시 제출 합니다. 오류가 아닌 응답을 받을 때까지이 프로세스를 계속 합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Marketplace 계량 서비스 api](./marketplace-metering-service-apis.md)를 참조 하세요.

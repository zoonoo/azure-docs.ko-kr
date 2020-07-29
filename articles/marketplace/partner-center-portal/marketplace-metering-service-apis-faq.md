---
title: 계량 서비스 Api FAQ-Microsoft 상업적 marketplace
description: Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품에 대 한 계량 서비스 Api에 대 한 질문과 대답입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320003"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Marketplace 요금제 청구 Api-FAQ

고객이 SaaS 서비스를 구독 하거나, 관리 되는 앱 요금제를 사용 하 여 Azure 애플리케이션 데이터 요금 청구를 사용 하는 경우 사용 되는 각 청구 차원에 대 한 사용량을 추적 합니다.  고객이 선택한 용어에 대 한 포함 된 수량 집합을 초과 하는 경우 서비스는 사용 이벤트를 Microsoft에 내보냅니다.

## <a name="for-both-saas-offers-and-managed-apps"></a>SaaS 제품 및 관리 되는 앱 모두

### <a name="how-often-is-it-expected-to-emit-usage"></a>얼마나 자주 사용량을 내보낼 예정 인가요?

이전 시간에 사용량이 있는 경우에만 과거 시간에 대 한 시간 마다 사용을 내보내야 합니다.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>한 번의 내보내기와 다음 한 개의 내보내기와 그 사이에 최대 기간이 있나요?

이러한 제한은 없습니다. 사용 되는 경우에만 사용 합니다. 예를 들어 구독 수명 당 하나의 사용 단위를 제출 해야 하는 경우이 작업을 수행할 수 있습니다.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>이벤트가 발생 하는 시간과 사용 이벤트를 Microsoft로 내보내는 시간 사이의 최대 지연 시간은 어떻게 되나요?

가장 이상적으로는 과거 시간에 발생 한 이벤트에 대해 1 시간 마다 사용 이벤트를 내보냅니다. 그러나 지연 시간이 필요 합니다. 허용 되는 최대 지연은 24 시간입니다. 그 후에는 사용 이벤트가 허용 되지 않습니다. 가장 좋은 방법은 시간당 사용량을 수집 하는 것이 고, 내보내기는 시간 종료 시 하나의 이벤트입니다.

예를 들어, 하루에 오후 1 시에 사용 이벤트가 발생 하는 경우이 이벤트와 관련 된 사용 이벤트를 내보낼 수 있도록 다음 날 오후 1 시까지 실행 됩니다.  시스템 내보내기 사용이 중단 되는 경우에는이를 복구한 후 사용 이벤트를 사용 하는 시간 간격에 대 한 사용 이벤트를 그대로 사용 합니다.

실제 사용량 후 24 시간이 경과한 경우에도 이후 사용 이벤트로 소비 된 단위를 내보낼 수 있습니다.  그러나이 방법은 최종 고객에 대 한 청구 이벤트 보고서의 신뢰성을 저하 시킬 수 있습니다.  미터 내보내기가 하루/주/월에 한 번 전송 되지 않도록 하는 것이 좋습니다.  고객의 실제 사용량을 이해 하 고 사용 이벤트와 관련 하 여 발생할 수 있는 문제 또는 질문을 해결 하는 것이 더 어려워집니다.

1 시간 마다 사용을 전송 하는 또 다른 이유는 게시자가 일별/주별/월별 내보내기 이벤트를 보내기 전에 사용자가 구독을 취소 하는 상황을 방지 하기 위한 것입니다.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>같은 시간에 둘 이상의 사용 이벤트를 보내면 어떻게 되나요?

1 시간 간격 동안 사용 이벤트는 하나만 허용 됩니다. 시간 간격은 분 0에서 시작 하 고 59 분에 끝납니다.  같은 시간에 대해 둘 이상의 사용 이벤트를 내보내는 경우 후속 사용 이벤트는 모두 중복으로 삭제 됩니다.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>고객이 취소 정책에서 허용 하는 시간 내에 구매를 취소 하면 어떻게 되나요?

정액 요금에는 요금이 부과 되지 않지만 초과분 사용량은이 됩니다.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>일회성 지불에 사용자 지정 측정기 요금제를 사용할 수 있나요?

예, 사용자 지정 차원을 일회성 지불의 한 단위로 정의 하 고 각 고객에 대해 한 번만 내보낼 수 있습니다.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>사용자 지정 측정기 요금제를 계층화 된 가격 책정 모델에 사용할 수 있나요?

예, 단일 가격 계층을 나타내는 각 사용자 지정 차원으로 구현할 수 있습니다.

예를 들어 Contoso는 첫 번째 1000 전자 메일에 대해 $0.5 전자 메일, 1000와 5000 전자 메일 간의 $0.4 전자 메일, 위의 $0.2 전자 메일에 대 한 전자 메일 별로의 요금을 부과 하려고 합니다. 세 개의 사용자 지정 차원을 정의할 수 있으며 세 개의 전자 메일 가격 책정 계층에 해당 합니다. 전자 메일의 수가 1000 미만으로 유지 되는 경우 첫 번째 차원의 단위를 내보냅니다. 그러면 전자 메일 수가 1000에서 5000 사이이 고, 마지막으로 위의 5000 전자 메일에 대 한 세 번째 차원의 단위입니다.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Marketplace 계량 서비스의 작동이 중단 되 면 어떻게 되나요?

ISV가 사용자 지정 측정기를 전송 하 고 오류를 수신 하는 경우 Microsoft의 문제로 인해 발생 했을 수 있습니다. 일반적으로 오류 없이 유사한 이벤트가 허용 된 경우 ISV는 대기 하 고 내보내기를 다시 시도 해야 합니다.

오류가 계속 발생 하면 다음 시간 (수량 누적)으로 해당 사용자 지정 측정기를 다시 제출 합니다. 오류가 아닌 응답을 받을 때까지이 프로세스를 계속 합니다.

## <a name="for-saas-offers-only"></a>SaaS 제품에만 해당

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>이미 구독 취소 된 SaaS 구독에 대 한 사용 현황을 내보내는 경우는 어떻게 되나요?

SaaS 구독이 삭제 된 후에 marketplace 플랫폼으로 내보내는 모든 사용 이벤트는 허용 되지 않습니다.

구독 상태에서 구독에 대 한 사용 현황만 내보낼 수 있으며 `PendingFulfillmentStart` , 또는 상태의 구독은 사용할 수 없습니다 `Suspended` `Unsubscribed` .

유일한 예외는 SaaS 구독이 취소 되기 전의 시간에 대 한 사용 현황을 보고 하는 것입니다.

예를 들어 고객은 오늘 오후 3 시에 SaaS 구독을 취소 했습니다. 이제 오후 5 시 이며, 게시자는이 SaaS 구독에 대해 오늘 어제 오후 6 시와 오후 3 시 사이에 사용 현황을 내보낼 수 있습니다.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>활성 구독 및 구독 취소 된 구독을 포함 하 여 모든 SaaS 구독 목록을 가져올 수 있나요?

예, 모든 SaaS 구독 목록이 포함 되어 있으므로 [GET Subscription LIST API](pc-saas-fulfillment-api-v2.md#subscription-apis) 를 호출 하는 경우 각 SaaS 구독에 대 한 응답의 상태 필드는 구독이 활성화 되었는지 아니면 구독 취소 되었는지를 캡처합니다.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>SaaS 구독 약관 및 초과분 사용 현황 내보내기의 시작 및 종료 날짜가 연결 되어 있나요?

초과분 이벤트는 *구독* 상태에서 기존 SaaS 구독의 모든 시점에 내보낼 수 있습니다. 청구 계획에 정의 된 정책에 따라 사용 이벤트를 내보내는 것은 게시자의 책임입니다. 초과분은 SaaS 구독의 용어에 정의 된 날짜를 기준으로 계산 되어야 합니다. 

예를 들어 게시자가 $100의 1000 전자 메일을 월간 정액 속도로 포함 하는 SaaS 요금제를 정의 하는 경우 1000 위의 모든 전자 메일에는 사용자 지정 차원을 통해 $1이 청구 됩니다.

고객이 1 월 6 일에 구독을 구매 하 고 활성화 하면이 날부터 플랫 요금에 포함 된 1000 이메일이 계산 됩니다. 따라서 2 월 5 일 (구독의 첫 달의 끝) 까지만 900 이메일이 전송 됩니다 .이 경우 고객은이 구독의 첫 번째 달에 대해서만 고정 요금을 지불 하 고 1 월 6 일에서 2 월 5 일까 지 게시자가 초과분 사용 이벤트를 내보내지 않습니다. 2 월 6 일에 구독이 자동으로 갱신 되 고 카운트가 다시 시작 됩니다. 2 월 15 일에 고객이 보낸 1000 전자 메일에 도달 하면 3 월 5 일까지 전송 된 전자 메일의 나머지 부분은 게시자가 내보낸 초과분 사용 이벤트에 따라 초과분 (전자 메일 당 $1)로 청구 됩니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Marketplace 계량 서비스 api](./marketplace-metering-service-apis.md)를 참조 하세요.

---
title: 요금제에 대 한 변칙 검색 | Azure Marketplace
description: 요금제에 대 한 자동 변칙 검색을 통해 상용 marketplace 제품의 요금제 사용에 대 한 고객의 요금 청구를 보장할 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989467"
---
# <a name="anomaly-detection-for-metered-billing"></a>요금제에 대 한 변칙 검색

이 문서에서는 marketplace 계량 서비스와 관련 된 자동화 된 변칙 검색 기능에 대 한 세부 정보를 제공 하 여 고객에 게 요금제 사용을 위해 고객에 게 올바르게 청구서를 제공 합니다. 요금제 (요금제) 청구 옵션은 현재 SaaS ( [Software as a service](plan-saas-offer.md) ) 제품 및 관리 되는 응용 프로그램 계획으로 [Azure 응용 프로그램](plan-azure-application-offer.md#types-of-plans) 에 사용할 수 있습니다. 이 옵션을 사용 하면 파트너가 비표준 단위에 따라 요금이 청구 되는 상업적 marketplace 프로그램에 제품을 만들 수 있습니다.

SaaS 및 관리 되는 응용 프로그램에 대해 사용자 지정 측정기가 배포 된 파트너는 파트너 센터의 특정 _사용자 지정 미터_ 에 대 한 _초과분 이벤트_ 에 대 한 비정상으로 예상 사용 동작의 편차를 볼 수 있습니다. 위험을 완화 하기 위해 파트너 센터는 기계 학습 알고리즘을 적용 하 여 일반적인 요금제 청구 동작을 결정 하 고, 요금제 사용량을 분석 하 고, 최소한의 사용자 작업으로 변칙을 검색 하는 변칙 검색 서비스를 사용 합니다. 파트너 센터는 데이터 요금 청구 사용 데이터 집합에서 _변칙 검색 모델_ 을 사용 하 여 보고 된 사용이 예상 사용량을 초과 하는 경우 게시자에 게 알립니다.

## <a name="usability-experience"></a>유용성 환경

Microsoft는 파트너를 사용 하 여 고객에 게 고객이 송장을 제공 하기 전에 고객의 SaaS 또는 Azure 관리 되는 응용 프로그램의 초과분 사용을 보고 합니다. 잘못 된 사용량을 보고 하는 경우 고객은 잠재적으로 잘못 된 청구서를 수신 하 여 Microsoft와 파트너의 신뢰성을 모두 마이닝 하 게 됩니다.

이를 완화 하기 위해 SaaS 앱과 Azure 응용 프로그램 관리 응용 프로그램 계획 모두에 대 한 자동 변칙 검색 기능이 제공 됩니다. 이 기능은 데이터 요금제에 대 한 사용량을 사전에 모니터링 하 고 예상 된 범위 내에서 예상 사용량 값을 예측 하는 기계 학습 모델입니다. 사용량이 예상 범위를 벗어나면 비정상으로 처리 되 고 파트너 센터의 상업적 marketplace 프로그램의 제품 개요 페이지에서 파트너에 게 경고 알림이 표시 됩니다.

기계 학습 모델은 초과분 사용량을 매일 분석 합니다. 게시자는 모든 제품의 사용자 지정 측정기 차원에 대 한 고객의 초과분 사용량에 대해 보고 된 모든 비정상 상황을 볼 수 있습니다.

### <a name="view-and-manage-metered-usage-anomalies"></a>요금제 사용 변칙 보기 및 관리

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인 합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스**  >  **분석** 을 선택 합니다.
1. **요금제 사용 비정상** 상태 탭을 선택 합니다.

    [![사용량 페이지에서 요금제 사용에 대 한 탭을 보여 줍니다.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***그림 1: 요금제 사용 비정상 상태 탭***

1. 데이터 요금 청구에 대해 검색 된 사용 조건에 대 한 자세한 내용은 게시자로 확인 하 고 변칙이 true 인지 여부를 확인 하 라는 메시지가 표시 됩니다. **변칙으로 표시** 를 선택 하 여 진단을 확인 합니다.

     [![변칙으로 표시 대화 상자를 보여 줍니다.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***그림 2: 변칙 대화 상자로 표시***

1. 검색 된 초과분 사용이 정품이 아닌 것으로 판단 되는 경우 특정 초과분 사용에 대 한 파트너 센터 **의 비정상** 에 대해 비정상을 선택 하 여 해당 피드백을 제공할 수 있습니다.

    [![변칙 대화 상자가 아닌 이유를 보여 줍니다.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***그림 3: 변칙이 아닌 이유는 무엇 인가요? 대화 상자***

1. 페이지 아래쪽으로 스크롤하여 승인 되지 않은 변칙의 인벤토리 목록을 볼 수 있습니다. 이 목록에는 사용자가 승인 하지 않은 변칙의 인벤토리가 제공 됩니다. 파트너 센터에 플래그가 지정 된 변칙을 정품이 나 false로 표시 하도록 선택할 수 있습니다.

   [![사용량 페이지에서 파트너 센터 승인 되지 않은 변칙 목록을 보여 줍니다.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***그림 4: 승인 되지 않은 파트너 센터 변칙 목록***

1. 초과분 사용에 대해 수행한 작업을 보여 주는 변칙 작업 로그도 표시 됩니다. 작업 로그에서 정품이 나 false로 표시 된 초과분 usage 이벤트를 확인할 수 있습니다.

   [ ![ 사용량 페이지의 변칙 작업 로그를 보여 줍니다.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***그림 5: 변칙 작업 로그***

1. 파트너 센터 분석은 보고서 내보내기의 재작성 초과분 사용 이벤트를 지원 하지 않습니다. 파트너 센터에서는 비정상에 대해 수정 된 초과분 사용을 입력할 수 있으며, 자세한 내용은 조사를 위해 Microsoft 팀에 전달 됩니다. 조사에 따라 Microsoft는 고객에 게 적절 하 게 환불 신용 거래를 발급 합니다. 플래그가 지정 된 변칙 중 하나를 선택 하는 경우 **변칙으로 표시** 를 선택 하 여 사용 초과분 변칙을 정품으로 표시할 수 있습니다.

   [ ![ 변칙 대화 상자로 표시를 보여 줍니다.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***그림: 6: 변칙으로 표시 대화 상자***

파트너 센터에서 초과분 사용이 불규칙 하 게 표시 되는 경우 처음에는 해당 인스턴스에서 30 일의 기간을 사용 하 여 변칙을 정품이 나 false로 표시 합니다. 30 일 기간 후에는 이상에 대 한 작업을 수행할 수 없습니다.

> [!IMPORTANT]
> 보고 된 초과분 사용 단위는 재작성 및 재무 조정에 적합 하지 않습니다.

선택한 계산 기간에 대 한 모든 변칙 (승인 또는 기타)을 볼 수 있습니다. 다양 한 계산 기간은 지난 30 일, 지난 60 일 및 마지막 90 일입니다.

> [!IMPORTANT]
> 파트너가 파트너 센터에서 처음 보고 될 때부터 30 일 이내에 플래그가 지정 된 잘못 된 기능을 수행 하도록 요청 됩니다.

위젯의 필터 모달을 사용 하면 개별 제품 및 개별 사용자 지정 측정기를 선택할 수 있습니다.

초과분 사용을 비정상으로 표시 하거나 비정상으로 비정상으로 플래그가 지정 된 모델을 승인 하는 경우 선택 항목을 "비정상"으로 변경할 수 없습니다.

> [!IMPORTANT]
> 과도 한 요금이 부과 되는 경우 초과분 사용을 다시 제출할 수 있습니다.

## <a name="see-also"></a>참조
- [상용 marketplace 계량 서비스를 사용 하 여 SaaS에 대 한 요금제 청구](./partner-center-portal/saas-metered-billing.md)
- [관리 되는 응용 프로그램 요금제 청구](./partner-center-portal/azure-app-metered-billing.md)

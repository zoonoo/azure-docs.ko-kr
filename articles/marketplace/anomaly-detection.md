---
title: 파트너 센터에서 요금제 청구 이상 관리 | Azure Marketplace
description: 요금제에 대 한 자동 변칙 검색을 통해 상용 marketplace 제품의 요금제 사용을 위해 고객에 게 올바르게 청구 되도록 하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092526"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>파트너 센터에서 요금제 청구 이상 관리

사용자 지정 데이터 요금 청구 옵션은 현재 SaaS ( [Software as a service](plan-saas-offer.md) ) 제품 및 관리 되는 응용 프로그램 계획으로 [Azure 응용 프로그램](plan-azure-application-offer.md#types-of-plans) 에 사용할 수 있습니다.

요금제를 사용 하 여 비 표준 단위를 기준으로 사용량에 대 한 요금을 청구할 수 있는 상업적 marketplace 프로그램의 제안을 사용 하는 경우 고객이 예상 보다 더 많은 서비스를 사용 하는 시기를 알고 있어야 합니다.

## <a name="use-the-anomaly-detection-feature"></a>변칙 검색 기능 사용

Microsoft는 귀하의 파트너를 사용 하 여 Microsoft에서 고객에 게 송장을 제공 하기 전에 고객의 SaaS 또는 Azure 관리 되는 응용 프로그램의 초과분 사용량을 보고 합니다. 잘못 된 사용량을 보고 하는 경우 고객은 잠재적으로 잘못 된 청구서를 수신 하 여 Microsoft와 파트너의 신뢰성을 모두 마이닝 하 게 됩니다.

고객이 올바르게 청구 되도록 하려면 SaaS 앱과 Azure 응용 프로그램 관리 응용 프로그램 계획 모두에 대 한 **변칙 검색** 기능을 사용 합니다. 이 기능은 요금제에 대 한 사용량을 모니터링 하 고 예상 된 범위 내에서 예상 사용량 값을 예측 합니다. 사용량이 예상 범위를 벗어나면 예기치 않은 (비정상)로 처리 되며 파트너 센터의 상업적 marketplace 프로그램에서 제품 개요 페이지에 대 한 경고 알림을 받게 됩니다. 설정한 모든 사용자 지정 측정기 차원에 대 한 고객의 사용량을 매일 추적할 수 있습니다.

## <a name="view-and-manage-metered-usage-anomalies"></a>요금제 사용 변칙 보기 및 관리

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

## <a name="see-also"></a>참고 항목
- [상용 marketplace 계량 서비스를 사용 하 여 SaaS에 대 한 요금제 청구](./partner-center-portal/saas-metered-billing.md)
- [관리 되는 응용 프로그램 요금제 청구](./partner-center-portal/azure-app-metered-billing.md)
- [요금제 청구를 위한 변칙 검색 서비스](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)

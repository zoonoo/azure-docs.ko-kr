---
title: 사전 구매를 사용 하 여 Azure Databricks 비용 최적화
description: 어떻게 하면 수 선불 Azure Databricks 요금에 대 한 비용을 절약 하려면 예약 된 용량을 사용 하 여에 대해 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811259"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>사전 구매를 사용 하 여 Azure Databricks 비용 최적화

사용자 Azure Databricks에서 저장할 수 있습니다 (DBU) 단위 비용은 Azure Databricks 미리 구입한 경우 1 년 또는 3 년에 대 한 단위 (DBCU)를 커밋합니다. 구매 기간 중 언제 든 지 미리 구입한 DBCUs를 사용할 수 있습니다. Vm의 경우와 달리 미리 구입한 단위 시간 단위로 만료 되지 않습니다 및 구매 기간 중 언제 든 지 사용 합니다.

Azure Databricks를 사용할 때 자동으로 미리 구입한 Dbu에서 차감 합니다. 재배포 하거나 사전 구매 할인을 가져오려는 DBU 사용량에 대 한 Azure Databricks 작업 영역에 미리 구입한 계획을 할당할 필요가 없습니다.

사전 구매 할인 DBU 사용에만 적용 됩니다. 계산, 저장소, 네트워킹 등 기타 요금이 별도로 청구 됩니다.

## <a name="determine-the-right-size-to-buy"></a>구입 하려면 적절 한 크기를 결정 합니다.

Databricks 사전 구매 모든 Databricks 워크 로드 및 계층에 적용 됩니다. 선불 Databricks 커밋 단위 풀으로 사전 구매를 생각할 수 있습니다. 사용량은 워크 로드 또는 계층에 관계 없이 풀에서 차감 됩니다. 사용량 비율을 다음에서 차감 됩니다.

| **작업** | **DBU 응용 프로그램 비율-표준 계층** | **DBU 응용 프로그램 비율-프리미엄 계층** |
| --- | --- | --- |
| 데이터 분석 | 0.4 | 0.55 |
| 데이터 엔지니어링 | 0.15 | 0.30 |
| 데이터 엔지니어링 라이트 | 0.07 | 0.22 |

예를 들어, 데이터 분석-의 표준 계층을 사용 하는 경우 미리 구입한 Databricks 커밋 단위 0.4 단위에서 차감 됩니다.

구입 하기 전에 다양 한 작업 및 계층에 사용 된 총 DBU 수량을 계산 합니다. DBCU 정규화 한 후 다음 하나 또는 3 년간의 총 사용량 프로젝션을 실행 하려면 위의 비율을 사용 합니다.

## <a name="purchase-databricks-commit-units"></a>Databricks 커밋 단위를 구매 합니다.

Databricks 계획을 구매할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)합니다. 예약 된 용량을 구입 하려면 적어도 하나의 enterprise 구독에 대 한 소유자 역할이 있어야 합니다.

- 현재 사전 구매할 기업 계약 고객 에게만 제공 됩니다.
- Enterprise 구독을 하나 이상에 대 한 소유자 역할에 있어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용 하지 않으면 구독의 EA 관리자 여야 합니다.

**구매:**

1. [Azure 포털](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)로 이동합니다.
1. 구독을 선택합니다. 사용 된 **구독** 예약된 용량에 대 한 요금을 지불 하는 데 사용 되는 구독을 선택 하려면 목록입니다. 구독의 결제 예약 된 용량에 대해 선불로 비용이 청구 됩니다. 요금은 등록의 금액 약정 잔액에서 차감 또는 초과분으로 청구 됩니다.
1. 범위를 선택 합니다. 사용 된 **범위** 구독 범위를 선택 하는 목록:
    - **리소스 그룹 범위를 단일** -선택한 리소스 그룹에만 일치 하는 리소스에 예약 할인을 적용 합니다.
    - **구독 범위를 단일** -선택한 구독에서 일치 하는 리소스에 예약 할인을 적용 합니다.
    - **공유 범위** -를 청구 컨텍스트에서 적합 한 구독에서 리소스를 일치 하는 예약 할인을 적용 합니다. 기업 계약 고객에 대 한 청구 컨텍스트는 등록 합니다.
1. 구매 및 구매를 완료 하려면 Azure Databricks 커밋 단위 수를 선택 합니다.


![Azure portal에서 Azure Databricks 구매를 보여 주는 예제](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>범위 변경 및 소유권

예약 구매 후에 다음과 같은 유형의 변경 내용 만들 수 있습니다.

- 예약 범위를 업데이트 합니다.
- 역할 기반 액세스

분할 하거나 병합 커밋 Databricks 단위 사전 구매 수 없습니다. 예약을 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [예약 구매 후 관리](billing-manage-reserved-vm-instance.md)합니다.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

취소 및 exchange Databricks 사전 구매 요금제에 대 한 지원 되지 않습니다. 모든 구매 최종있지 않습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](billing-save-compute-costs-reservations.md)
  - [Azure Databricks 사전 구매 DBCU 할인이 적용 되는 방식을 이해 합니다.](billing-reservation-discount-databricks.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)

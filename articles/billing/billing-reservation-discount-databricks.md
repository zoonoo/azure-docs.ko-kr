---
title: Azure Databricks 사전 구매 할인은 적용 하는 방법
description: 사용 하는 Azure Databricks 사전 구매 할인 적용 하는 방법에 대해 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827631"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Azure Databricks 사전 구매 할인은 적용 하는 방법

구매 기간 중 언제 든 지 미리 구입한 Azure Databricks 커밋 단위 (DBCU)를 사용할 수 있습니다. 사용량은 모든 Azure Databricks는 자동으로 미리 구입한 DBCUs에서 차감 됩니다.

Vm의 경우와 달리 미리 구입한 단위는 시간 단위로 만료 되지 않습니다. 구매 기간 중 언제 든 지 사용할 수 있습니다. 사전 구매 할인 정보를 가져오려면 다시 배포 하거나 미리 구입한 계획을 사용 하 여 Azure Databricks 작업 영역에 할당할 필요가 없습니다.

Azure Databricks 단위 (DBU) 사용량만 사전 구매 할인 적용 됩니다. 계산, 저장소, 네트워킹 등 기타 요금이 별도로 청구 됩니다.

## <a name="pre-purchase-discount-application"></a>사전 구매 할인 응용 프로그램

Databricks 사전 구매 모든 Databricks 워크 로드 및 계층에 적용 됩니다. 선불 Databricks 커밋 단위 풀으로 사전 구매를 생각할 수 있습니다. 사용량은 워크 로드 또는 계층에 관계 없이 풀에서 차감 됩니다. 사용량 비율을 다음에서 차감 됩니다.

| **작업** | **DBU 응용 프로그램 비율-표준 계층** | **DBU 응용 프로그램 비율-프리미엄 계층** |
| --- | --- | --- |
| 데이터 분석 | 0.4 | 0.55 |
| 데이터 엔지니어링 | 0.15 | 0.30 |
| 데이터 엔지니어링 라이트 | 0.07 | 0.22 |

예를 들어, 데이터 분석-표준 계층의 사용 되 면 미리 구입한 Databricks 커밋 단위 0.4 단위에서 차감 됩니다. 데이터 엔지니어링 Light –의 표준 계층은 사용 되 면 미리 구입한 Databricks 커밋 단위 0.07 단위에서 차감 됩니다.

## <a name="determine-plan-use"></a>계획 사용 여부 결정

DBCU 계획 사용을 확인 하려면 Azure portal로 이동 > **예약** 구매한 Databricks 계획을 클릭 합니다. 프로그램 사용률-종료 날짜는 나머지 모든 단위를 사용 하 여 표시 됩니다. 예약을 결정 하는 방법에 대 한 자세한 내용은 다음을 사용 합니다 [예약 사용량을 확인할](billing-reservation-apis.md#see-reservation-usage) 문서.

## <a name="how-discount-application-shows-in-usage-data"></a>할인 응용 프로그램 사용 현황 데이터에 표시 하는 방법

사전 구매 할인 Databricks 사용량에 적용 될 때 주문형 요금은 사용 현황 데이터에는 0으로 표시 합니다. 예약 비용 및 사용량에 대 한 자세한 내용은 참조 하세요. [기업 계약 가져오기 예약 비용 및 사용량](billing-understand-reserved-instance-usage-ea.md)합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.
- 사전 구매 비용을 절약 하려면 Azure Databricks에 대 한 자세한 내용은 참조 하세요 [사전 구매를 사용 하 여 Azure Databricks 최적화 비용](billing-prepay-databricks-reserved-capacity.md)합니다.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure Reservations란?](billing-save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](billing-manage-reserved-vm-instance.md)
  - [종 량 제 요금으로 구독에 대 한 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)

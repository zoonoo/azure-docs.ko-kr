---
title: 예약 된 용량으로 계산 비용 절감
description: 계산 비용을 절약 하기 위해 Azure Data Factory 데이터 흐름 예약 용량을 구입 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: d41646ef1ef964db38245009717554eaeb783824
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526309"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>예약 된 용량 Azure Data Factory 데이터 흐름을 사용 하 여 리소스에 대 한 비용 절감

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

종 량 제 가격과 비교 하 여 계산 리소스에 대 한 예약을 커밋하여 Azure Data Factory 데이터 흐름 비용을 절감 하세요. 예약 된 용량을 사용 하면 1 년 또는 3 년 동안 ADF 데이터 흐름을 사용 하 여 계산 비용에 대 한 상당한 할인을 얻을 수 있습니다. 예약 된 용량을 구매 하려면 Azure 지역, 계산 유형, 코어 수 수량 및 용어를 지정 해야 합니다.

예약을 특정 팩터리 또는 통합 런타임에 할당할 필요는 없습니다. 기존 팩터리 또는 새로 배포 된 팩터리에서 자동으로 혜택을 받습니다. 예약을 구매 하 여 1 년 또는 3 년 동안 데이터 흐름 계산 비용에 대 한 사용량을 커밋합니다. 예약을 구매 하는 즉시 예약 특성과 일치 하는 계산 요금은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 

[예약 된 용량](https://portal.azure.com) [앞에 예약을 선택 하거나 월별 지불을](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md)선택 하 여 구매할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 종 량 제 요금은 하나 이상의 Enterprise 또는 개별 구독에 대 한 소유자 역할에 속해야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다. 예약 된 용량입니다.

엔터프라이즈 고객과 종 량 제 고객이 예약 구매에 대해 부과 하는 방법에 대 한 자세한 내용은 [기업 등록에 대 한 azure 예약 사용량 이해](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 및 [종 량 제 구독에 대 한 Azure 예약 사용량 이해](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage)를 참조 하세요.

> [!NOTE]
> 예약 된 용량을 구입 해도 사용을 위해 특정 인프라 리소스 (가상 머신 또는 클러스터)를 미리 할당 하거나 예약 하지 않습니다.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>구매 하기 전에 필요한 적절 한 Azure IR 크기 확인

예약 크기는 동일한 계산 계층을 사용 하 여 기존 또는 곧 배포 되는 데이터 흐름에서 사용 하는 총 계산 크기를 기준으로 해야 합니다.

예를 들어 32 코어를 사용 하 여 최적화 된 메모리를 사용 하는 파이프라인을 매시간 실행 한다고 가정해 보겠습니다. 또한 다음 달에 범용 64 코어를 사용 하는 추가 파이프라인을 배포 하려는 경우를 가정해 보겠습니다. 또한 최소 1년 동안 이러한 리소스가 필요할 것으로 가정해 보겠습니다. 이 경우 1 시간 동안 각 계산 형식에 필요한 코어 수를 입력 합니다. Azure Portal에서 예약을 검색 합니다. Data Factory > 데이터 흐름을 선택 하 고 범용으로 메모리 최적화 및 64에 32을 입력 합니다.

## <a name="buy-reserved-capacity"></a>예약된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음 **구매 예약** 창에서 **adf 데이터 흐름** 을 선택 하 여 adf 데이터 흐름에 대 한 새 예약을 구매 합니다.
4. 선택한 필수 필드 및 특성을 입력 하 여 예약 된 용량 할인을 받습니다. 할인을 받는 실제 데이터 흐름 수는 선택 된 범위와 수량에 따라 달라 집니다.
5. **비용** 섹션에서 용량 예약의 비용을 검토 합니다.
6. **구매** 를 선택합니다.
7. 구매 상태를 보려면 **이 예약 보기** 를 선택합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약 할인 이해](data-flow-understand-reservation-charges.md)

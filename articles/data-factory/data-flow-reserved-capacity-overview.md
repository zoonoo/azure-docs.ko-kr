---
title: 예약된 용량으로 컴퓨팅 비용 절감
description: Azure Data Factory 데이터 흐름 예약된 용량을 구매하여 컴퓨팅 비용을 절약하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.date: 02/05/2021
ms.openlocfilehash: a46a84b9dfc1b167f41d15b4e9e5fa0e91fd1b69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536376"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>예약된 용량으로 리소스 비용 절감 - Azure Data Factory 데이터 흐름

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

컴퓨팅 리소스를 예약에 커밋하여 종량제 가격 대비 Azure Data Factory 데이터 흐름 비용을 절감할 수 있습니다. 예약된 용량을 사용하면 ADF 데이터 흐름 사용량에 대해 1년이나 3년 기간의 약정을 체결하여 컴퓨팅 비용에서 상당한 할인을 받을 수 있습니다. 예약된 용량을 구입하려면 Azure 지역, 컴퓨팅 형식, 코어 개수 수량, 용어를 지정해야 합니다.

예약을 특정 팩터리 또는 통합 런타임에 할당할 필요는 없습니다. 기존 팩터리 또는 새로 배포된 팩터리는 자동으로 혜택을 받습니다. 예약을 구입하면 1년 또는 3년 동안의 데이터 흐름 컴퓨팅 비용을 사용량에 커밋하게 됩니다. 예약 용량을 구매하는 즉시 예약 특성과 일치하는 컴퓨팅 요금은 더 이상 종량제 요금으로 부과되지 않습니다. 

[사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md) 예약을 선택하여 [예약된 용량](https://portal.azure.com)을 구입할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 종량제 요금이 있는 하나 이상의 엔터프라이즈 구독 또는 개별 구독에 대한 소유자 역할에 속해야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다. 예약된 용량

예약 구매에 대해 엔터프라이즈 고객 및 종량제 고객에게 요금이 청구되는 방법에 대한 자세한 내용은 [엔터프라이즈 등록에서 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종량제 구독의 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조하세요.

> [!NOTE]
> 예약된 용량을 구입해도 내가 사용할 수 있도록 특정 인프라 리소스(가상 머신 또는 클러스터)가 미리 할당되거나 예약되는 것은 아닙니다.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>구입 전 필요한 적정 Azure IR 크기 확인

예약 크기는 동일한 컴퓨팅 계층을 사용하는 기존 데이터 흐름 또는 곧 배포될 데이터 흐름에 사용되는 총 컴퓨팅양을 기준으로 해야 합니다.

예를 들어 32코어로 최적화된 메모리를 사용하여 매시간 파이프라인을 실행한다고 가정해 보겠습니다. 또한 다음 달 내로 범용 64코어를 사용하는 추가 파이프라인을 배포할 계획이라고 가정해 보겠습니다. 또한 최소 1년 동안 이러한 리소스가 필요할 것으로 가정해 보겠습니다. 이 경우 1시간에 대해 각 컴퓨팅 형식에 필요한 코어 수를 입력합니다. Azure Portal에서 예약을 검색합니다. Data Factory > 데이터 흐름을 선택한 후 메모리 최적화의 경우 32를, 범용은 64를 입력합니다.

## <a name="buy-reserved-capacity"></a>예약된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음 **예약 구매** 창에서 **ADF 데이터 흐름** 을 선택하여 ADF 데이터 흐름에 대한 새 예약을 구입합니다.
4. 필수 필드를 작성하면 선택한 특성에 예약된 용량 할인을 받을 자격을 얻게 됩니다. 할인을 받을 실제 데이터 흐름 수는 선택한 범위 및 수량에 따라 달라집니다.
5. **비용** 섹션에서 용량 예약의 비용을 검토합니다.
6. **구매** 를 선택합니다.
7. 구매 상태를 보려면 **이 예약 보기** 를 선택합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약 할인 이해](data-flow-understand-reservation-charges.md)

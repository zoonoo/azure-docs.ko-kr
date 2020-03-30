---
title: 비용을 최적화하기 위해 Azure 코스모스 DB의 예약 용량
description: Azure Cosmos DB 예약 용량을 구매하여 컴퓨팅 비용을 절약하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505957"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Azure Cosmos DB에서 예약된 용량을 활용해 비용 최적화

Azure Cosmos DB 예약된 용량을 통해 1년 또는 3년 동안 Azure Cosmos DB 리소스 예약을 약정하여 비용을 절약할 수 있습니다. Azure Cosmos DB 예약 용량을 사용하여 Cosmos DB 리소스에 대 해 프로비전된 처리량에 할인을 받을 수 있습니다. 리소스의 예로 데이터베이스 및 컨테이너(테이블, 컬렉션 및 그래프)를 들 수 있습니다.

Azure Cosmos DB 예약 용량은 1년 또는 3년 선불 약정을 통해 Cosmos DB 비용을 정상 가격의 &mdash;최대 65%까지 크게 낮출 수 있습니다. 예약 용량은 청구 할인을 제공하며, Azure Cosmos DB 리소스의 런타임 상태에는 영향을 주지 않습니다.

Azure Cosmos DB 예약 용량은 리소스에 대해 프로비전된 처리량을 포함합니다. 스토리지 및 네트워킹 요금은 포함하지 않습니다. 예약 용량을 구매하는 즉시 예약 특성과 일치하는 처리량 요금은 더이상 종량제 요금으로 부과되지 않습니다. 예약에 대한 자세한 내용은 [Azure 예약](../cost-management-billing/reservations/save-compute-costs-reservations.md) 문서를 참조하세요.

Azure Cosmos DB 예약 용량은 [Azure Portal](https://portal.azure.com)에서 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/monthly-payments-reservations.md)로 처리할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

* 종량제 요금으로 하나 이상의 엔터프라이즈 또는 개별 구독의 소유자 역할에 있어야 합니다.  
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 영업 담당자만 Azure Cosmos DB 예약 용량을 구입할 수 있습니다.

## <a name="determine-the-required-throughput-before-purchase"></a>구매 전 필요한 처리량 결정

예약된 용량 구매의 크기는 기존 또는 곧 배포될 Azure Cosmos DB 리소스가 시간단위로 사용할 총 처리량을 기반으로 해야 합니다. 예: 일관된 시간당 사용 패턴인 경우 30,000 RU/s 예약 용량을 구매합니다. 이 예에서는 30,000RU/s를 초과하는 프로비저닝된 처리량은 종량제 요금을 사용하여 청구됩니다. 프로비저닝된 처리량이 한 시간 동안 30,000 RU/s 미만이면 해당 시간에 대한 추가 예약 용량이 낭비됩니다.

시간당 사용 패턴을 기반으로 구매 권장 사항을 계산합니다. 지난 7일, 30일 및 60일 동안의 사용량을 분석하여 절감효과를 극대화하는 예약 용량 구매를 권장합니다. 다음 단계를 사용하여 Azure 포털에서 권장 예약 크기를 볼 수 있습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.  

2. **모든 서비스** > **예약** > 추가 를**선택합니다.**

3. 구매 **예약** 창에서 **Azure 코스모스 DB를**선택합니다.

4. **권장** 예약을 보려면 권장 탭을 선택합니다.

다음 특성으로 권장 사항을 필터링할 수 있습니다.

- **기간(1년** 또는 3년)
- **청구 빈도(월별** 또는 선결제)
- **처리량 유형(RU** 대 다중 마스터 RU)

또한 권장 사항을 단일 리소스 그룹, 단일 구독 또는 전체 Azure 등록 내에 있도록 범위를 정할 수 있습니다. 

다음은 권장 사항입니다.

![예약된 용량 권장 사항](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

30,000 RU/s 예약을 구매하는 이 권장 사항은 3년 예약 중 30,000RU/s 예약 규모가 절감효과를 극대화할 수 있음을 나타냅니다. 이 경우 권장 사항은 Azure Cosmos DB 사용량의 지난 30일을 기준으로 계산됩니다. 이 고객이 Azure Cosmos DB 사용의 지난 30일이 향후 사용을 대표할 것으로 예상하는 경우 30,000 RU/s 예약을 구매하여 비용을 절감합니다.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 예약 용량 구매

1. [Azure 포털에](https://portal.azure.com)로그인합니다.  

2. **모든 서비스** > **예약** > 추가 를**선택합니다.**  

3. 구매 **예약** 창에서 **Azure 코스모스 DB를** 선택하여 새 예약을 구입합니다.  

4. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

   ![예약 용량 양식 채우기](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |필드  |설명  |
   |---------|---------|
   |Scope   |   얼마나 많은 구독이 예약과 연결된 청구 혜택을 사용할 수 있는지 제어하는 옵션입니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다. <br/><br/>  **공유**를 선택하면 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행되는 Azure Cosmos DB 인스턴스에 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 경우 공유 범위는 계정 관리자가 만든 종량제 요금이 있는 모든 개별 구독입니다.  <br/><br/>  **단일 구독**을 선택하면 예약 할인이 선택한 구독의 Azure Cosmos DB 인스턴스에 적용됩니다. <br/><br/> **단일 리소스 그룹을**선택하면 선택한 구독의 Azure Cosmos DB 인스턴스와 해당 구독 내의 선택한 리소스 그룹에 예약 할인이 적용됩니다. <br/><br/> 예약 용량을 구입한 후 예약 범위를 변경할 수 있습니다.  |
   |Subscription  |   Azure Cosmos DB 예약 용량에 대한 요금을 지불하는 데 사용되는 구독입니다. 선택한 구독의 결제 방법은 비용을 청구하는 데 사용됩니다. 구독은 다음 유형 중 하나여야 합니다. <br/><br/>  기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P): 엔터프라이즈 구독의 경우 등록의 통화 약정 잔액에서 요금이 차감되거나 초과로 청구됩니다. <br/><br/> 종량제 요금(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 구독: 종량제 요금이 있는 개별 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다.    |
   | 리소스 그룹 | 예약된 용량 할인이 적용되는 리소스 그룹입니다. |
   |용어  |   1년 또는 3년입니다.   |
   |처리량 유형   |  처리량은 요청 단위로 프로비전됩니다. 단일 지역 쓰기와 여러 리전 쓰기 - 두 설정모두에 대해 프로비저닝된 처리량에 대한 예약을 구입할 수 있습니다. 처리량 유형에는 시간당 100RU/s, 시간당 100개의 다중 마스터 RU/s 중에서 선택할 수 있는 두 가지 값이 있습니다.|
   | 예약된 용량 단위| 예약하려는 처리량이 크기입니다. 하위 지역별로 모든 Cosmos DB 리소스(예: 데이터베이스 또는 컨테이너)에 필요한 처리량을 확인하여 이 값을 계산할 수 있습니다. 그런 다음 Cosmos 데이터베이스와 연결할 지역 수를 곱합니다. 예를 들어 지역마다 100만 RU/초 용량이 필요한 5개의 하위 지역이 있는 경우 구매할 예약 용량으로 500만 RU/초를 선택합니다. |


5. 양식을 작성하면 예약된 용량을 구입하는 데 필요한 가격이 계산됩니다. 출력은 선택한 옵션으로 얻을 할인의 비율을 보여줍니다. 다음으로 **선택을 클릭합니다.**

6. 구매 **예약** 창에서 할인 및 예약 가격을 검토합니다. 이 예약 가격은 모든 하위 지역에서 처리량이 프로비전되는 Azure Cosmos DB 리소스에 적용됩니다.  

   ![예약된 용량 요약](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. **검토 + 구매를** 선택한 다음 지금 **구입하십시오.** 구매가 성공적으로 완료되면 다음과 같은 페이지가 표시됩니다.

예약이 구입되면 예약 조건과 일치하는 기존 Azure Cosmos DB 리소스에 즉시 적용됩니다. 기존 Azure Cosmos DB 리소스가 없으면 배포 시 예약 조건과 일치하는 새 Cosmos DB 인스턴스에 예약이 적용됩니다. 예약 기간은 두 경우 모두 구매하는 즉시 시작됩니다.

예약이 만료되는 경우 Azure Cosmos DB 인스턴스는 계속 실행되지만 일반 종량제 요금으로 청구됩니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치하는 Azure Cosmos DB 리소스에 자동으로 적용됩니다. 예약 범위는 Azure Portal, PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

*  예약 용량 할인이 Azure Cosmos DB에 적용되는 방법을 알아보려면 [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)를 참조하세요.

* Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

   * [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Azure 예약 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [파트너 센터 CSP 프로그램에서 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

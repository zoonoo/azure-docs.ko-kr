---
title: Azure Cosmos DB에 예약 할인이 적용되는 방식 이해 | Microsoft Docs
description: Azure Cosmos DB에서 프로비전된 처리량(RU/초)에 예약 할인이 적용되는 방식에 대해 알아봅니다.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8c5e9c8437c516a5e6d1d97494b21bfc8e90d88e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960263"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Azure Cosmos DB에 예약 할인이 적용되는 방식 이해

Azure Cosmos DB 예약된 용량을 구입하고 나면 예약의 특성 및 수량과 일치하는 Azure Cosmos DB 리소스에 예약 할인이 자동으로 적용됩니다. 예약 할인은 Azure Cosmos DB 리소스용으로 프로비전되는 처리량에 대해 적용되며 소프트웨어, 네트워킹, 저장소 또는 미리 정의된 컨테이너 요금에는 적용되지 않습니다.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Azure Cosmos DB 계정에 적용되는 예약 할인

예약 할인은 시간별 초당 요청 단위 수(RU/초)를 기준으로 하여 [프로비전된 처리량](../cosmos-db/request-units.md)에 적용됩니다. 1시간 내내 실행되지 않는 Azure Cosmos DB 리소스의 경우 예약 특성과 일치하는 다른 Cosmos DB 리소스에 예약 할인이 자동으로 적용됩니다. 동시에 실행되는 Azure Cosmos DB 리소스에도 할인이 적용될 수 있습니다. 1시간 내내 실행되며 예약 특성과 일치하는 Cosmos DB 리소스가 없으면 해당 시간 동안의 예약 할인 혜택 전액이 적용되지 않습니다.

* 할인은 계층별로 적용되므로 요청 단위 수가 많은 예약일수록 할인 금액도 큽니다.  
* 지역별 주문형 가격 책정 방식과 동일한 비율로 모든 지역의 예약 구매에 할인이 적용됩니다. 각 지역의 예약 할인 비율은 이 문서의 [지역별 예약 할인](#reservation-discount-per-region) 섹션을 참조하세요.

## <a name="reservation-discount-per-region"></a>지역별 예약 할인
단일 구독 또는 등록/계정 범위에서 시간별 Azure Cosmos DB 처리량 비용에 대해 예약 할인이 적용됩니다. 각 지역에서 사용 요금에 대해 적용되는 예약 할인의 비율은 다음과 같습니다.

|요금 설명  |지역 |비율  |
|---------|---------|---------|
|Azure Cosmos DB - 초당 RU 100개/시간 - 아시아 태평양 남동부  |  아시아 태평양 남동부    |   1      |
|Azure Cosmos DB - 초당 RU 100개/시간 - 아시아 태평양 동부 |   아시아 태평양 동부   |    1     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 북유럽|  북유럽       |    1     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 한국 남부|    한국 남부     |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 서유럽|    서유럽     |      1   |
|Azure Cosmos DB - 초당 RU 100개/시간 - 한국 중부|   한국 중부    |       1  |
|Azure Cosmos DB - 초당 RU 100개/시간 - 영국 남부|   영국 남부      |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 영국 서부|   영국 서부      |    1     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 영국 북부 |   영국 북부    |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 영국 남부 2|   영국 남부 2      |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 동부 2|  미국 동부 2     |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 중북부|   미국 중북부      |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 서부|   미국 서부      |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 중부| 미국 중부        |     1    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 서부 2|   미국 서부 2      |      1   |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 중서부|   미국 중서부      |       1  |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 동부|   미국 동부      |  1       |
|Azure Cosmos DB - 초당 RU 100개/시간 - 남아프리카 북부|     남아프리카 북부    |   1      |
|Azure Cosmos DB - 초당 RU 100개/시간 - 남아프리카 서부 |    남아프리카 서부      |    1     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 인도 남부|    인도 남부     |    1.0375    |
|Azure Cosmos DB - 초당 RU 100개/시간 - 캐나다 동부|   캐나다 동부      |    1.1      |
|Azure Cosmos DB - 초당 RU 100개/시간 - 일본 동부|   일본 동부      |    1.125     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 일본 서부|     일본 서부    |   1.125       |
|Azure Cosmos DB - 초당 RU 100개/시간 - 인도 서부|     인도 서부    |    1.1375     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 인도 중부|    인도 중부     |  1.1375       |
|Azure Cosmos DB - 초당 RU 100개/시간 - 오스트레일리아 동부|     오스트레일리아 동부    |   1.15       |
|Azure Cosmos DB - 초당 RU 100개/시간 - 캐나다 중부|  캐나다 중부       |   1.2       |
|Azure Cosmos DB - 초당 RU 100개/시간 - 프랑스 중부|   프랑스 중부      |    1.25      |
|Azure Cosmos DB - 초당 RU 100개/시간 - 브라질 남부|  브라질 남부       |   1.5      |
|Azure Cosmos DB - 초당 RU 100개/시간 - 오스트레일리아 중부|   오스트레일리아 중부      |   1.5      |
|Azure Cosmos DB - 초당 RU 100개/시간 - 오스트레일리아 중부 2| 오스트레일리아 중부 2        |    1.5     |
|Azure Cosmos DB - 초당 RU 100개/시간 - 프랑스 남부|    프랑스 남부     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>예약 할인이 적용되는 방식을 보여 주는 시나리오

예약에 다음 요구 사항이 적용된다고 가정해 보겠습니다.

* 필요한 처리량: 초당 RU 50,000개  
* 사용 지역: 2 

이 경우 총 주문형 용량의 요금은 초당 RU 100개 x 수량 500개 x 2개 지역이므로 초당 총 RU 사용량은 1시간에 100,000개입니다. 

**시나리오 1**

예를 들어 “미국 중북부” 및 “미국 서부” 지역에 Azure Cosmos DB를 배포해야 하는데 각 지역의 처리량 사용량이 초당 RU 50,000개인 경우 초당 RU 100,000개를 예약 구매하면 주문형 용량 요금과 정확히 일치하게 됩니다.

이 경우 예약에 따라 적용되는 할인은 (처리량 사용량 x 해당 지역의 예약 할인율)로 계산됩니다. "미국 중북부" 및 "미국 서부" 지역의 예약 할인율은 “1”입니다. 따라서 할인된 초당 총 RU 수는 초당 RU 100,000개(50,000 x 1 + 50,000 x 1 = 초당 RU 100,000개로 계산됨)이므로 종량제 정액 외에는 추가 요금을 지불하지 않아도 됩니다. 

|요금 설명 | 지역 |처리량 사용량(초당 RU 수) |초당 RU 수에 적용되는 예약 할인 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 중북부  |   미국 중북부  | 50,000  | 50,000  |
|Azure Cosmos DB - 초당 RU 100개/시간 - 미국 서부  |  미국 서부   |  50,000  |  50,000 |

**시나리오 2**

예를 들어 “오스트레일리아 중부 2” 및 “프랑스 남부” 지역에 Azure Cosmos DB를 배포해야 하는데 각 지역의 처리량 사용량이 초당 RU 50,000개인 경우 초당 RU 100,000개를 예약 구매하면 됩니다(오스트레일리아 중부 2의 사용량에 할인이 먼저 적용된다고 가정함).

|요금 설명 | 지역 |처리량 사용량(초당 RU 수) |초당 RU 수에 적용되는 예약 할인 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 초당 RU 100개/시간 - 오스트레일리아 중부 2  |  오스트레일리아 중부 2   |  50,000  |  50,000   |
|Azure Cosmos DB - 초당 RU 100개/시간 - 프랑스 남부  |  프랑스 남부   |  50,000 |  15,384  |

“오스트레일리아 중부 2” 지역의 사용량은 50,000개 단위는 청구 가능 사용량(정규화된 사용량)인 초당 RU 75,000개에 해당합니다. 이 값은 (처리량 사용량 x 해당 지역의 예약 할인율)로 계산되므로 여기서 청구 가능(정규화된) 사용량은 초당 RU 75,000개입니다. 이 값은 50,000 x 1.5 = 초당 RU 75,000개로 계산됩니다. 

이 시나리오의 경우 초당 RU 100,000개를 예약 구매하면 “오스트레일리아 중부 2”에서 초당 RU 75,000개를 사용하면 “프랑스 남부” 지역에서는 초당 RU 25,000개를 사용할 수 있습니다. 나머지 용량인 초당 RU 25,000개에 대해 “프랑스 남부” 지역에 예약 할인 용량인 초당 RU 15,384개(25,000/1.625 = 초당 RU 15,384개로 계산됨)가 적용됩니다. 따라서 “프랑스 남부” 지역의 경우 나머지 용량인 초당 RU 34,616개에 대해 종량제 정액이 청구됩니다. 

Azure 청구 시스템에서는 처리되는 인스턴스 중 예약 구성과 일치하는 첫 번째 인스턴스(이 경우에는 오스트레일리아 중부 2)에 예약 청구 혜택이 할당됩니다.

청구 사용량 보고서에서 Azure 예약의 응용 프로그램을 이해하고 보려면 [Azure 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 예약이란?](../billing/billing-save-compute-costs-reservations.md)  
* [Azure Cosmos DB 예약된 용량을 사용하여 Azure Cosmos DB 리소스 요금 선결제](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Azure SQL Database 예약된 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)  
* [Azure Reservations 관리](../billing/billing-manage-reserved-vm-instance.md)  
* [종량제 구독의 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage.md)  
* [엔터프라이즈 등록의 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [CSP 구독의 예약 사용량 이해](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.


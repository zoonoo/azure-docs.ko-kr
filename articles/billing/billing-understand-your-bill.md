---
title: Azure 청구서 이해
description: 읽기 및 사용량 및 Azure 구독에 대 한 청구서를 이해 하는 방법에 알아봅니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: c8b0d6febbffb80cb4e8bcbf62febb51d059282c
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490294"
---
# <a name="understand-your-microsoft-azure-bill"></a>Microsoft Azure 청구서 이해
Azure 청구서를 이해 하려면 Azure portal에서 비용 관리 보고서와 자세한 일별 사용 현황 파일을 사용 하 여 청구서를 비교 합니다.

이 문서는 기업계약을 체결한 Azure 고객(EA 고객)에게는 적용되지 않습니다. EA 고객 인 경우 참조 [기업 계약을 사용 하 여 Azure 고객에 대 한 요금 청구 방식](billing-understand-your-bill-ea.md)합니다.

이 문서에서는 Azure 고객에 게 적용 되지 않습니다는 [Microsoft 고객 계약](#check-access-to-a-microsoft-customer-agreement)합니다. Microsoft 고객 계약에 있는 경우 참조 [Microsoft 고객 계약 청구서에 Azure 요금 이해](billing-mca-understand-your-bill.md)합니다.

청구 주기, 가격 책정 및 사용량을 포함하여 Azure CSP(Azure Cloud Solution Provider) 프로그램에서 청구 작동 방법에 대한 설명은 [Azure CSP 청구 개요](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/)를 참조합니다.

## <a name="charges"></a>요금 검토

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

청구서 요금에 대한 추가 정보를 원하는 경우 사용량 및 요금을 사용량 파일이나 Azure Portal과 비교할 수 있습니다.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>옵션 1: 사용량 파일과 사용량 및 요금 비교

자세한 사용 현황 CSV 파일은 청구 기간 및 일별 사용 현황별로 청구 요금을 보여 줍니다. 참조를 다운로드 하거나 파일을 보려면 [Azure 청구 송장 및 일간 사용 현황 데이터를 받는](billing-download-azure-invoice-daily-usage-date.md)합니다.

사용 요금은 미터 수준으로 표시됩니다. 다음 용어는 청구서와 자세한 사용 현황 파일에서 동일한 의미로 사용됩니다. 예를 들어 청구서의 청구 주기는 세부 사용량 파일에 표시된 청구 기간과 같습니다.

 | 청구서(PDF) | 자세한 사용 현황(CSV)|
 | --- | --- |
|청구 주기 | 청구 기간 |
 |이름 |미터 범주 |
 |유형 |미터 하위 범주 |
 |리소스 |미터 이름 |
 |지역 |미터 영역 |
 |사용 |사용량 |
 |포함 |포함된 수량 |
 |청구 대상 |초과량 |

합니다 **사용 요금은** 청구서의 섹션에는 청구 기간 동안 사용 된 각 미터의 총 값을 표시 합니다. 예를 들어, 다음 이미지는 Azure Scheduler 서비스에 대 한 사용 요금을 보여 줍니다.

![청구서 사용 요금](./media/billing-understand-your-bill/1.png)

자세한 사용 현황 CSV의 **명세서** 섹션에도 동일한 요금이 표시됩니다. *사용* 부분과 *값* 부분 모두가 청구서와 일치합니다.

![CSV 사용 요금](./media/billing-understand-your-bill/2.png)

요금의 일별 분석 결과 보려면로 이동 합니다 **일일 사용량** CSV 파일의 섹션입니다. 에 대 한 필터링 *스케줄러* 아래에서 *미터 범주*합니다. 미터가 사용된 날짜와 사용량을 확인할 수 있습니다. *자원* 하 고 *리소스 그룹* 비교에 대 한 정보가 표시 됩니다. 합니다 *사용량이* 값 최대 추가 및 청구서에 표시 되는 내용이 일치 해야 합니다.

![CSV의 일일 사용량 섹션](./media/billing-understand-your-bill/3.png)

일별 비용을 가져오려면 *사용* 금액을 **명세서** 섹션의 *요율* 값으로 곱합니다.

자세한 내용은 다음을 참조하세요.

- [Azure 청구서 이해](billing-understand-your-invoice.md)
- [Azure 세부 사용량 이해](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>옵션 2: 사용량 및 Azure portal에서 비용 비교

Azure Portal은 요금을 확인하는 데도 도움이 됩니다. 청구서의 사용량 및 요금을 빠르게 살펴보려면 비용 관리 차트를 확인합니다.

1. Azure Portal에서 [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)으로 이동합니다.
1. 구독 > **비용 분석**을 선택합니다.
1. **시간 범위**로 필터링합니다.
1. 앞의 예제를 계속하려면 Azure Scheduler 서비스의 사용량 요금을 확인합니다.

   ![Azure Portal의 비용 분석 보기](./media/billing-understand-your-bill/4.png)

1. 일별 비용 분석을 보려는 요금을 보여 주는 행을 선택 합니다.

   ![Azure Portal의 비용 내역 보기](./media/billing-understand-your-bill/5.png)

자세히 알아보려면 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md#costs)를 참조하세요.

## <a name="external"></a>별도로 청구되는 외부 서비스

외부 서비스 또는 마켓플레이스 요금은 타사 소프트웨어 공급업체가 만든 리소스에 해당됩니다. 이러한 리소스는 Azure Marketplace에서 사용할 수 있습니다. 예를 들어 Barracuda Firewall은 타사가 제공하는 Azure Marketplace 리소스입니다. 방화벽 및 방화벽에 해당하는 미터의 모든 요금은 외부 서비스 요금으로 표시됩니다.

외부 서비스 요금은 별도로 청구됩니다. 이 요금은 Azure 청구서에 표시되지 않습니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)를 참조하세요.

## <a name="resources-billed-by-usage-meters"></a>사용량 미터로 청구되는 리소스

Azure는 리소스 요금을 기준으로 직접 청구하지 않습니다. 리소스 요금은 하나 이상의 미터를 사용하여 계산됩니다. 미터는 전체 수명 동안 리소스의 사용량을 추적 하는 데 사용 됩니다. 이러한 미터는 청구서를 계산하는 데 사용됩니다.

예를 들어 가상 머신과 같은 단일 Azure 리소스를 만드는 경우 하나 이상의 미터 인스턴스가 생성됩니다. 미터는 시간이 지남에 따라 리소스의 사용량을 추적 하는 데 사용 됩니다. 각 미터는 Azure에서 청구서를 계산하는 데 사용하는 사용량 레코드를 내보냅니다.

예를 들어 Azure에서 만든 단일 VM(가상 머신)의 경우 사용량을 추적하기 위해 다음과 같은 미터가 생성될 수 있습니다.

- 컴퓨팅 시간
- IP 주소 시간
- 데이터 수신
- 데이터 송신
- 표준 관리 디스크
- 표준 관리 디스크 작업
- 표준 IO-디스크
- 표준 IO-블록 Blob 읽기
- 표준 IO-블록 Blob 쓰기
- 표준 IO-블록 Blob 삭제

VM을 만든 경우 각 미터 사용량 레코드를 내보내기 시작 합니다. 이 사용량과 미터의 요금은 Azure 계량 시스템에서 추적됩니다.

## <a name="payment"></a>청구서 결제

결제 방법으로 신용 카드 또는 직불 카드를 설정한 경우 청구 기간 종료 후 10일 이내에 결제가 자동으로 이루어집니다. 신용 카드 명세서에는 **MSFT Azure** 항목으로 표시됩니다.

청구되는 신용 카드 또는 직불 카드를 변경하려면 [Azure에 대한 신용 카드 또는 직불 카드 추가, 업데이트 또는 제거](billing-how-to-change-credit-card.md)를 참조하세요.

[청구서 결제](billing-how-to-pay-by-invoice.md)의 경우 청구서 맨 아래에 기재된 위치로 결제 금액을 송금합니다.

귀하의 지불의 상태를 검사할 [지원 티켓을 만드는](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.


## <a name="tips-for-cost-management"></a>비용 관리 팁

- 다음을 사용하여 비용을 예상합니다.
  - [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)
  - [총 소유 비용 계산기](https://aka.ms/azure-tco-calculator)
  - [각 서비스에 대한 상세 가격 정보](https://azure.microsoft.com/pricing/)
- [Azure Portal에서 정기적으로 사용량 및 비용을 검토](billing-getting-started.md#costs)합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="learn-more"></a>자세한 정보

- [Azure 청구서 및 일일 사용량 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft Azure 청구서의 용어 이해](billing-understand-your-invoice.md)
- [Microsoft Azure 세부 사용량의 용어 이해](billing-understand-your-usage.md)
- [Azure Portal 비용 관리](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md#costs)

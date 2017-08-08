---
title: "Azure 청구서 이해 | Microsoft Docs"
description: "Azure 구독에 대한 사용량 및 청구를 읽고 이해하는 방법에 대해 알아봅니다."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 264078587b81d7840fe5976498a91ae4f535b6aa
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure 청구서 이해
Azure 청구서를 이해하려면 Azure Portal에서 자세한 일별 사용 현황 파일 및 비용 관리 보고서와 청구서를 비교합니다.

청구서 PDF 및 자세한 일별 사용 현황 파일 CSV 다운로드는 [Azure 청구서 및 일별 사용 현황 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)을 참조하세요. 

청구서 및 자세한 일별 사용 현황 파일의 자세한 용어 및 설명은 [Microsoft Azure 청구서의 용어 이해](billing-understand-your-invoice.md) 및 [Microsoft Azure 자세한 사용 현황의 용어 이해](billing-understand-your-usage.md)를 참조하세요.

비용 관리 보고서에 대한 세부 정보는 [Azure Portal 비용 관리](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started)를 참조하세요.

## <a name="reconcile"></a>청구서의 요금이 맞는지 확인하려면 어떻게 할까요?
자세한 내용을 보려는 청구서에 대한 요금이 청구되는 경우 다음과 같은 두 가지 옵션이 있습니다.

### <a name="review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>청구서를 검토하고 자세한 사용 현황 CSV 파일과 사용 현황 및 비용 비교

자세한 사용 현황 CSV 파일은 청구 기간 및 일별 사용 현황에 따라 사용자의 요금을 청구합니다. 자세한 사용 현황 CSV 파일을 가져오려면 [Azure 청구서 및 일별 사용 현황 데이터를 가져오는 방법](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date)을 참조하세요.

미터 수준에서 사용 현황 요금이 표시됩니다. 다음 용어 목록은 청구서와 자세한 사용 현황 파일 간에 동일한 항목을 의미합니다. 예를 들어 청구서의 청구 주기는 자세한 사용 현황 파일에 표시된 요금 청구 기간과 같습니다.

 | 청구서(PDF) | 자세한 사용 현황(CSV)|
 | --- | --- |
 |대금 청구 주기 | 청구 기간 |
 |이름 |측정기 범주 |
 |형식 |미터 하위 범주 |
 |리소스 |측정기 이름 |
 |지역 |측정기 영역 |
 |사용 |소비된 수량 |
 |포함됨 |표함된 수량 |
 |청구 가능 |초과 수량 |

청구서의 사용 요금 섹션에는 청구 기간 동안 사용된 각 미터의 총 값이 있습니다. 예를 들어, 다음 스크린샷은 Azure Scheduler 서비스에 대한 사용 요금을 보여줍니다.

![청구서 사용 요금](./media/billing-understand-your-bill/1.png)

동일한 요금이 자세한 사용 현황 CSV의 문 섹션에 표시됩니다.

![CSV 사용 요금](./media/billing-understand-your-bill/2.png)

또한 자세한 사용 현황 CSV 파일에는 일별 사용 현황 섹션이 있습니다. 섹션에는 미터를 사용한 날짜에 대한 줄 항목이 있습니다. 이러한 줄 항목은 청구 기간 동안 미터의 소비된 수량에 추가되어야 합니다.

청구서의 자세한 섹션, 용어 및 설명입니다. [여기](billing-understand-your-invoice.md)를 참조하세요.

자세한 일별 사용 현황 파일의 자세한 섹션, 용어 및 설명은 [여기](billing-understand-your-usage.md)를 참조하세요.

### <a name="review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Azure Portal에서 청구서 검토 및 사용 현황, 비용과 비교

Azure Portal은 요금을 확인하는 데도 도움이 됩니다. Azure Portal은 청구서의 사용 현황 및 요금의 빠른 개요에 대한 비용 관리 차트를 제공합니다.

Azure Portal에서 사용할 수 있는 비용 관리 도구의 몇 가지 예는 다음과 같습니다.

![Azure Portal에서 리소스별 비용](./media/billing-understand-your-bill/3.png)

이 차트는 리소스별 현재 비용을 제공합니다.

![Azure Portal에서 지출율 및 예측](./media/billing-understand-your-bill/4.png)

이 차트는 이 구독에 대한 현재 비용을 보여주고 청구 기간의 나머지 부분에 대한 비용을 예측합니다.

![Azure Portal의 비용 분석 보기](./media/billing-understand-your-bill/5.png)

비용 분석은 다양한 청구 기간에 대한 리소스 및 미터 수준 사용 현황을 제공합니다.

자세히 알아보려면 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md#costs)를 참조하세요.

## <a name="external"></a>외부 서비스 요금은 어떤가요?
외부 서비스(또는 Marketplace 주문)는 독립 서비스 공급업체에서 제공하며 별도로 청구됩니다. Azure 청구서에 요금이 표시되지 않습니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)를 참조하세요.

## <a name="payment"></a>지불하려면 어떻게 할까요?
결제 방법으로 신용 카드 또는 직불 카드를 설정한 경우 자동으로 결제됩니다. 신용 카드 명세서에서 품목명은 **MSFT Azure**입니다.

[결제를 청구서로 보내려면](https://azure.microsoft.com/pricing/invoicing/) 청구서 아래쪽에 나열된 위치로 결제 금액을 보냅니다. 자세한 도움말은 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>신용 카드 지불 상태를 어떻게 확인하나요?
결제 상태를 요청하는 [지원 티켓을 만듭니다](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

## <a name="tips-for-cost-management"></a>비용 관리 팁
- [가격 계산기](https://azure.microsoft.com/pricing/calculator/), [총 소유 비용 계산기](https://aka.ms/azure-tco-calculator)를 사용하여 서비스를 추가할 때 비용 예측
- [청구 경고 설정](billing-set-up-alerts.md)
- [Azure Portal에서 정기적으로 사용량 및 비용 검토](billing-getting-started.md#costs)

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요. 
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.


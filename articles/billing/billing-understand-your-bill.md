---
title: Azure 청구서 이해 | Microsoft Docs
description: Azure 구독에 대한 사용량 및 청구를 읽고 이해하는 방법에 대해 알아봅니다.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: 689ea9e0d029bb65bc579fc914c6ed3073b4a96b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064624"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure 청구서 이해
Azure 청구서를 이해하려면 청구서를 자세한 일별 사용 현황 파일 및 Azure Portal의 비용 관리 보고서와 비교합니다.

>[!NOTE]
>이 문서는 EA(기업 계약) 고객에게는 적용되지 않습니다. EA 고객인 경우 [엔터프라이즈 포털에서 청구서 설명서를 찾을 수 있습니다.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

청구서 PDF 및 자세한 일별 사용 현황 파일 CSV 사본을 다운로드하려면 [Azure 청구서 및 일별 사용 현황 데이터 가져오기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. 

청구서 및 자세한 일별 사용 현황 파일의 세부 용어 및 설명은 [Microsoft Azure 청구서의 용어 이해](billing-understand-your-invoice.md) 및 [Microsoft Azure의 자세한 사용 현황의 용어 이해](billing-understand-your-usage.md)를 참조하세요. 

비용 관리 보고서에 대한 세부 정보는 [Azure Portal 비용 관리](https://docs.microsoft.com/azure/billing/billing-getting-started)를 참조하세요.

## <a name="charges"></a>청구서의 요금이 올바른지 확인하는 방법은 무엇인가요?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

청구서에 자세히 알아보려는 요금이 있는 경우 다음과 같은 두 가지 옵션을 통해 확인할 수 있습니다.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>옵션 1: 청구서를 검토하고 사용 현황 및 비용을 자세한 사용 현황 CSV 파일과 비교

자세한 사용 현황 CSV 파일은 청구 기간 및 일별 사용 현황별로 청구 요금을 보여 줍니다. 자세한 사용 현황 CSV 파일을 가져오려면 [Azure 청구서 및 일별 사용 현황 데이터 가져오기](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date)를 참조하세요.

사용 요금은 미터 수준으로 표시됩니다. 다음 용어는 청구서와 자세한 사용 현황 파일에서 동일한 의미로 사용됩니다. 예를 들어 청구서의 청구 주기는 자세한 사용 현황 파일에 표시된 요금 청구 기간과 같습니다.

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

청구서의 **사용 요금** 섹션에는 청구 기간 동안 사용된 각 미터의 총 값이 나와 있습니다. 예를 들어, 다음 스크린샷은 Azure Scheduler 서비스에 대한 사용 요금을 보여줍니다.

![청구서 사용 요금](./media/billing-understand-your-bill/1.png)

자세한 사용 현황 CSV의 **명세서** 섹션에도 동일한 요금이 표시됩니다. *사용* 부분과 *값* 부분 모두가 청구서와 일치합니다.

![CSV 사용 요금](./media/billing-understand-your-bill/2.png)

일일 단위로 이 요금의 세부 내역을 확인하려면 CSV의 **일일 사용량** 섹션으로 이동하세요. *미터 범주*에서 "Scheduler"로 필터링하면 미터가 사용된 기간(일)과 사용량을 확인할 수 있습니다. 비교를 위해 *리소스*와 *리소스 그룹* 정보도 나열됩니다. *사용* 값은 모두 합산되어 청구서에 표시된 금액이 되어야 합니다.

![CSV의 일일 사용량 섹션](./media/billing-understand-your-bill/3.png)

일별 비용을 가져오려면 *사용* 금액을 **명세서** 섹션의 *요율* 값으로 곱합니다.

청구서에 대해 자세히 알아 보려면 [Azure 청구서 이해](billing-understand-your-invoice.md)를 참조하세요.

CSV에 있는 각 열에 대한 자세한 내용은 [Azure의 자세한 사용 현황 이해](billing-understand-your-invoice.md)를 참조하세요.

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>옵션 2: 청구서를 검토하고 Azure Portal에서 사용 현황 및 비용과 비교

Azure Portal에서 요금을 확인할 수도 있습니다. Azure Portal은 청구서의 사용 현황 및 요금의 간략한 개요를 나타내는 비용 관리 차트를 제공합니다.

위의 예로 계속하려면 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동하여 구독을 선택한 다음, **비용 분석**을 선택합니다. 이어서 기간을 지정하고 Azure Scheduler 서비스에 대한 사용 요금을 확인할 수 있습니다.

![Azure Portal의 비용 분석 보기](./media/billing-understand-your-bill/4.png)

**비용 내역**에서 일별 비용 세부 내용을 확인하려면 원하는 행을 클릭합니다.

![Azure Portal의 비용 내역 보기](./media/billing-understand-your-bill/5.png)

자세히 알아보려면 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md#costs)를 참조하세요.

## <a name="external"></a>외부 서비스 요금은 어떤가요?
외부 서비스(또는 Azure Marketplace 주문)는 독립 서비스 공급업체에서 제공하며 별도로 청구됩니다. 이 요금은 Azure 청구서에 표시되지 않습니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)를 참조하세요.

## <a name="payment"></a>지불하려면 어떻게 할까요?

결제 방법으로 신용 카드 또는 직불 카드를 설정한 경우 청구 기간 종료 후 10일 이내에 결제가 자동으로 이루어집니다. 신용 카드 명세서에는 **MSFT Azure** 항목으로 표시됩니다.

[청구서 결제](billing-how-to-pay-by-invoice.md)의 경우 청구서 하단에 기재된 위치로 결제 금액을 송금합니다. 자세한 도움은 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>신용 카드 결제 상태는 어떻게 확인하나요?

[지원 티켓을 작성](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 결제 상태 확인을 문의합니다. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>다른 Azure 고객 유형이 있나요? 내가 어떤 유형의 고객인지 알려면 어떻게 해야 하나요?
Azure 고객에는 여러 가지 유형이 있습니다. 가격 책정 및 청구서를 잘 이해하려면 다음 고객 유형 설명을 참조하세요.

- **Enterprise**: 기업 고객은 Azure와 기업 계약을 체결하여 현금 약정 금액을 협상하고 Azure 리소스에 대한 사용자 지정 가격에 액세스할 수 있습니다.
- **Web Direct**: Direct 웹 고객은 Azure와 사용자 지정 계약을 체결하지 않습니다. 이 고객은 azure.com을 통해 Azure에 등록하고 Azure의 모든 리소스에 대해 공개된 가격이 적용됩니다.
- **클라우드 서비스 공급자**: 일반적으로 클라우드 서비스 공급자는 최종 고객이 Azure를 기반으로 솔루션을 구축하기 위해 고용한 회사입니다.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>내가 만든 리소스에 대한 비용이 청구서에 표시되지 않는 이유가 무엇인가요?
Azure는 리소스 비용에 직접 기반하여 청구하지 않습니다. 청구는 리소스의 수명 전체를 통틀어서 사용량을 추적하는 데 사용되는 하나 이상의 미터를 기반으로 수행됩니다. 이러한 미터는 청구서를 계산하는 데 사용됩니다. Azure 계량(metering)에 대한 자세한 내용은 아래를 참조하세요.

## <a name="how-does-azure-charge-metering-work"></a>Azure 요금 계량 원리는 무엇인가요?
Azure 리소스(예: 가상 머신) 하나를 스핀업하면 하나 이상의 미터 인스턴스도 같이 만들어집니다. 미터는 시간별 리소스 사용량을 추적하는 데 사용됩니다. 각 미터가 사용량 레코드를 내보내면 당사의 비용 측정 시스템에서 Azure가 청구서를 계산하는 데 사용됩니다. 

예를 들어, Azure에서 생성된 단일 가상 머신에서 사용량을 추적하기 위해 다음과 같은 미터를 생성할 수 있습니다.

- 계산 시간
- IP 주소 시간
- 데이터 수신
- 데이터 송신
- 표준 관리 디스크
- 표준 관리 디스크 작업
- 표준 IO-디스크
- 표준 IO-블록 Blob 읽기
- 표준 IO-블록 Blob 쓰기
- 표준 IO-블록 Blob 삭제

VM이 생성되면 위의 미터는 각자 사용량 레코드를 내보내기 시작합니다. 사용량은 미터의 가격과 함께 Azure의 계량 시스템에 사용되어 고객에게 부과되는 요금을 결정합니다.

> [!Note]
> 위의 예제 미터는 생성된 VM을 만든 미터의 하위 집합 일 수 있습니다.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Azure 자사 요금과 Azure Marketplace 요금의 차이는 무엇인가요?
자사 요금은 Azure가 직접 개발하고 제공한 리소스에 대한 요금입니다. Azure Marketplace 요금은 Azure Marketplace를 통해 사용할 수 있는 타사 소프트웨어 공급업체가 생성한 리소스에 대한 요금입니다. 예를 들어 Barracuda Firewall은 타사가 제공하는 Azure Marketplace 리소스입니다. 방화벽 및 방화벽에 해당하는 미터의 모든 요금은 마켓플레이스 요금으로 표시됩니다. 

## <a name="tips-for-cost-management"></a>비용 관리 팁
- [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)와 [총 소유 비용 계산기](https://aka.ms/azure-tco-calculator)를 사용하여 비용을 산정하고 [각 서비스에 대한 자세한 요금 정보](https://azure.microsoft.com/pricing/)를 가져옵니다.
- [청구 알림을 설정](billing-set-up-alerts.md)합니다.
- [Azure Portal에서 정기적으로 사용량 및 비용을 검토](billing-getting-started.md#costs)합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

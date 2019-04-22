---
title: Microsoft 고객 계약 청구서 이해 | Microsoft Docs
description: 읽고 MCA 청구서를 이해 하는 방법을 알아봅니다
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: ee6317f61f95b19effd64308b88f53c027582b63
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883012"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Microsoft 고객 계약 청구서에서 용어 이해

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

청구서는 요금 및 지불에 대 한 지침의 요약을 제공합니다. [Azure Portal](https://portal.azure.com/)에서 .pdf(Portable Document Format)로 다운로드하고 전자 메일을 통해 전송할 수 있습니다. 자세한 내용은 [Microsoft Azure 청구서 보기 및 다운로드](billing-download-azure-invoice.md)합니다.

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>송장 용어 및 설명

다음 섹션에서는 각 용어에 대 한 청구서 및 설명에 표시 되는 중요 한 용어를 나열 합니다.

### <a name="invoice-summary"></a>청구서 요약

합니다 **청구서 요약** 첫 번째 페이지의 맨 위에서 이며 청구 프로필 및 지불 방법에 대 한 정보를 보여 줍니다.

![청구서 요약 섹션](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 용어 | 설명 |
| --- | --- |
| 구매처 |청구 계정 속성에서 찾을 법인의 주소|
| 청구지 |대금 청구 주소, 청구 프로필 속성에 있는 송장을 받은 청구 프로필|
| 청구 프로필 |송장을 받은 청구 프로필의 이름 |
| P.O. number |추적을 위해 사용자에 의해 할당된 선택적 구매 주문 번호 |
| 청구서 번호 |추적용으로 사용할를 고유 하 고 Microsoft에서 생성 된 청구서 번호 |
| 청구서 날짜 |청구서 생성 되는 일반적으로 5 ~ 12 일 후 청구 주기 종료 날짜입니다. 청구 프로필 속성에 송장 날짜를 확인할 수 있습니다.|
| 지불 조건 |Microsoft 청구서에 대 한 지불 방법입니다. *30 일 동안 net* 송장 일 로부터 30 일 이내에 지불을 의미 합니다. |

### <a name="billing-summary"></a>청구 요약

합니다 **청구 요약** 기한 이후에 이전 청구 기간이, 적용 된 크레딧은, 세금 및 총 청구 프로필에 대 한 요금을 나타냅니다.

![청구 요약 섹션](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 용어 | 설명 |
| --- | --- |
| Charges|마지막 청구 기간 이후이 청구 프로필에 대 한 Microsoft 요금은의 총 수 |
| 크레딧 |반환에서 받은 크레딧 |
| Azure 크레딧은 적용 | Azure 크레딧은 적용 되는 자동으로 Azure 비용 각 청구 기간 |
| 소계 |세 전 금액 |
| 세금 |형식 및 청구 프로필의 국가 따라 지불 하는 세금입니다. 세금을 납부 하지 않아도 세금 청구서에 표시 되지 않습니다. |
| 총 절감 비용 예상 |유효 할인에서 저장 하는 예상된 총 공간입니다. 해당 하는 경우 유효 할인 요금이 청구서 섹션으로 구매 품목 세부 정보에서 아래에 나열 됩니다. |

### <a name="invoice-sections"></a>청구서 섹션

청구 프로필에서 각 송장 섹션에 대 한 요금은, 적용 하는 Azure 크레딧의 금액, 세금 및 총 금액 표시 됩니다.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>송장 섹션에서 세부 정보

세부 정보에는 제품을 주문 별로 각 송장 섹션에 대 한 비용을 보여 줍니다. 각 제품 주문 내의 비용 서비스 유형별로 세분화 됩니다. Azure portal 및 Azure 사용 요금 CSV 제품 및 서비스에 대 한 일일 요금을 찾을 수 있습니다. 자세한 내용은 [요금은 청구서에 대 한 Microsoft 고객 계약에 대 한 이해](billing-mca-understand-your-bill.md)합니다.

각 서비스 제품군을 빼서 계산 지불할 총 금액 *크레딧은* 에서 *크레딧/요금* 추가 하 고 *세금*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![송장 섹션에서 세부 정보](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 용어 |설명 |
| --- | --- |
| 단가 | (통화 가격)에 서비스의 효과적인 단가 사용 속도를 사용 합니다. 이 제품, 서비스 제품군, 측정기, 및 제품에 대 한 고유 합니다. |
| Qty | 구입 하거나 청구 기간 동안 사용 수량 |
| 요금/크레딧 | Net 금액 크레딧/환불이 적용 된 후에 요금 |
| Azure 크레딧 | 요금/크레딧을 적용할 Azure 크레딧의 금액|
| 세율 | 국가 따라 rate(s) 세금 |
| 세 액 | 세율을 기준으로 하는 양 구매에 적용 되는 세금 |
| 합계 | 구매에 대 한 금액 합계 |

### <a name="how-to-pay"></a>비용을 지불 하는 방법

청구서의 맨 아래에서 청구서를 지불 하는 것에 대 한 지침이 있습니다. 연결을 통해 확인 하 여 요금을 지불할 수 있습니다 또는 온라인입니다. 온라인 지불 하는 경우에 해당 하는 경우 신용/직불 카드 또는 Azure 크레딧을 사용할 수 있습니다.

### <a name="publisher-information"></a>게시자 정보

타사 서비스를 청구서에 있는 경우 각 게시자의 주소와 이름을 청구서의 맨 아래에 나열 됩니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

- [청구 프로필의 청구서 요금 이해](billing-mca-understand-your-bill.md)
- [Azure 청구서 및 일간 사용 현황 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)
- [조직의 Azure 가격을 확인합니다](billing-ea-pricing.md)
- [청구 프로필에 대 한 세금 문서 보기](billing-mca-download-tax-document.md)

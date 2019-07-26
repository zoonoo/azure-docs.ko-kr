---
title: Azure의 Microsoft 고객 계약 청구서 이해
description: Azure에서 Microsoft 고객 계약 청구서를 읽고 이해 하는 방법 알아보기
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383545"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Microsoft 고객 계약 청구서의 약관

이 문서는 Microsoft 고객 계약에 대 한 Azure 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 대 한 액세스 권한이 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

청구서는 요금 및 지불에 대 한 지침을 요약 하 여 제공 합니다. [Azure Portal](https://portal.azure.com/)에서 .pdf(Portable Document Format)로 다운로드하고 전자 메일을 통해 전송할 수 있습니다. 자세한 내용은 [Microsoft Azure 청구서 보기 및 다운로드](billing-download-azure-invoice.md)를 참조 하세요.

## <a name="billing-period"></a>청구 기간

월 기준으로 송장이 청구 됩니다. [Azure Portal](https://portal.azure.com/)에서 청구 프로필 속성 아래의 *청구서 날짜* 를 확인 하 여 청구서를 받는 월을 확인할 수 있습니다. 청구 기간 및 청구서 날짜 사이에 발생 하는 요금은 다음 달의 청구서에 포함 됩니다 (다음 청구 기간에 포함 됨). 각 송장의 청구 기간 시작 및 종료 날짜가 청구서의 청구서 **요약**에 나열 되어 있습니다.

## <a name="invoice-terms-and-descriptions"></a>송장 용어 및 설명

다음 섹션에서는 청구서에 표시 되는 중요 한 용어를 나열 하 고 각 용어에 대 한 설명을 제공 합니다.

### <a name="invoice-summary"></a>송장 요약

**청구서 요약은** 첫 번째 페이지의 맨 위에 있으며 청구 프로필 및 지불 방법에 대 한 정보를 표시 합니다.

![청구서 요약 섹션](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 용어 | Description |
| --- | --- |
| 구매처 |청구 계정 속성에 있는 법률 엔터티의 주소|
| 청구지 |청구 프로필 속성에 있는 청구서를 받는 청구 프로필의 청구 주소|
| 청구 프로필 |송장을 받는 청구 프로필의 이름 |
| P.O. number |추적을 위해 사용자에 의해 할당된 선택적 구매 주문 번호 |
| 청구서 번호 |추적 목적으로 사용 되는 고유한 Microsoft 생성 송장 번호 |
| 청구서 날짜 |송장이 생성 되는 날짜입니다. 일반적으로 청구 주기가 끝난 후에는 일반적으로 5 ~ 12 일입니다. 청구 프로필 속성에서 청구서 날짜를 확인할 수 있습니다.|
| 지불 조건 |Microsoft 청구서 요금을 지불 하는 방법입니다. *Net 30 일* 은 청구서 날짜의 30 일 이내에 지불 하는 것을 의미 합니다. |

### <a name="billing-summary"></a>청구 요약

**청구 요약은** 이전 청구 기간 이후 청구 프로필에 대 한 요금, 적용 된 크레딧, 세금 및 총 금액을 보여 줍니다.

![청구 요약 섹션](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 용어 | 설명 |
| --- | --- |
| 요금|마지막 청구 기간 이후이 청구 프로필에 대 한 총 Microsoft 요금 수 |
| 인증 |에서 받은 크레딧을 반환 합니다. |
| Azure 크레딧이 적용 됨 | 각 청구 기간에 Azure에 자동으로 적용 되는 azure 크레딧을 청구 합니다. |
| 소계 |만기 전 세 액 |
| 세금 |청구 프로필의 국가/지역에 따라 지불 하는 세금의 유형 및 양입니다. 세금을 지불 하지 않아도 되는 경우 청구서에 세금이 표시 되지 않습니다. |
| 예상 총 절감 액 |유효 할인율에서 저장 한 예상 총 금액입니다. 해당 하는 경우 유효한 할인 요금은 청구서의 세부 정보 섹션에 있는 구매 품목 항목 아래에 나열 됩니다. |

### <a name="invoice-sections"></a>청구서 섹션

청구 프로필 아래의 각 송장 섹션에 대해 요금, 적용 되는 Azure 크레딧의 양, 세금 및 총 금액을 확인할 수 있습니다.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>송장 별 세부 정보 섹션

세부 정보는 각 송장 섹션에 대 한 비용을 제품 주문 별로 구분 하 여 보여 줍니다. 각 제품 주문 내에서 비용은 서비스 유형에 따라 세분화 됩니다. Azure Portal 및 Azure 사용량에서 제품 및 서비스에 대 한 일별 요금을 찾고 CSV에 요금을 청구할 수 있습니다. 자세히 알아보려면 [Microsoft 고객 계약에 대 한 청구서의 요금 이해](billing-mca-understand-your-bill.md)를 참조 하세요.

각 서비스 제품군에 대 한 총 금액은 *크레딧/요금* 에서 *Azure 크레딧을* 빼서 *세금*을 추가 하 여 계산 됩니다.


![송장 별 세부 정보 섹션](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 용어 |설명 |
| --- | --- |
| 단가 | 사용량을 평가 하는 데 사용 되는 서비스의 유효 단가 (가격 책정 통화)입니다. 제품, 서비스 제품군, 미터 및 제안에 대해 고유 합니다. |
| Qty | 청구 기간 동안 구입 또는 소비 된 수량 |
| 요금/크레딧 | 크레딧/환불 적용 후의 순 요금 |
| Azure 크레딧 | 요금/크레딧에 적용 되는 Azure 크레딧의 양|
| 세금 | 국가/지역에 따라 세율 |
| 세금 금액 | 세금을 기준으로 구매에 적용 되는 세금의 양 |
| Total | 구매의 총 금액 |

### <a name="how-to-pay"></a>지불 방법

청구서의 맨 아래에는 청구서 요금을 지불 하기 위한 지침이 있습니다. 확인, 통신 또는 온라인으로 요금을 지불할 수 있습니다. 온라인으로 지불 하는 경우에는 신용 카드나 Azure 크레딧을 사용할 수 있습니다 (해당 하는 경우).

### <a name="publisher-information"></a>게시자 정보

청구서에 타사 서비스가 있는 경우 각 게시자의 이름과 주소가 청구서의 아래쪽에 나열 됩니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [청구 프로필 청구서의 요금 이해](billing-mca-understand-your-bill.md)
- [Azure 청구서 및 일간 사용 현황 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)
- [조직의 Azure 가격 책정 보기](billing-ea-pricing.md)
- [청구 프로필에 대 한 세금 문서 보기](billing-mca-download-tax-document.md)

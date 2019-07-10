---
title: Microsoft 고객 계약의 청구서-Azure 요금 이해
description: 읽기 및 청구서의 요금 이해 하는 방법에 알아봅니다.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490669"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Microsoft 고객 계약 청구서의 요금 이해

청구서의 개별 트랜잭션을 분석 하 여 요금을 이해할 수 있습니다. Microsoft 고객 계약에 대 한 대금 청구 계정, 송장 청구 각 프로필에 대해 매월 생성 됩니다. 청구서는 이전 달의 모든 요금을 포함 합니다. Azure portal에서 청구서를 볼 수 있습니다. 자세한 내용은 [Microsoft 고객 계약에 대 한 송장을 다운로드](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)합니다.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure portal에서 청구서에 대 한 트랜잭션 보기

1. [Azure Portal](https://www.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

    ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 선택 **모든 트랜잭션을** 페이지의 왼쪽에서. 대금 청구 계정, 청구 프로필 또는 송장 섹션을 선택한 다음 선택 해야 하면 액세스를 따라 **모든 트랜잭션을**합니다.

4. 모든 트랜잭션 페이지에는 다음 정보가 표시 됩니다.

    ![청구 트랜잭션 목록을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |열  |정의  |
    |---------|---------|
    |Date     | 트랜잭션의 날짜  |
    |송장 ID     | 트랜잭션이 가져온 요금이 청구 됩니다 청구서에 대 한 식별자입니다. 지원 요청을 신속 하 게 Azure 지원을 통해 지원 요청을 제출 하는 경우 공유 ID |
    |트랜잭션 유형     |  구매, cancel 및 사용량 요금이 같은 트랜잭션 유형  |
    |제품군     | 가상 컴퓨터에 대 한 계산 또는 Azure SQL database 용 데이터베이스와 같은 제품의 범주|
    |제품 sku     | 제품의 인스턴스를 식별 하는 고유 코드 |
    |Amount     |  트랜잭션 크기      |
    |송장 섹션     | 프로필의 청구서의이 섹션에는 트랜잭션 표시 |
    |청구 프로필     | 이 청구 프로필의이 청구서에 트랜잭션이 표시 |

5. 청구서에 대 한 트랜잭션을 필터링 할 송장 ID 검색 합니다.

### <a name="view-transactions-by-invoice-sections"></a>송장 섹션에서 트랜잭션 보기

송장 섹션에는 청구 프로필의 청구서에 대 한 비용을 구성할 수 있습니다. 자세한 내용은 [청구서 섹션 이해](billing-mca-overview.md#invoice-sections)합니다. 송장을 생성 되 면 청구 프로필의 모든 섹션에 대 한 요금이 청구서에 표시 됩니다.

다음 이미지는 샘플 청구서 회계 부서 송장 섹션에 대 한 요금을 보여 줍니다.

![송장 섹션 정보로 세부 정보를 보여 주는 예제 이미지](./media/billing-understand-your-bill-mca/invoicesection-details.png)

청구서 섹션에 대 한 요금을 식별 한 경우 트랜잭션이 요금 이해 하려면 Azure portal에서 볼 수 있습니다.

1. 송장에 대 한 트랜잭션을 보려면 Azure portal에서 모든 트랜잭션 페이지로 이동 합니다. 자세한 내용은 [Azure portal에서 청구서에 대 한 트랜잭션 보기](#view-transactions-for-an-invoice-in-the-azure-portal)합니다.

2. 트랜잭션을 보려면 청구서 섹션 이름으로 필터링 합니다.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>다음 청구서를 예측 하는 요금 보류 중인 검토

Microsoft 고객 계약에 대 한 대금 청구 계정에 요금이 예상 되며 송장이 발급 되는 때까지 보류 중인 것으로 간주 합니다. 요금은 다음 청구서를 예측 하는 Azure 포털에서 보류 중인 볼 수 있습니다. 보류 중인 청구 금액은 추정치 이며 해당 세금을 포함 하지 않습니다. 실제 요금은 다음 청구서에는 보류 중인 청구 달라 집니다.

### <a name="view-summary-of-pending-charges"></a>요금 보류 중인의 요약 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택 합니다. 사용자 액세스에 따라 청구 계정을 선택 해야 합니다. 청구 계정에서 선택 **청구 프로필** 청구 프로필을 선택 합니다.

4. 선택 **요약** 화면 위쪽의 탭 합니다.

5. 요금 섹션 월-날짜 및 지난 달 요금을 표시 합니다.

   ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

월-일 요금은 이번 달에 대 한 보류 중인 요금은 및 청구서는 월에 대 한 생성 되 면 요금이 청구 됩니다. 지난 달에 대 한 송장을 아직 생성 되지 않은 경우 지난 달 요금은 보류 중인 다음 청구서에 표시 됩니다.

### <a name="view-pending-transactions"></a>보류 중인 트랜잭션 보기

요금 보류 중인 식별 하는 경우에 요금에 영향을 주는 개별 트랜잭션을 분석 하 여 요금을 이해할 수 있습니다. 이 시점에서 사용량 보류 중인 모든 트랜잭션 페이지에 요금이 표시 되지 않습니다. Azure 구독 페이지에서 보류 중인 사용 요금을 볼 수 있습니다. 자세한 내용은 참조 하세요. [요금 보류 중 보기](#view-pending-usage-charges)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택 합니다. 사용자 액세스에 따라 청구 계정을 선택 해야 합니다. 청구 계정에서 선택 **청구 프로필** 청구 프로필을 선택 합니다.

4. 선택 **모든 트랜잭션을** 페이지의 왼쪽에서.

5. 검색할 *보류 중인*합니다. 사용 합니다 **Timespan** 현재 또는 지난 달 요금 보류 중 보기를 필터링 합니다.

   ![보류 중인 트랜잭션 목록을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>보류 중인 사용 요금 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 *비용 관리 + 청구*합니다.

   ![비용 관리 + 청구에 대 한 Azure portal 검색을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택 합니다. 사용자 액세스에 따라 청구 계정을 선택 해야 합니다. 청구 계정에서 선택 **청구 프로필** 청구 프로필을 선택 합니다.

4. 선택 **모든 구독** 하나 페이지의 왼쪽입니다.

5. Azure 구독 페이지 청구 프로필에서 현재 및 각 구독에 대 한 지난 달 요금을 표시합니다. 월-일 요금은 이번 달에 대 한 보류 중인 요금은 및 청구서는 월에 대 한 생성 되 면 요금이 청구 됩니다. 지난 달에 대 한 송장을 아직 생성 되지 않은 경우 지난 달 요금은 또한 보류 중입니다.

    ![청구 프로필에 대 한 Azure 구독 목록을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Azure 사용 요금은 분석

사용량 기반 요금을 분석 하는 Azure 사용량 및 요금이 CSV 파일을 사용 합니다. 또는 송장에 대 한 파일을 다운로드할 수 요금 보류 중입니다. 자세한 내용은 [Azure 청구 송장 및 일간 사용 현황 데이터를 받는](billing-download-azure-invoice-daily-usage-date.md)합니다.

### <a name="view-detailed-usage-by-invoice-section"></a>송장 섹션에서 자세한 사용 현황 보기

송장 섹션에 대 한 사용 요금을 조정 하려면 Azure 사용량 및 요금 파일을 필터링 할 수 있습니다.

다음 단계를 안내 회계 부서 송장 섹션에 대 한 계산 요금을 조정 합니다.

![송장 섹션 정보로 세부 정보를 보여 주는 예제 이미지](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 청구서 PDF | Azure 사용량 및 요금이 CSV |
 | --- | --- |
 |회계 부서 |invoiceSectionName |
 |Microsoft Azure 계획 요금 |productOrderName |
 |컴퓨팅 |serviceFamily |

1. 필터를 **invoiceSectionName** CSV 파일에 열 **회계 부서**합니다.
2. 필터를 **productOrderName** CSV 파일에 열 **Microsoft Azure 계획**합니다.
3. 필터를 **serviceFamily** CSV 파일에 열 **Microsoft.Compute**합니다.

![사용법을 보여 줍니다 요금 청구서 섹션에서 필터링 하는 파일을 스크린 샷](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>구독별 자세한 사용 현황 보기

사용 요금은 구독에 대 한 조정 하도록 Azure 사용량 및 요금이 CSV 파일을 필터링 할 수 있습니다. 모든 구독에서 청구 프로필을 참조 하십시오 [사용 요금은 보류 중 보기](#view-pending-usage-charges)합니다.

구독에 대 한 요금을 식별 하는 경우 청구 비용을 분석 하는 Azure 사용량 및 요금이 CSV 파일을 사용 합니다.

다음 이미지는 Azure portal에서 구독 목록을 표시 합니다.

![청구 프로필에 대 한 Azure 구독 목록을 보여 주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

필터를 **subscriptionName** Azure 사용량 및 요금이 CSV 파일에 열 **WA_Subscription** WA_Subscription에 대 한 세부 사용 요금을 볼 수 있습니다.

![사용법을 보여 줍니다 구독 별로 필터링 하는 파일을 청구 하는 스크린 샷](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>청구서를 지불

청구서 지불에 대 한 지침 청구서의 맨 아래에 표시 됩니다. [비용을 지불 하는 방법을 알아봅니다](billing-mca-understand-your-invoice.md#how-to-pay)합니다.

청구서 들으실 수, 하는 경우 Azure portal에서 청구서 페이지에서 지불의 상태를 확인할 수 있습니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

청구서 및 자세한 사용 현황에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure 청구서 및 일간 사용 현황 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft 고객 계약 청구서에서 용어 이해](billing-mca-understand-your-invoice.md)
- [Microsoft 고객 계약 사용 CSV에서 용어 이해](billing-mca-understand-your-usage.md)

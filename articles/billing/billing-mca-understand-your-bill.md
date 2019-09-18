---
title: Microsoft 고객 계약 청구서의 요금 이해 - Azure
description: 청구서의 요금을 읽고 이해하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490669"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Microsoft 고객 계약 청구서의 요금 이해

개별 트랜잭션을 분석하여 청구서의 요금을 이해할 수 있습니다. Microsoft 고객 계약의 청구 계정에서, 매월 청구 프로필마다 청구서가 생성됩니다. 청구서에는 이전 달의 모든 요금이 포함됩니다. Azure Portal에서 청구서를 볼 수 있습니다. 자세한 내용은 [Microsoft 고객 계약 청구서 다운로드](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)를 참조하세요.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure Portal에서 청구서의 트랜잭션 보기

1. [Azure Portal](https://www.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

    ![Cost Management + 청구에 대한 Azure Portal 검색을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다. 액세스 권한에 따라 청구 계정, 청구 프로필 또는 청구서 섹션을 선택한 다음, **모든 트랜잭션**을 선택해야 할 수도 있습니다.

4. [모든 트랜젝션] 페이지에는 다음 정보가 표시됩니다.

    ![청구된 트랜잭션 목록을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |열  |정의  |
    |---------|---------|
    |Date     | 트랜잭션 날짜  |
    |청구서 ID     | 트랜잭션 요금이 청구된 청구서의 식별자입니다. 지원 요청을 신속하게 처리할 수 있도록 지원 요청을 제출할 때 Azure 지원에 ID를 알려주세요. |
    |트랜잭션 유형     |  구매, 취소, 사용 요금 등의 트랜잭션 유형  |
    |제품군     | 가상 머신용 컴퓨팅 또는 Azure SQL용 데이터베이스 같은 제품 범주|
    |제품 SKU     | 제품 인스턴스를 식별하는 고유 코드 |
    |Amount     |  트랜잭션 양      |
    |청구서 섹션     | 청구 프로필 청구서의 이 섹션에 트랜잭션이 표시됩니다. |
    |청구 프로필     | 청구 프로필의 청구서에 트랜잭션이 표시됩니다. |

5. 청구서 ID를 검색하여 해당 청구서의 트랜잭션을 필터링합니다.

### <a name="view-transactions-by-invoice-sections"></a>청구서 섹션별 트랜잭션 보기

청구서 섹션에서는 청구 프로필의 청구서 비용을 구성할 수 있습니다. 자세한 내용은 [청구서 섹션 이해](billing-mca-overview.md#invoice-sections)를 참조하세요. 청구서가 생성되면 청구 프로필의 모든 섹션에 대한 요금이 청구서에 표시됩니다.

다음 이미지는 샘플 청구서의 회계 부서 요금 청구서 섹션을 보여줍니다.

![청구서 섹션 정보를 기준으로 세부 정보를 표시하는 예제 이미지](./media/billing-understand-your-bill-mca/invoicesection-details.png)

청구서 섹션의 요금을 확인한 후에는 Azure Portal에서 트랜잭션을 살펴보며 요금을 이해할 수 있습니다.

1. Azure Portal의 [모든 트랜잭션] 페이지로 이동하여 청구서의 트랜잭션을 볼 수 있습니다. 자세한 내용은 [Azure Portal에서 청구서의 트랜잭션 보기](#view-transactions-for-an-invoice-in-the-azure-portal)를 참조하세요.

2. 트랜잭션을 보려면 청구서 섹션 이름으로 필터링하세요.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>보류 중인 요금을 검토하여 다음 달 청구서 예상

Microsoft 고객 계약의 청구 계정에서, 요금은 청구서가 작성되기 전에는 예상 요금이며 보류 중인 요금으로 간주됩니다. Azure Portal에서 보류 중인 요금을 확인하여 다음 달 청구서를 예상할 수 있습니다. 보류 중인 요금은 예상 요금이며 세금이 포함되지 않은 가격입니다. 다음 달 청구서의 실제 요금은 보류 중인 요금과 다릅니다.

### <a name="view-summary-of-pending-charges"></a>보류 중인 요금의 요약 정보 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Cost Management + 청구에 대한 Azure Portal 검색을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

4. 화면 맨 위에서 **요약** 탭을 선택합니다.

5. 요금 섹션에는 월간 누계 요금과 지난 달 요금이 표시됩니다.

   ![Cost Management + 청구에 대한 Azure Portal 검색을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금이며 다음 달 청구서에 표시됩니다.

### <a name="view-pending-transactions"></a>보류 중인 트랜잭션 보기

보류 중인 요금을 확인할 때 요금에 포함된 개별 트랜잭션을 분석하여 요금을 이해할 수 있습니다. 현재 보류 중인 사용 요금은 [모든 트랜잭션] 페이지에 표시되지 않습니다. Azure 구독 페이지에서 보류 중인 사용 요금을 볼 수 있습니다. 자세한 내용은 [보류 중인 사용 요금 보기](#view-pending-usage-charges)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Cost Management + 청구에 대한 Azure Portal 검색을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

4. 페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다.

5. *보류 중*을 검색합니다. **Timespan** 필터를 사용하여 이번 달 또는 지난 달의 보류 중인 요금을 봅니다.

   ![보류 중인 트랜잭션 목록을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>보류 중인 사용 요금 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. *Cost Management + 청구*를 검색합니다.

   ![Cost Management + 청구에 대한 Azure Portal 검색을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

4. 페이지 왼쪽에서 **모든 구독**을 선택합니다.

5. Azure 구독 페이지에는 청구 프로필의 각 구독에 대한 이번 달과 지난 달 요금이 표시 됩니다. 월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금입니다.

    ![청구 프로필의 Azure 구독 목록을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Azure 사용 요금 분석

Azure 사용량 및 요금 CSV 파일을 사용하여 사용량 기반 요금을 분석합니다. 청구서 또는 보류 중인 요금에 대한 파일을 다운로드할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용량 데이터 받기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요.

### <a name="view-detailed-usage-by-invoice-section"></a>청구서 섹션별로 자세한 사용량 보기

Azure 사용량 및 요금 파일을 필터링하여 청구서 섹션의 사용 요금을 조정할 수 있습니다.

다음은 회계 부서 청구서 섹션의 컴퓨팅 요금을 조정하는 단계입니다.

![청구서 섹션 정보를 기준으로 세부 정보를 표시하는 예제 이미지](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 청구서 PDF | Azure 사용량 및 요금 CSV |
 | --- | --- |
 |회계 부서 |invoiceSectionName |
 |사용 요금 - Microsoft Azure 플랜 |productOrderName |
 |컴퓨팅 |serviceFamily |

1. CSV 파일의 **invoiceSectionName** 열을 **회계 부서**로 필터링합니다.
2. CSV 파일의 **productOrderName** 열을 **Microsoft Azure 플랜**으로 필터링합니다.
3. CSV 파일의 **serviceFamily** 열을 **Microsoft.Compute**로 필터링합니다.

![청구서 섹션을 기준으로 필터링한 사용량 및 요금 파일을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>구독별 세부 사용량 보기

Azure 사용량 및 요금 CSV 파일을 필터링하여 구독의 사용 요금을 조정할 수 있습니다. 청구 프로필의 모든 구독을 보려면 [보류 중인 사용 요금 보기](#view-pending-usage-charges)를 참조하세요.

구독의 요금을 확인할 때 Azure 사용량 및 요금 CSV 파일을 사용하여 요금을 분석하세요.

다음 이미지는 Azure Portal의 구독 목록을 보여줍니다.

![청구 프로필의 Azure 구독 목록을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

WA_Subscription의 자세한 사용 요금을 확인하려면 Azure 사용량 및 요금 CSV 파일의 **subscriptionName** 열을 **WA_Subscription**으로 필터링합니다.

![구독을 기준으로 필터링한 사용량 및 요금 파일을 보여주는 스크린샷](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>청구서 결제

청구서 결제 방법에 대한 지침은 청구서 하단에 표시되어 있습니다. [결제 방법을 알아보세요](billing-mca-understand-your-invoice.md#how-to-pay).

이미 청구서를 결제한 경우 Azure Portal의 청구서 페이지에서 결제 상태를 확인할 수 있습니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 권한 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

청구서 및 세부 사용량에 대해 자세히 알아보세요.

- [Azure 청구서 및 일간 사용 현황 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft 고객 계약 청구서의 용어 이해](billing-mca-understand-your-invoice.md)
- [Microsoft 고객 계약 사용량 CSV 파일의 용어 이해](billing-mca-understand-your-usage.md)

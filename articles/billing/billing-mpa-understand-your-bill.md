---
title: Microsoft 파트너 계약 청구서의 요금 이해 - Azure
description: 청구서의 요금을 읽고 이해하는 방법에 대해 알아봅니다.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 579a052f8bde780ac33de85c5a08d9b3e79d3096
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515774"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Microsoft 파트너 계약 청구서의 요금 이해

 Microsoft 파트너 계약의 청구 계정에서, 매월 청구 프로필마다 청구서가 생성됩니다. 청구서에는 이전 달의 모든 고객 요금이 포함됩니다. Azure Portal에서 개별 트랜잭션을 분석하여 청구서의 요금을 이해할 수 있습니다. Azure Portal에서 청구서를 볼 수도 있습니다. 자세한 내용은 [Azure Portal에서 청구서를 다운로드하는 방법](billing-download-azure-invoice.md)을 참조하세요.

이 문서는 Microsoft 파트너 계약에 대한 청구 계정에 적용됩니다. [Microsoft 파트너 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure Portal에서 청구서의 트랜잭션 보기

1. [Azure Portal](https://www.azure.com)에 로그인합니다.

2. *Cost Management + 청구*를 검색합니다.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. 페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다. 액세스 권한에 따라 청구 계정, 청구 프로필 또는 고객을 선택한 다음, **모든 트랜잭션**을 선택해야 할 수도 있습니다.

4. [모든 트랜젝션] 페이지에는 다음 정보가 표시됩니다.

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |열  |정의  |
    |---------|---------|
    |Date     | 트랜잭션 날짜  |
    |청구서 ID     | 트랜잭션 요금이 청구된 청구서의 식별자입니다. 지원 요청을 신속하게 처리할 수 있도록 지원 요청을 제출할 때 Azure 지원에 ID를 알려주세요. |
    |트랜잭션 유형     |  구매, 취소, 사용 요금 등의 트랜잭션 유형  |
    |제품군     | 가상 머신용 컴퓨팅 또는 Azure SQL용 데이터베이스 같은 제품 범주|
    |제품 SKU     | 제품 인스턴스를 식별하는 고유 코드 |
    |Amount     |  트랜잭션 양      |
    |청구 프로필     | 청구 프로필의 청구서에 트랜잭션이 표시됩니다. |

5. 청구서 ID를 검색하여 해당 청구서의 트랜잭션을 필터링합니다.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>보류 중인 요금을 검토하여 다음 달 청구서 예상

요금은 예상 요금이며 청구서가 청구될 때까지 보류 중인 것으로 간주됩니다. Azure Portal에서 Microsoft 파트너 계약 청구 프로필에 대한 보류 중인 요금을 확인하여 다음 청구서를 예측할 수 있습니다. 보류 중인 요금은 예상 요금이며 세금이 포함되지 않은 가격입니다. 다음 달 청구서의 실제 요금은 보류 중인 요금과 다릅니다.

### <a name="view-pending-transactions"></a>보류 중인 트랜잭션 보기

보류 중인 요금을 확인할 때 요금에 포함된 개별 트랜잭션을 분석하여 요금을 이해할 수 있습니다. 현재 보류 중인 사용 요금은 [모든 트랜잭션] 페이지에 표시되지 않습니다. Azure 구독 페이지에서 보류 중인 사용 요금을 볼 수 있습니다. 자세한 내용은 [보류 중인 사용 요금 보기](#view-pending-usage-charges)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. *Cost Management + 청구*를 검색합니다.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

4. 페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다.

5. *보류 중*을 검색합니다. **Timespan** 필터를 사용하여 이번 달 또는 지난 달의 보류 중인 요금을 봅니다.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>고객별 보류 중인 요금 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. *Cost Management + 청구*를 검색합니다.

3. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

4. 페이지 왼쪽에서 **고객**을 선택합니다.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. 고객 페이지에는 청구 프로필과 연결된 각 고객에 대한 이번 달과 지난 달 요금이 표시됩니다. 월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금입니다.

### <a name="view-pending-usage-charges"></a>보류 중인 사용 요금 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. *Cost Management + 청구*를 검색합니다.

3. 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

4. 페이지 왼쪽에서 **Azure 구독**을 선택합니다.

5. Azure 구독 페이지에는 청구 프로필의 각 구독에 대한 이번 달과 지난 달 요금이 표시 됩니다. 월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금입니다.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Azure 사용 요금 분석

Azure 사용량 및 요금 CSV 파일을 사용하여 사용량 기반 요금을 분석합니다. [Azure Portal에서 Azure 사용량 및 요금 CSV를 다운로드](billing-download-azure-daily-usage.md)할 수 있습니다.

Azure 사용량 및 요금 파일을 필터링하여 청구서 pdf에 나열된 각 제품에 대한 사용량 요금을 조정할 수 있습니다. 특정 제품에 대한 자세한 사용량 요금을 보려면 Azure 사용량 및 요금 CSV 파일의 **제품** 열을 필터링하여 해당 제품의 이름만 포함하도록 합니다.

Azure 사용량 및 요금 CSV 파일에서 **customerName** 열을 필터링하여 각 고객에 대한 일일 사용량 요금을 볼 수도 있습니다. Azure 구독별 일일 사용량 요금을 확인하려면 **subscriptionName** 열을 필터링합니다.


## <a name="pay-your-bill"></a>청구서 결제

청구서 결제 방법에 대한 지침은 청구서 하단에 표시되어 있습니다. 청구서 날짜로부터 60일 이내에 회선 또는 수표로 요금을 지불할 수 있습니다.

이미 청구서를 결제한 경우 Azure Portal의 청구서 페이지에서 결제 상태를 확인할 수 있습니다.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft 파트너 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

청구서 및 세부 사용량에 대해 자세히 알아보세요.

---
title: 비용을 구성하도록 청구서에 섹션 만들기 - Azure
description: 청구서 섹션으로 비용을 구성하는 방법을 알아봅니다.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490755"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>비용을 구성하도록 청구서에 섹션 만들기

부서, 개발 환경 또는 조직의 요구에 따라 비용을 구성하도록 청구서에 섹션을 만듭니다. 그런 다음, 다른 사용자에게 섹션에 청구되는 Azure 구독을 만들 수 있는 권한을 부여합니다. 그러면 구독에 대한 모든 사용량 요금 및 구매는 섹션에 청구됩니다. Azure Portal의 청구서에서 섹션에 대한 총 요금을 확인하거나 Azure 비용 분석에서 검토할 수 있습니다. 자세한 내용은 [청구서 섹션에서 트랜잭션 보기](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)를 참조하세요.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인합니다](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Azure Portal에서 청구서 섹션 만들기

청구서 섹션을 만들려면 **청구 프로필 소유자**이거나 **청구 프로필 기여자**여야 합니다. 자세한 내용은 [청구 프로필에 대한 청구서 섹션 관리](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. 왼쪽 창에서 **청구서 섹션**을 선택합니다. 액세스 권한에 따라 청구 프로필 또는 청구 계정을 선택한 다음, **청구서 섹션**을 선택해야 할 수도 있습니다.

   ![청구서 섹션 목록을 보여 주는 스크린샷](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. 페이지 맨 위에서 **추가**를 선택합니다.

5. 청구서 섹션의 이름을 입력하고 청구 프로필을 선택합니다. 각 구독의 사용량과 섹션에 할당한 구매를 반영하는 섹션이 청구 프로필의 청구서에 표시됩니다. 

   ![청구서 섹션 만들기 페이지를 보여 주는 스크린샷](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. **만들기**를 선택합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대한 추가 Azure 구독 만들기](billing-mca-create-subscription.md)
- [다른 사용자에게 Azure 구독을 만들 수 있는 권한 부여](billing-mca-create-subscription.md#give-others-permission)
- [다른 청구 계정의 사용자에서 Azure 구독의 청구 소유권 얻기](billing-mca-request-billing-ownership.md)

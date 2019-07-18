---
title: 비용-Azure를 구성 하 여 청구서에서 섹션 만들기
description: 송장 섹션을 사용 하 여 비용을 구성 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490755"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>비용을 구성 하려면 청구서에 섹션을 만들려면

개발 환경에는 부서별로 비용을 구성 하 여 청구서에 섹션을 만들거나 조직의 요구에 따라 합니다. 다른 섹션으로 청구 되는 Azure 구독을 만드는 권한을 부여 합니다. 모든 사용 요금 및 구독에 대 한 구매 다음 섹션에 청구 됩니다. Azure portal에서 청구서에는 섹션에 대 한 총 요금을 볼 수도 있고 검토 하 여 Azure 비용 분석 수 있습니다. 자세한 내용은 [청구서 섹션에서 트랜잭션을 보고](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)합니다.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Azure portal에서 청구서 섹션을 만들려면

송장 섹션을 만들려고 해야는 **청구 프로필 소유자** 또는 **청구 프로필 참가자**합니다. 자세한 내용은 [청구 프로필에 대 한 청구서 섹션 관리](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile)합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. 선택 **섹션에서는 송장** 왼쪽 창에서. 사용자 액세스에 따라 청구 프로필 또는 대금 청구 계정을 선택한 다음 선택 해야 할 수 있습니다 **섹션에서는 송장**합니다.

   ![송장 섹션 목록을 보여 주는 스크린샷](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. 페이지 맨 위에서 **추가**를 선택합니다.

5. 송장 섹션에 대 한 이름을 입력 하 고 청구 프로필을 선택 합니다. 섹션을 각 구독 및 섹션에 할당 한 구매의 사용량을 반영 하는이 청구 프로필의이 청구서에 표시 됩니다. 

   ![송장 섹션 만들기 페이지를 보여 주는 스크린샷](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. **만들기**를 선택합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대 한 추가 Azure 구독 만들기](billing-mca-create-subscription.md)
- [다른 Azure 구독을 만들 수 있는 권한이 제공](billing-mca-create-subscription.md#give-others-permission)
- [다른 청구 계정에 사용자의 Azure 구독 청구 소유권 가져오기](billing-mca-request-billing-ownership.md)

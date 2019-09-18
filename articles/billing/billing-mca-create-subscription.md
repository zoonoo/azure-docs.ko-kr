---
title: 청구 계정에 대한 추가 Azure 구독 만들기
description: Azure Portal에서 새 Azure 구독을 추가하는 방법을 알아봅니다.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490924"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 추가 Azure 구독 만들기

청구 계정에 대한 추가 구독을 만들어 개발 및 테스트, 보안을 위한 별도의 환경을 설정하거나 규정 준수 상의 이유로 데이터를 격리합니다.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인합니다](#check-access). 다른 유형의 청구 계정에 대한 구독을 만들려는 경우 [Azure Portal에서 추가 구독 만들기](billing-create-subscription.md)를 참조하세요.

구독을 만들려면 **청구서 섹션 소유자**, **청구서 섹션 기여자** 또는 **Azure 구독 작성자**여야 합니다. 자세한 내용은 [구독 청구 역할 및 작업](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요. 다른 사용자에게 청구 계정에 대한 Azure 구독을 만들 수 있는 권한을 제공하려면 [다른 사용자에게 Azure 구독을 만들 수 있는 권한 부여](#give-others-permission)를 참조하세요.

## <a name="create-a-subscription"></a>구독 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **구독**을 검색합니다.

   ![포털의 구독 검색을 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. **추가**를 선택합니다.

4. 여러 청구 계정에 액세스할 수 있는 경우 Microsoft 고객 계약에 대한 청구 계정을 선택합니다.

   ![구독 만들기 페이지를 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 청구 프로필을 선택합니다. 구독에 대한 요금은 선택한 청구 프로필로 청구됩니다. 하나의 청구 프로필에만 액세스할 수 있는 경우 선택 영역이 회색으로 표시됩니다.

6. 청구서 섹션을 선택합니다. 구독에 대한 요금은 청구 프로필 청구서의 이 섹션으로 청구됩니다. 하나의 청구서 섹션에만 액세스할 수 있는 경우 선택 영역이 회색으로 표시됩니다.

7. 구독에 대한 계획을 선택합니다. **DevTest용 Microsoft Azure 플랜**을 선택하고 개발 또는 테스트 작업에 이 구독을 사용하려는 경우 **Microsoft Azure 플랜**을 사용합니다. 하나의 플랜에만 액세스할 수 있는 경우 선택 영역이 회색으로 표시됩니다.

8. 구독의 이름을 입력합니다. 이 이름을 사용하면 Azure Portal에서 구독을 쉽게 식별할 수 있습니다.

9. **만들기**를 선택합니다.

## <a name="give-others-permission"></a>다른 사용자에게 권한 부여

청구서 섹션에 사용자를 Azure 구독 작성자로 추가하여 Azure 구독을 만들 수 있는 권한을 부여합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![포털의 구독 검색을 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 청구서 섹션으로 이동합니다. 액세스 권한에 따라 청구 계정 또는 청구 프로필을 선택해야 할 수도 있습니다. 청구 계정 또는 프로필에서 **청구서 섹션**을 선택한 다음, 목록에서 청구서 섹션을 선택합니다. 사용자가 생성하는 모든 구독은 이 청구서 섹션으로 청구됩니다.
   
   ![청구서 섹션 선택을 보여 주는 스크린샷](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. 왼쪽 맨 위에서 **액세스 관리(IAM)** 를 선택합니다.

5. 페이지 맨 위에서 **추가**를 선택합니다.

6. 역할에 대한 **Azure 구독 작성자**를 선택합니다.

7. 액세스 권한을 부여하려는 사용자의 이메일 주소를 입력합니다.

8. **저장**을 선택합니다.

## <a name="check-access"></a>액세스 권한 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [기본 제공 역할을 사용하여 다른 사용자에게 Azure 리소스를 만들 수 있는 권한 부여](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)
- [Linux 가상 머신 만들기](../virtual-machines/linux/quick-create-portal.md)
- [리소스 조직 및 관리를 위한 관리 그룹 만들기](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)

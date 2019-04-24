---
title: 대금 청구 계정에 대 한 추가 Azure 구독 만들기 | Microsoft Docs
description: Azure Portal에서 새 Azure 구독을 추가하는 방법을 알아봅니다.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372025"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 추가 Azure 구독 만들기

개발 및 테스트를 보안에 대 한 별도 환경을 설정 하려면 또는 규정 준수 상의 이유로 데이터를 분리 하도록 대금 청구 계정에 대 한 추가 구독을 만듭니다.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다. 다른 청구 계정에 대 한 구독을 만들려는 경우 참조 [Azure portal에서 구독을 더 만들](billing-create-subscription.md)합니다.

구독을 만들 수 있습니다는 **청구서 섹션 소유자**를 **송장 섹션 참가자**, 또는 **Azure 구독 작성자**합니다. 자세한 내용은 [구독 청구 역할 및 태스크](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)합니다. 참조를 제공 하려면 다른 대금 청구 계정에 대 한 Azure 구독을 만들 수 있는 권한이 [다른 사용자가 Azure 구독을 만들 수 있는 권한이 있도록](#give-others-permission-to-create-azure-subscriptions)입니다.

## <a name="create-a-subscription-in-the-azure-portal"></a>Azure portal에서 구독 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **구독**을 검색합니다.

   ![구독에 대 한 포털에서 검색을 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. **추가**를 선택합니다.

4. 여러 청구 계정에 액세스할 수 있으면 Microsoft 고객 계약에 대 한 청구 계정을 선택 합니다.

   ![구독 페이지를 만들기를 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 청구 프로필을 선택 합니다. 구독에 대 한 요금이 청구 프로필의 청구서에 반영 하 고 해당 결제 방법을 사용 하 여 지급 됩니다. 하나의 청구 프로필에 액세스할 수 있으면 선택 회색으로 표시 됩니다.

6. 송장 섹션을 선택 합니다. 청구 프로필의 청구서의이 섹션에서 구독에 대 한 요금이 반영 됩니다. 하나의 송장 섹션에 액세스할 수 있으면 선택 회색으로 표시 됩니다.

7. 구독에 대 한 계획을 선택 합니다. 선택 **DevTest에 대 한 Microsoft Azure 플랜**개발에 대 한이 구독을 사용 하려는 경우 다른 테스트 작업 사용 **Microsoft Azure 플랜**합니다. 하나의 계획에 액세스할 수 있으면 선택 회색으로 표시 됩니다.

8. 구독의 이름을 입력합니다. 이름을 사용 하면 Azure portal에서 구독을 쉽게 식별할 수 있습니다.

9. **만들기**를 선택합니다.

## <a name="give-others-permission-to-create-azure-subscriptions"></a>다른 Azure 구독을 만들 수 있는 권한이 제공

Azure 구독을 만들 수 있는 권한을 부여할 수는 청구서 섹션에서 Azure 구독 작성자로 사용자를 추가 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**에서 검색합니다.

   ![구독에 대 한 포털에서 검색을 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 청구서 섹션으로 이동합니다. 사용자 액세스에 따라 대금 청구 계정 또는 청구 프로필을 선택 해야 합니다. 대금 청구 계정 또는 프로필에서 선택 **섹션에서는 송장** 와 다음 송장 부분을 지정 합니다.

4. 선택 **액세스 관리 (IAM)** 위 왼쪽에서 합니다.

5. 페이지 맨 위에서 **추가**를 선택합니다.

6. 선택 **Azure 구독 작성자** 역할에 대 한 합니다.

   ![Azure 구독 작성자 역할을 사용자에 게 보여 주는 스크린샷](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. 액세스 권한을 부여 하려는 사용자의 이메일 주소를 입력 합니다.

8. **저장**을 선택합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

- [다른 사용자가 기본 제공 역할을 사용 하 여 Azure 리소스를 만들 수 있는 권한이 있도록](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)
- [Linux 가상 머신 만들기](../virtual-machines/linux/quick-create-portal.md)
- [리소스 조직 및 관리를 위한 관리 그룹 만들기](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)

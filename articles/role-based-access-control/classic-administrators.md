---
title: Azure 클래식 구독 관리자
description: Azure 공동 관리자 및 서비스 관리자 역할을 추가하거나 변경하는 방법과 계정 관리자를 확인하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 935c0bf76e298ab1e2e4d25fab8685ac4ce617db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95916168"
---
# <a name="azure-classic-subscription-administrators"></a>Azure 클래식 구독 관리자

Azure 역할 기반 액세스 제어 (Azure RBAC)를 사용 하 여 Azure 리소스에 대 한 액세스를 관리 하는 것이 좋습니다. 그러나 클래식 배포 모델을 계속 사용 하는 경우에는 클래식 구독 관리자 역할 (서비스 관리자 및 공동 관리자)을 사용 해야 합니다. 자세한 내용은 [Azure Resource Manager 및 클래식 배포](../azure-resource-manager/management/deployment-models.md)를 참조 하세요.

이 문서에서는 공동 관리자 및 서비스 관리자 역할을 추가하거나 변경하는 방법과 계정 관리자를 확인하는 방법을 설명합니다.

## <a name="add-a-co-administrator"></a>공동 관리자 추가

> [!TIP]
> 사용자가 [Azure 서비스 관리 PowerShell 모듈](/powershell/module/servicemanagement/azure.service)을 사용하여 Azure 클래식 배포를 관리해야 하는 경우에만 공동 관리자를 추가하면 됩니다. 사용자가 Azure Portal을 통해서만 클래식 리소스를 관리하는 경우 사용자에 대한 클래식 관리자를 추가할 필요가 없습니다.

1. 서비스 관리자 또는 공동 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 열고 구독을 선택합니다.

    공동 관리자는 구독 범위에서만 할당할 수 있습니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **클래식 관리자** 탭을 클릭합니다.

    ![클래식 관리자를 여는 스크린샷](./media/classic-administrators/classic-administrators.png)

1. **Add**  >  **공동 관리자** 추가 추가를 클릭 하 여 공동 관리자 추가 창을 엽니다.

    공동 관리자 추가 옵션을 사용할 수 없는 경우 권한이 없는 것입니다.

1. 추가 하려는 사용자를 선택 하 고 **추가** 를 클릭 합니다.

    ![공동 관리자를 추가하는 스크린샷](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>게스트 사용자를 Co-Administrator으로 추가

게스트 사용자를 공동 관리자로 추가 하려면 이전 [공동 관리자 추가](#add-a-co-administrator) 섹션과 동일한 단계를 수행 합니다. 게스트 사용자는 다음 조건을 충족 해야 합니다.

- 게스트 사용자는 디렉터리에 있는 상태 여야 합니다. 즉, 사용자가 디렉터리에 초대 되 고 초대를 수락 했음을 의미 합니다.

디렉터리에 게스트 사용자를 추가 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 B2B 공동 작업 사용자 추가 Azure Active Directory](../active-directory/external-identities/add-users-administrator.md)를 참조 하세요.

### <a name="differences-for-guest-users"></a>게스트 사용자의 차이점

Co-Administrator 역할이 할당 된 게스트 사용자에 게는 Co-Administrator 역할의 멤버 사용자에 비해 몇 가지 차이점이 표시 될 수 있습니다. 다음 시나리오를 고려하세요.

- Azure AD 계정 (회사 또는 학교 계정)을 사용 하는 사용자 A는 Azure 구독에 대 한 서비스 관리자입니다.
- 사용자 B에게는 Microsoft 계정이 있습니다.
- 사용자 A가 사용자 B에게 공동 관리자 역할을 할당합니다.
- 사용자 B는 거의 모든 작업을 수행할 수 있지만 Azure AD 디렉터리에서 애플리케이션을 등록하거나 사용자를 조회할 수 없습니다.

사용자 B는 모든 것을 관리할 수 있다고 예상할 수 있습니다. 이러한 차이가 나타나는 이유는 Microsoft 계정이 멤버 사용자가 아닌 게스트 사용자로 구독에 추가되기 때문입니다. 게스트 사용자에 멤버 사용자와 비교할 때 Azure AD에서 다른 기본 권한을 갖습니다. 예를 들어, 멤버 사용자는 Azure AD에서 다른 사용자가 읽을 수 있지만 게스트 사용자는 그럴 수 없습니다. 멤버 사용자는 Azure AD에서 새 서비스 주체를 등록할 수 있지만 게스트 사용자는 그럴 수 없습니다.

게스트 사용자가 이러한 작업을 수행할 수 있어야 하는 경우 가능한 해결 방법은 게스트 사용자에 게 필요한 특정 Azure AD 역할을 할당 하는 것입니다. 예를 들어, 이전 시나리오에서는 다른 사용자를 읽기 위한 [디렉터리 읽기 권한자](../active-directory/roles/permissions-reference.md#directory-readers) 역할과 서비스 주체를 만들 수 있는 [애플리케이션 개발자](../active-directory/roles/permissions-reference.md#application-developer) 역할을 할당할 수 있습니다. 멤버 및 게스트 사용자와 해당 권한에 대한 자세한 내용은 [Azure Active Directory의 기본 사용자 권한이란?](../active-directory/fundamentals/users-default-permissions.md)을 참조하세요. 게스트 사용자에 게 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 외부 게스트 사용자에 대 한 Azure 역할 할당 추가 또는 제거](role-assignments-external-users.md)를 참조 하세요.

[Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md) 은 [azure AD 역할과](../active-directory/roles/permissions-reference.md)다릅니다. 기본 제공 역할은 Azure AD에 대한 액세스 권한을 부여하지 않습니다. 자세한 내용은 [다른 역할 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)를 참조하세요.

멤버 사용자와 게스트 사용자를 비교 하는 내용은 [Azure Active Directory?의 기본 사용자 권한은 무엇입니까?](../active-directory/fundamentals/users-default-permissions.md)를 참조 하십시오.

## <a name="remove-a-co-administrator"></a>공동 관리자 제거

1. 서비스 관리자 또는 공동 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 열고 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **클래식 관리자** 탭을 클릭합니다.

1. 제거할 공동 관리자 옆에 확인 표시를 추가합니다.

1. **제거** 를 클릭합니다.

1. 표시되는 메시지 상자에서 **예** 를 클릭합니다.

    ![공동 관리자를 제거하는 스크린샷](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>서비스 관리자 변경

계정 관리자만 구독에 대한 서비스 관리자를 변경할 수 있습니다. 기본적으로, Azure 구독에 가입할 때 서비스 관리자는 계정 관리자와 같습니다. 계정 관리자 역할의 사용자는 Azure Portal에 액세스할 수 없습니다. 서비스 관리자 역할의 사용자는 Azure Portal에 대한 모든 권한이 있습니다. 계정 관리자 및 서비스 관리자가 동일한 사용자이고 서비스 관리자를 다른 사용자로 변경하는 경우 계정 관리자는 Azure Portal에 액세스할 수 없게 됩니다. 그러나 계정 관리자는 계정 센터를 사용하여 언제든지 서비스 관리자를 다시 자신으로 변경할 수 있습니다.

Azure Portal에서 서비스 관리자를 변경 하려면 다음 단계를 수행 합니다.

1. [서비스 관리자 변경에 대 한 제한 사항을](#limitations-for-changing-the-service-administrator)확인 하 여 시나리오가 지원 되는지 확인 합니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 열고 구독을 선택합니다.

1. 왼쪽 탐색 영역에서 **속성** 을 클릭 합니다.

1. **서비스 관리자** 를 클릭 합니다.

    ![Azure Portal 구독 속성을 보여 주는 스크린샷](./media/classic-administrators/service-admin.png)

1. **서비스 관리 편집** 페이지에서 새 서비스 관리자의 전자 메일 주소를 입력 합니다.

    ![서비스 관리 편집 페이지를 보여 주는 스크린샷](./media/classic-administrators/service-admin-edit.png)

1. **확인** 을 클릭하여 변경 내용을 저장합니다.

### <a name="limitations-for-changing-the-service-administrator"></a>서비스 관리자 변경에 대한 제한 사항

Azure 구독 당 서비스 관리자는 하나만 있을 수 있습니다. 서비스 관리자를 변경 하는 것은 계정 관리자가 Microsoft 계정 인지 아니면 Azure AD 계정 (회사 또는 학교 계정) 인지에 따라 다르게 동작 합니다.

| 계정 관리자 계정 | 다른 Microsoft 계정로 서비스 관리자를 변경할 수 있나요? | 동일한 디렉터리의 Azure AD 계정에 대 한 서비스 관리자를 변경할 수 있나요? | 다른 디렉터리의 Azure AD 계정에 대 한 서비스 관리자를 변경할 수 있나요? |
| --- | --- | --- | --- |
| Microsoft 계정 | 예 | 예 | 예 |
| Azure AD 계정 | 예 | 예 | 예 |

계정 관리자가 Azure AD 계정인 경우에는 서비스 관리자를 같은 디렉터리의 Azure AD 계정으로 변경할 수 있지만 다른 디렉터리에는 변경할 수 없습니다. 예를 들어는 abby@contoso.com 서비스 관리자를로 변경할 수 bob@contoso.com 있지만 john@notcontoso.com john@notcontoso.com contoso.com 디렉터리에가 없는 경우에는로 서비스 관리자를 변경할 수 없습니다.

Microsoft 계정 및 Azure AD 계정에 대 한 자세한 내용은 [Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)을 참조 하세요.

## <a name="view-the-account-administrator"></a>계정 관리자 보기

계정 관리자는 초기에 Azure 구독에 등록한 사용자이며 구독의 청구 소유자로서 책임이 있습니다. 구독의 계정 관리자를 변경 하려면 [다른 계정에 Azure 구독의 소유권 이전](../cost-management-billing/manage/billing-subscription-transfer.md)을 참조 하세요.

계정 관리자를 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 열고 구독을 선택합니다.

1. 왼쪽 탐색 영역에서 **속성** 을 클릭 합니다.

    구독 계정 관리자는 **계정** 관리자 상자에 표시 됩니다.

    ![계정 관리자를 보여 주는 스크린샷](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>다음 단계

* [여러 역할의 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)
* [Azure 구독 관리자 추가 또는 변경](../cost-management-billing/manage/add-change-subscription-administrator.md)
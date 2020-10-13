---
title: Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거-Azure RBAC
description: Azure Portal 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4e5c13c635091988f299d31c67795916e709d51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597616"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 이 문서에서는 Azure Portal를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

Azure Active Directory에서 관리자 역할을 할당 해야 하는 경우 [Azure Active Directory에서 관리자 역할 보기 및 할당](../active-directory/users-groups-roles/directory-manage-roles-portal.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

역할 할당을 추가 하거나 제거 하려면 다음을 수행 해야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="access-control-iam"></a>액세스 제어(IAM)

**액세스 제어 (IAM)** 는 Azure 리소스에 대 한 액세스 권한을 부여 하기 위해 일반적으로 역할을 할당 하는 데 사용 하는 페이지입니다. Id 및 액세스 관리 라고도 하며 Azure Portal의 여러 위치에 표시 됩니다. 다음에서는 구독에 대 한 액세스 제어 (IAM) 페이지의 예를 보여 줍니다.

![구독의 액세스 제어 (IAM) 페이지](./media/role-assignments-portal/access-control-subscription.png)

액세스 제어 (IAM) 페이지에서 가장 효과적으로 사용 하려면 다음 단계를 수행 하 여 역할을 할당 하는 것이 좋습니다.

1. 액세스 권한이 필요한 사용자를 결정 합니다. 사용자, 그룹, 서비스 주체 또는 관리 id에 역할을 할당할 수 있습니다.

1. 적절 한 역할을 찾습니다. 사용 권한은 역할에 함께 그룹화 됩니다. 여러 [Azure 기본 제공 역할](built-in-roles.md) 목록에서 선택 하거나 고유한 사용자 지정 역할을 사용할 수 있습니다.

1. 필요한 범위를 식별 합니다. Azure는 [관리 그룹](../governance/management-groups/overview.md), 구독, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups)및 리소스의 네 가지 범위를 제공 합니다. 범위에 대한 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

1. 다음 섹션 중 하나의 단계를 수행 하 여 역할을 할당 합니다.

## <a name="add-a-role-assignment"></a>역할 할당 추가

Azure RBAC에서 Azure 리소스에 대 한 액세스 권한을 부여 하려면 역할 할당을 추가 합니다. 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭 하 고 액세스 권한을 부여할 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 해당 범위에 대 한 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. 이 범위에서 역할 할당을 보려면 **역할 할당** 탭을 클릭 합니다.

    ![액세스 제어 (IAM) 및 역할 할당 탭](./media/role-assignments-portal/role-assignments.png)

1. **추가**  >  **역할 할당**추가를 클릭 합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![역할 할당 추가 메뉴](./media/shared/add-role-assignment-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹, 서비스 주체 또는 관리 ID르 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

    ![역할 할당 추가 저장 됨](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>구독의 관리자로 사용자 할당

특정 사용자를 Azure 구독의 관리자로 지정하려면 해당 사용자에게 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당합니다. 소유자 역할은 사용자에 게 다른 사용자에 게 액세스 권한을 부여할 수 있는 권한을 포함 하 여 구독에 있는 모든 리소스에 대 한 모든 권한을 부여 합니다. 이러한 단계는 다른 역할 할당과 동일합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 액세스 권한을 부여하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭 하 여이 구독에 대 한 역할 할당을 확인 합니다.

    ![액세스 제어 (IAM) 및 역할 할당 탭](./media/role-assignments-portal/role-assignments.png)

1. **추가**  >  **역할 할당**추가를 클릭 합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![구독에 대 한 역할 할당 메뉴 추가](./media/shared/add-role-assignment-menu.png)

    역할 할당 추가 창이 열립니다.

   ![구독에 대 한 역할 할당 창 추가](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **소유자** 역할을 선택합니다.

1. **선택** 목록에서 사용자를 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 직접 입력하여 표시 이름 및 이메일 주소에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 사용자에게는 구독 범위의 소유자 역할이 할당됩니다.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>관리 id에 대 한 역할 할당 추가 (미리 보기)

이 문서의 앞부분에서 설명한 대로 **액세스 제어 (IAM)** 페이지를 사용 하 여 관리 되는 id에 대 한 역할 할당을 추가할 수 있습니다. 액세스 제어 (IAM) 페이지를 사용 하는 경우 범위에서 시작 하 고 관리 되는 id 및 역할을 선택 합니다. 이 섹션에서는 관리 되는 id에 대 한 역할 할당을 추가 하는 다른 방법을 설명 합니다. 이러한 단계를 사용 하 여 관리 되는 id로 시작한 다음 범위 및 역할을 선택 합니다.

> [!IMPORTANT]
> 이러한 대체 단계를 사용 하 여 관리 되는 id에 대 한 역할 할당 추가는 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

관리 id로 시작 하 여 시스템 할당 관리 id에 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 시스템 할당 관리 id를 엽니다.

1. 왼쪽 메뉴에서 **id**를 클릭 합니다.

    ![시스템 할당 관리 ID](./media/shared/identity-system-assigned.png)

1. **사용 권한**아래에서 **Azure 역할 할당**을 클릭 합니다.

    선택한 시스템 할당 관리 id에 역할이 이미 할당 된 경우 역할 할당 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![시스템 할당 관리 id에 대 한 역할 할당](./media/shared/role-assignments-system-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

1. **역할 할당 추가 (미리 보기)** 를 클릭 합니다.

1. 드롭다운 목록을 사용 하 여 **구독**, **리소스 그룹**또는 리소스와 같은 역할 할당이 적용 되는 리소스 집합을 선택할 수 있습니다.

    선택한 범위에 대 한 역할 할당 쓰기 권한이 없는 경우 인라인 메시지가 표시 됩니다. 

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

   ![시스템 할당 관리 id에 대 한 역할 할당 창 추가](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분 후에 관리 되는 id에는 선택한 범위에서 역할이 할당 됩니다.

### <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

관리 id로 시작 하 여 사용자 할당 관리 id에 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 사용자 할당 관리 id를 엽니다.

1. 왼쪽 메뉴에서 **Azure 역할 할당**을 클릭 합니다.

    선택한 사용자 할당 관리 id에 역할이 이미 할당 된 경우 역할 할당 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![사용자 할당 관리 id에 대 한 역할 할당](./media/shared/role-assignments-user-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

1. **역할 할당 추가 (미리 보기)** 를 클릭 합니다.

1. 드롭다운 목록을 사용 하 여 **구독**, **리소스 그룹**또는 리소스와 같은 역할 할당이 적용 되는 리소스 집합을 선택할 수 있습니다.

    선택한 범위에 대 한 역할 할당 쓰기 권한이 없는 경우 인라인 메시지가 표시 됩니다. 

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

   ![사용자 할당 관리 id에 대 한 역할 할당 창 추가](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분 후에 관리 되는 id에는 선택한 범위에서 역할이 할당 됩니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

Azure RBAC에서 Azure 리소스에 대 한 액세스 권한을 제거 하려면 역할 할당을 제거 합니다. 역할 할당을 제거 하려면 다음 단계를 수행 합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![제거 하도록 선택한 역할 할당](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **제거**를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 클릭합니다.

    상속 된 역할 할당을 제거할 수 없다는 메시지가 표시 되 면 자식 범위에서 역할 할당을 제거 하려고 시도 하는 것입니다. 역할이 할당 된 범위에서 액세스 제어 (IAM)를 열고 다시 시도 해야 합니다. 올바른 범위에서 액세스 제어 (IAM)를 빠르게 여는 것은 **범위** 열을 확인 하 고 **(상속 됨)** 옆의 링크를 클릭 하는 것입니다.

   ![상속 된 역할 할당에 대 한 역할 할당 메시지 제거](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 사용 하 여 Azure 역할 할당 나열](role-assignments-list-portal.md)
- [자습서: Azure Portal을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여](quickstart-assign-role-user-portal.md)
- [Azure RBAC 문제 해결](troubleshooting.md)
- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

---
title: RBAC 및 Azure 포털을 통해 역할 할당 추가 또는 제거
description: AZURE 역할 기반 액세스 제어(RBAC) 및 Azure 포털을 사용하여 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 대한 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246358"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC 및 Azure 포털을 사용하여 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]이 문서에서는 Azure 포털을 사용하여 역할을 할당하는 방법을 설명합니다.

Azure Active Directory에서 관리자 역할을 할당해야 하는 경우 [Azure Active Directory 에서 관리자 역할을 보고 관리자 역할을 할당합니다.](../active-directory/users-groups-roles/directory-manage-roles-portal.md)

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 추가하거나 제거하려면 다음이 있어야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="access-control-iam"></a>액세스 제어(IAM)

**IAM(액세스 제어)은** Azure 리소스에 대한 액세스 권한을 부여하는 역할을 할당하는 데 사용하는 블레이드입니다. ID 및 액세스 관리라고도 하며 Azure 포털의 여러 위치에 나타납니다. 다음 구독에 대한 액세스 제어(IAM) 블레이드의 예를 보여줍니다.

![구독의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/access-control-subscription.png)

IAM(액세스 컨트롤) 블레이드를 사용하면 역할을 할당하려고 할 때 다음 세 가지 질문에 대답할 수 있으면 도움이 됩니다.

1. **액세스가 필요한 사람은 누구입니까?**

    사용자, 그룹, 서비스 주체 또는 관리되는 ID를 참조하는 사람입니다. 이를 *보안 보안 주체라고도*합니다.

1. **그들은 어떤 역할이 필요한가?**

    사용 권한은 역할로 함께 그룹화됩니다. 여러 [기본 제공 역할](built-in-roles.md) 목록에서 선택하거나 사용자 지정 역할을 사용할 수 있습니다.

1. **액세스권한이 필요한 곳은 어디입니까?**

    여기서 는 액세스가 적용되는 리소스 집합을 나타냅니다. 관리 그룹, 구독, 리소스 그룹 또는 저장소 계정과 같은 단일 리소스가 될 수 있는 위치입니다. 이를 *범위라고*합니다.

## <a name="add-a-role-assignment"></a>역할 할당 추가

Azure RBAC에서 Azure 리소스에 대한 액세스 권한을 부여하려면 역할 할당을 추가합니다. 다음 단계에 따라 역할을 할당합니다.

1. Azure 포털에서 **모든 서비스를** 클릭한 다음 액세스 권한을 부여할 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 해당 범위에 대한 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)를 클릭합니다.**

1. 이 범위에서 역할 할당을 보려면 **역할 할당** 탭을 클릭합니다.

    ![액세스 제어(IAM) 및 역할 할당 탭](./media/role-assignments-portal/role-assignments.png)

1. 역할 할당 **추가를** > **Add role assignment**클릭합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 메뉴](./media/role-assignments-portal/add-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹, 서비스 주체 또는 관리 ID르 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

    ![저장된 역할 할당 추가](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>구독 관리자로서 사용자 할당

특정 사용자를 Azure 구독의 관리자로 지정하려면 해당 사용자에게 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당합니다. 소유자 역할은 사용자에게 다른 사용자에게 액세스 권한을 부여하는 권한을 포함하여 구독의 모든 리소스에 대한 전체 액세스 권한을 부여합니다. 이러한 단계는 다른 역할 할당과 동일합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 액세스 권한을 부여하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)를 클릭합니다.**

1. 이 구독의 역할 할당을 보려면 **역할 할당** 탭을 클릭합니다.

    ![액세스 제어(IAM) 및 역할 할당 탭](./media/role-assignments-portal/role-assignments.png)

1. 역할 할당 **추가를** > **Add role assignment**클릭합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 메뉴](./media/role-assignments-portal/add-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **소유자** 역할을 선택합니다.

1. **선택** 목록에서 사용자를 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 직접 입력하여 표시 이름 및 이메일 주소에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 사용자에게는 구독 범위의 소유자 역할이 할당됩니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

Azure RBAC에서 Azure 리소스에서 액세스를 제거하려면 역할 할당을 제거합니다. 다음 단계에 따라 역할 할당을 제거합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **제거**를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 클릭합니다.

    상속된 역할 할당을 제거할 수 없다는 메시지가 표시되면 자식 범위에서 역할 할당을 제거하려고 합니다. 역할이 할당된 범위에서 IAM(액세스 컨트롤)을 열고 다시 시도해야 합니다. 올바른 범위에서 IAM(액세스 컨트롤)을 여는 빠른 방법은 **Scope** 열을 보고 **(상속된)** 다음의 링크를 클릭하는 것입니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure 포털을 사용하여 역할 할당 목록](role-assignments-list-portal.md)
- [자습서: RBAC 및 Azure 포털을 사용 하 여 Azure 리소스에 대 한 사용자 액세스 권한을 부여 합니다.](quickstart-assign-role-user-portal.md)
- [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)
- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

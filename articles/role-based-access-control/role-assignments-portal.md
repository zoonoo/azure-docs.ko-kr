---
title: Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거-Azure RBAC
description: Azure Portal 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117447"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 이 문서에서는 Azure Portal를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

Azure Active Directory에서 관리자 역할을 할당 해야 하는 경우 [Azure Active Directory에서 관리자 역할 보기 및 할당](../active-directory/roles/manage-roles-portal.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>역할 할당 추가

Azure RBAC에서 Azure 리소스에 대 한 액세스 권한을 부여 하려면 역할 할당을 추가 합니다. 역할을 할당 하려면 다음 단계를 수행 합니다. 단계에 대 한 개략적인 개요는 [역할 할당을 추가 하는 단계](role-assignments-steps.md)를 참조 하세요.

### <a name="step-1-identify-the-needed-scope"></a>1 단계: 필요한 범위 확인

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] 범위에 대 한 자세한 내용은 [범위 이해](scope-overview.md)를 참조 하세요.

![Azure RBAC의 범위 수준](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽의 검색 상자에서 액세스 권한을 부여 하려는 범위를 검색 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 특정 리소스를 검색 합니다.

    ![리소스 그룹 Azure Portal 검색](./media/shared/rg-portal-search.png)

1. 해당 범위에 대 한 특정 리소스를 클릭 합니다.

    다음은 예제 리소스 그룹을 보여줍니다.

    ![리소스 그룹 개요](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>2 단계: 역할 할당 추가 창 열기

**액세스 제어 (IAM)** 는 Azure 리소스에 대 한 액세스 권한을 부여 하기 위해 일반적으로 역할을 할당 하는 데 사용 하는 페이지입니다. 이는 IAM (id 및 액세스 관리) 라고도 하며 Azure Portal의 여러 위치에 표시 됩니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

    ![리소스 그룹에 대 한 액세스 제어 (IAM) 페이지](./media/shared/rg-access-control.png)

1. 이 범위에서 역할 할당을 보려면 **역할 할당** 탭을 클릭 합니다.

1. **추가**  >  **역할 할당** 추가를 클릭 합니다.
   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![역할 할당 추가 메뉴](./media/shared/add-role-assignment-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 창](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>3 단계: 적절 한 역할을 선택 합니다.

1. **역할** 목록에서 검색 하거나 스크롤하여 할당 하려는 역할을 찾습니다.

    적절 한 역할을 결정 하는 데 도움이 되도록 정보 아이콘 위로 마우스를 가져가면 역할에 대 한 설명을 표시할 수 있습니다. 추가 정보는 [Azure 기본 제공 역할](built-in-roles.md) 문서를 볼 수 있습니다.

   ![역할 할당 추가에서 역할 선택](./media/role-assignments-portal/add-role-assignment-role.png)

1. 역할을 클릭 하 여 선택 합니다.

### <a name="step-4-select-who-needs-access"></a>4 단계: 액세스 해야 하는 사용자 선택

1. **액세스 할당 대상** 목록에서 액세스 권한을 할당할 보안 주체의 유형을 선택 합니다.

    | Type | 설명 |
    | --- | --- |
    | **사용자, 그룹 또는 서비스 사용자** | 사용자, 그룹 또는 서비스 사용자 (응용 프로그램)에 역할을 할당 하려는 경우이 유형을 선택 합니다. |
    | **사용자 할당 관리 id** | [사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당 하려면이 형식을 선택 합니다. |
    | *시스템 할당 관리 id* | [시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당 하려면 관리 id가 있는 Azure 서비스 인스턴스를 선택 합니다. |

   ![역할 할당 추가에서 보안 주체 유형 선택](./media/role-assignments-portal/add-role-assignment-type.png)

1. 사용자 할당 관리 id 또는 시스템 할당 관리 id를 선택한 경우 관리 id가 있는 **구독** 을 선택 합니다.

1. **Select** 섹션에서 문자열을 입력 하거나 목록에서 스크롤하면 보안 주체를 검색 합니다.

   ![역할 할당 추가에서 사용자 선택](./media/role-assignments-portal/add-role-assignment-user.png)

1. 보안 주체를 찾았으면 클릭 하 여 선택 합니다.

### <a name="step-5-assign-role"></a>5 단계: 역할 할당

1. 역할을 할당 하려면 **저장** 을 클릭 합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

1. **역할** 할당 탭에서 목록에 역할 할당이 표시 되는지 확인 합니다.

    ![역할 할당 추가 저장 됨](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>역할 할당 제거

Azure RBAC에서 Azure 리소스에 대 한 액세스 권한을 제거 하려면 역할 할당을 제거 합니다. 역할 할당을 제거 하려면 다음 단계를 수행 합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![제거 하도록 선택한 역할 할당](./media/role-assignments-portal/rg-role-assignments-select.png)

1. **제거** 를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예** 를 클릭합니다.

    상속 된 역할 할당을 제거할 수 없다는 메시지가 표시 되 면 자식 범위에서 역할 할당을 제거 하려고 시도 하는 것입니다. 역할이 할당 된 범위에서 액세스 제어 (IAM)를 열고 다시 시도 해야 합니다. 올바른 범위에서 액세스 제어 (IAM)를 빠르게 여는 것은 **범위** 열을 확인 하 고 **(상속 됨)** 옆의 링크를 클릭 하는 것입니다.

   ![상속 된 역할 할당에 대 한 역할 할당 메시지 제거](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>다음 단계

- [Azure 구독의 관리자로 사용자 할당](role-assignments-portal-subscription-admin.md)
- [관리 id에 대 한 역할 할당 추가](role-assignments-portal-managed-identity.md)
- [Azure RBAC 문제 해결](troubleshooting.md)

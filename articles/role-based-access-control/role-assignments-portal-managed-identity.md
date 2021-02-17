---
title: 관리 id에 Azure 역할 할당 (미리 보기)-Azure RBAC
description: 관리 id로 시작 하 여 Azure 역할을 할당 한 다음 Azure Portal 및 Azure 역할 기반 액세스 제어 (Azure RBAC)를 사용 하 여 범위 및 역할을 선택 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 57c8c00a64996bc6223fbe7e514db9db38ccdcc2
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556859"
---
# <a name="assign-azure-roles-to-a-managed-identity-preview"></a>관리 id에 Azure 역할 할당 (미리 보기)

[Azure Portal를 사용 하 여 Azure 역할 할당](role-assignments-portal.md)에 설명 된 대로 **액세스 제어 (IAM)** 페이지를 사용 하 여 관리 되는 id에 역할을 할당할 수 있습니다. 액세스 제어 (IAM) 페이지를 사용 하는 경우 범위에서 시작 하 고 관리 되는 id 및 역할을 선택 합니다. 이 문서에서는 관리 되는 id에 대해 역할을 할당 하는 다른 방법을 설명 합니다. 이러한 단계를 사용 하 여 관리 되는 id로 시작한 다음 범위 및 역할을 선택 합니다.

> [!IMPORTANT]
> 이러한 대체 단계는 현재 미리 보기 상태를 사용 하 여 관리 되는 id에 역할을 할당 합니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

관리 id로 시작 하 여 시스템 할당 관리 id에 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 시스템 할당 관리 id를 엽니다.

1. 왼쪽 메뉴에서 **id** 를 클릭 합니다.

    ![시스템 할당 관리 ID](./media/shared/identity-system-assigned.png)

1. **사용 권한** 아래에서 **Azure 역할 할당** 을 클릭 합니다.

    선택한 시스템 할당 관리 id에 역할이 이미 할당 된 경우 역할 할당 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![시스템 할당 관리 id에 대 한 역할 할당](./media/shared/role-assignments-system-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

1. **역할 할당 추가 (미리 보기)** 를 클릭 합니다.

1. 드롭다운 목록을 사용 하 여 **구독**, **리소스 그룹** 또는 리소스와 같은 역할 할당이 적용 되는 리소스 집합을 선택할 수 있습니다.

    선택한 범위에 대 한 역할 할당 쓰기 권한이 없는 경우 인라인 메시지가 표시 됩니다. 

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

   ![시스템 할당 관리 id에 대 한 역할 할당 창 추가](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. **저장** 을 클릭하여 역할을 할당합니다.

   몇 분 후에 관리 되는 id에는 선택한 범위에서 역할이 할당 됩니다.

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

관리 id로 시작 하 여 사용자 할당 관리 id에 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 사용자 할당 관리 id를 엽니다.

1. 왼쪽 메뉴에서 **Azure 역할 할당** 을 클릭 합니다.

    선택한 사용자 할당 관리 id에 역할이 이미 할당 된 경우 역할 할당 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![사용자 할당 관리 id에 대 한 역할 할당](./media/shared/role-assignments-user-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

1. **역할 할당 추가 (미리 보기)** 를 클릭 합니다.

1. 드롭다운 목록을 사용 하 여 **구독**, **리소스 그룹** 또는 리소스와 같은 역할 할당이 적용 되는 리소스 집합을 선택할 수 있습니다.

    선택한 범위에 대 한 역할 할당 쓰기 권한이 없는 경우 인라인 메시지가 표시 됩니다. 

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

   ![사용자 할당 관리 id에 대 한 역할 할당 창 추가](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. **저장** 을 클릭하여 역할을 할당합니다.

   몇 분 후에 관리 되는 id에는 선택한 범위에서 역할이 할당 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
- [Azure Portal를 사용 하 여 Azure 역할 할당](role-assignments-portal.md)
- [Azure Portal을 사용하여 Azure 역할 할당을 나열](role-assignments-list-portal.md)합니다.

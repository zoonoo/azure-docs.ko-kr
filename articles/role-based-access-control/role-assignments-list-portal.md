---
title: Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 나열
description: Azure RBAC (역할 기반 액세스 제어) 및 Azure Portal를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 id가 액세스할 수 있는 리소스를 확인 하는 방법에 대해 알아봅니다.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6117f22b24887e913ed2f8d3a43e80335121636d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934488"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 나열

이 문서 [!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Azure Portal를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

> [!NOTE]
> 조직에서 [Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하는 서비스 공급자에 대해 아웃소싱 된 관리 기능을 사용 하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시 되지 않습니다.

## <a name="list-role-assignments-for-a-user-or-group"></a>사용자 또는 그룹에 대 한 역할 할당 나열

구독에서 사용자 또는 그룹에 할당 된 역할을 확인 하는 가장 쉬운 방법은 **Azure 리소스** 창을 사용 하는 것입니다.

1. Azure Portal에서 **모든 서비스** 를 클릭 한 다음 **사용자** 또는 **그룹**을 선택 합니다.

1. 원하는 역할 할당을 나열할 사용자 또는 그룹을 클릭 합니다.

1. **Azure 리소스**를 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양 한 범위에서 선택한 사용자 또는 그룹에 할당 된 역할 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![사용자에 대한 역할 할당](./media/role-assignments-list-portal/azure-resources-user.png)    

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

## <a name="list-owners-of-a-subscription"></a>구독의 소유자 나열

구독에 대 한 [소유자](built-in-roles.md#owner) 역할이 할당 된 사용자는 구독의 모든 항목을 관리할 수 있습니다. 다음 단계를 수행 하 여 구독의 소유자를 나열 합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 소유자를 나열 하려는 구독을 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. **소유자** 섹션으로 스크롤하여이 구독의 소유자 역할이 할당 된 모든 사용자를 확인 합니다.

   ![구독 액세스 제어-역할 할당 탭](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>범위에서 역할 할당 나열

1. Azure Portal에서 **모든 서비스** 를 클릭 한 다음 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

   ![액세스 제어 - 역할 할당 탭](./media/role-assignments-list-portal/access-control-role-assignments.png)

   역할 할당 탭에서 이 범위에서 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스 권한은 이 리소스에 특정적으로 할당되거나 부모 범위에 대한 할당에서 상속됩니다.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>범위에서 사용자에 대 한 역할 할당 나열

사용자, 그룹, 서비스 주체 또는 관리 id에 대 한 액세스를 나열 하려면 해당 역할 할당을 나열 합니다. 특정 범위에서 단일 사용자, 그룹, 서비스 주체 또는 관리 id에 대 한 역할 할당을 나열 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭 한 다음 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **액세스 권한 확인** 탭을 클릭합니다.

    ![액세스 제어 - 액세스 권한 확인 탭](./media/role-assignments-list-portal/access-control-check-access.png)

1. **찾기** 목록에서 액세스 권한을 확인하려는 보안 주체의 유형을 선택합니다.

1. 검색 상자에서 표시 이름, 이메일 주소 또는 개체 ID에 대한 디렉터리를 검색할 문자열을 입력합니다.

    ![액세스 권한 확인 선택 목록](./media/role-assignments-list-portal/check-access-select.png)

1. 보안 주체를 클릭하여 **할당** 창을 엽니다.

    ![할당 창](./media/role-assignments-list-portal/check-access-assignments.png)

    이 창에서 선택한 보안 주체 및 범위에 할당된 역할을 볼 수 있습니다. 이 범위에 거부 할당이 있거나 이 범위에 상속된 거부 할당이 있으면 나열됩니다.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>시스템 할당 관리 id에 대 한 역할 할당 나열

1. Azure Portal에서 시스템 할당 관리 id를 엽니다.

1. 왼쪽 메뉴에서 **id**를 클릭 합니다.

    ![시스템 할당 관리 ID](./media/role-assignments-list-portal/identity-system-assigned.png)

1. **역할 할당**에서 **이 관리 되는 id에 할당 된 Azure RBAC 역할 표시**를 클릭 합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양 한 범위에서 선택한 시스템 할당 관리 id에 할당 된 역할 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![시스템 할당 관리 id에 대 한 역할 할당](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>사용자 할당 관리 id에 대 한 역할 할당 나열

1. Azure Portal에서 시스템 할당 관리 id를 엽니다.

1. **Azure 리소스**를 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양 한 범위에서 선택한 사용자 할당 관리 id에 할당 된 역할 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![시스템 할당 관리 id에 대 한 역할 할당](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거](role-assignments-portal.md)
- [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)

---
title: Azure RBAC 및 Azure 포털을 사용하여 역할 할당 목록
description: 사용자, 그룹, 서비스 주체 또는 관리되는 ID가 AZURE 역할 기반 액세스 제어(RBAC) 및 Azure 포털을 사용하여 액세스할 수 있는 리소스를 확인하는 방법을 알아봅니다.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062236"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC 및 Azure 포털을 사용하여 역할 할당 목록

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]이 문서에서는 Azure 포털을 사용하여 역할 할당을 나열하는 방법을 설명합니다.

> [!NOTE]
> 조직에서 [Azure 위임리소스 관리를](../lighthouse/concepts/azure-delegated-resource-management.md)사용하는 서비스 공급자에게 관리 기능을 아웃소싱한 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="list-role-assignments-for-a-user-or-group"></a>사용자 또는 그룹에 대한 역할 할당 목록

구독에서 사용자 또는 그룹에 할당된 역할을 확인하는 가장 쉬운 방법은 **Azure 리소스** 창을 사용하는 것입니다.

1. Azure 포털에서 **모든 서비스를** 클릭한 다음 **사용자** 또는 **그룹**을 선택합니다.

1. 역할 할당을 나열하려는 사용자 또는 그룹을 클릭합니다.

1. **Azure 리소스**를 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양한 범위에서 선택한 사용자 또는 그룹에 할당된 역할 목록이 표시됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함됩니다.

    ![사용자에 대한 역할 할당](./media/role-assignments-list-portal/azure-resources-user.png)    

1. 구독을 변경하려면 구독 목록을 **클릭합니다.**

## <a name="list-owners-of-a-subscription"></a>구독의 목록 소유자

구독에 대한 [소유자](built-in-roles.md#owner) 역할이 할당된 사용자는 구독의 모든 것을 관리할 수 있습니다. 다음 단계에 따라 구독 소유자를 나열합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 소유자를 나열할 구독을 클릭합니다.

1. **액세스 제어(IAM)를 클릭합니다.**

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. **소유자** 섹션으로 스크롤하여 이 구독에 대한 소유자 역할이 할당된 모든 사용자를 확인합니다.

   ![구독 액세스 제어 - 역할 할당 탭](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>범위에서 역할 할당 목록 지정

1. Azure 포털에서 **모든 서비스를** 클릭한 다음 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)를 클릭합니다.**

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

   ![액세스 제어 - 역할 할당 탭](./media/role-assignments-list-portal/access-control-role-assignments.png)

   역할 할당 탭에서 이 범위에서 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스 권한은 이 리소스에 특정적으로 할당되거나 부모 범위에 대한 할당에서 상속됩니다.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>범위에서 사용자에 대한 역할 할당 목록

사용자, 그룹, 서비스 주체 또는 관리되는 ID에 대한 액세스를 나열하려면 해당 역할 할당을 나열합니다. 다음 단계에 따라 단일 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 대한 역할 할당을 특정 범위에 나열합니다.

1. Azure 포털에서 **모든 서비스를** 클릭한 다음 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)를 클릭합니다.**

1. **액세스 권한 확인** 탭을 클릭합니다.

    ![액세스 제어 - 액세스 권한 확인 탭](./media/role-assignments-list-portal/access-control-check-access.png)

1. **찾기** 목록에서 액세스 권한을 확인하려는 보안 주체의 유형을 선택합니다.

1. 검색 상자에서 표시 이름, 이메일 주소 또는 개체 ID에 대한 디렉터리를 검색할 문자열을 입력합니다.

    ![액세스 권한 확인 선택 목록](./media/role-assignments-list-portal/check-access-select.png)

1. 보안 주체를 클릭하여 **할당** 창을 엽니다.

    ![할당 창](./media/role-assignments-list-portal/check-access-assignments.png)

    이 창에서 선택한 보안 주체 및 범위에 할당된 역할을 볼 수 있습니다. 이 범위에 거부 할당이 있거나 이 범위에 상속된 거부 할당이 있으면 나열됩니다.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>시스템 할당된 관리 되는 ID에 대 한 역할 할당 목록

1. Azure 포털에서 시스템 할당된 관리되는 ID를 엽니다.

1. 왼쪽 메뉴에서 **ID**를 클릭합니다.

    ![시스템 할당 관리 ID](./media/role-assignments-list-portal/identity-system-assigned.png)

1. **역할 할당에서** **이 관리되는 ID에 할당된 Azure RBAC 역할 표시를 클릭합니다.**

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양한 범위에서 선택한 시스템 할당관리 ID에 할당된 역할 목록이 표시됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함됩니다.

    ![시스템 할당된 관리 ID에 대한 역할 할당](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>사용자 할당된 관리 되는 ID에 대 한 역할 할당 목록

1. Azure 포털에서 사용자 지정 관리되는 ID를 엽니다.

1. **Azure 리소스**를 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양한 범위에서 선택한 사용자 할당관리 ID에 할당된 역할 목록이 표시됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함됩니다.

    ![시스템 할당된 관리 ID에 대한 역할 할당](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. 구독을 변경하려면 구독 목록을 **클릭합니다.**

## <a name="list-number-of-role-assignments"></a>역할 할당 목록 수

각 구독에 최대 **2000개의** 역할 할당을 가질 수 있습니다. 이 제한을 추적하는 데 도움이 되도록 **역할 할당** 탭에는 현재 구독의 역할 할당 수를 나열하는 차트가 포함되어 있습니다.

![액세스 제어 - 역할 할당 차트 수](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

최대 수에 가까워지고 역할 할당을 추가하려고 하면 **역할 할당 추가** 창에 경고가 표시됩니다. 역할 할당 수를 줄일 수 있는 방법은 [Azure RBAC 를 참조하세요.](troubleshooting.md#azure-role-assignments-limit)

![액세스 제어 - 역할 할당 경고 추가](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure 포털을 사용하여 역할 할당 추가 또는 제거](role-assignments-portal.md)
- [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)

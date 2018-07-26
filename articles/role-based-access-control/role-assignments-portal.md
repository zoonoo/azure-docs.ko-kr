---
title: RBAC 및 Azure Portal을 사용하여 엑세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure Portal을 사용하여 사용자, 그룹 및 응용 프로그램의 액세스 권한을 관리하는 방법을 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
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
ms.date: 07/11/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aaa36d850516ff4d8e40b62c588347468da5c6d2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008164"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>RBAC 및 Azure Portal을 사용하여 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure Portal을 사용하여 사용자, 그룹 및 응용 프로그램의 액세스 권한을 관리하는 방법을 설명합니다.

## <a name="list-roles"></a>역할 나열

역할 정의는 역할 할당에 사용되는 권한 컬렉션입니다. Azure에는 60개가 넘는 [기본 제공 역할](built-in-roles.md)이 있습니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 선택합니다.

1. 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

   ![역할 옵션](./media/role-assignments-portal/list-subscription-access-control.png)

1. **역할**을 선택하면 모든 기본 제공 역할 및 사용자 지정 역할 목록을 볼 수 있습니다.

   ![역할 옵션](./media/role-assignments-portal/roles-option.png)

   각 역할에 할당된 사용자 및 그룹 수를 볼 수 있습니다.

   ![역할 목록](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>액세스 권한 나열

액세스 권한을 관리할 때 누가, 어떤 수준에서, 어떤 액세스 권한을 갖고 있는지 확인하고 싶은 경우가 있습니다. 역할 할당을 나열하면 액세스 권한이 나열됩니다.

### <a name="list-role-assignments-for-a-subscription"></a>구독의 역할 할당 나열

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 선택합니다.

1. 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

    ID 및 액세스 관리라고도 하는 액세스 제어(IAM) 블레이드에서 이 구독에 액세스할 수 있는 사람과 할당된 역할을 볼 수 있습니다.

    ![액세스 제어(IAM) 블레이드](./media/role-assignments-portal/subscription-access-control.png)

    클래식 구독 관리자와 공동 관리자는 RBAC 모델에서 구독의 소유자로 간주됩니다.


### <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

1. 왼쪽 목록에서 **리소스 그룹**을 선택합니다.

1. 리소스 그룹을 선택하고 **액세스 제어(IAM)** 를 선택합니다.

   액세스 제어(IAM) 블레이드에서 이 리소스 그룹에 대한 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스는 리소스 그룹에 특별히 할당되거나 부모 구독에 할당된 내용에서 상속됩니다.

   ![리소스 그룹](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

1. 탐색 목록에서 **Azure Active Directory**를 선택합니다.

1. **사용자**를 선택하여 **모든 사용자**를 엽니다.

   ![Azure Active Directory 모든 사용자 블레이드](./media/role-assignments-portal/aad-all-users.png)

1. 목록에서 개별 사용자를 선택합니다.

1. **관리** 섹션에서 **Azure 리소스**를 선택합니다.

   ![Azure Active Directory 사용자 Azure 리소스](./media/role-assignments-portal/aad-user-azure-resources.png)

   Azure 리소스 블레이드에서, 선택한 사용자 및 선택한 구독에 할당된 역할을 볼 수 있습니다. 이 목록에는 여러분이 읽기 권한을 갖고 있는 리소스의 역할 할당만 포함됩니다. 예를 들어 읽을 수 없는 역할이 사용자에게 할당된 경우 해당 역할 할당은 목록에 표시되지 않습니다.

1. 여러 구독이 있는 경우 **구독** 드롭다운 목록을 선택하여 다른 구독에서 역할 할당을 확인할 수 있습니다.

## <a name="grant-access"></a>액세스 권한 부여

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>구독 범위에서 역할 할당 만들기

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 선택합니다.

1. 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 선택하면 구독 범위의 현재 역할 할당 목록을 볼 수 있습니다.

   ![리소스 그룹의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/grant-subscription-access-control.png)

1. **추가**를 선택하여 **권한 추가** 창을 엽니다.

   역할을 할당할 수 있는 권한이 없는 경우 **추가** 옵션이 표시되지 않습니다.

   ![권한 추가 창](./media/role-assignments-portal/add-permissions.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹 또는 응용 프로그램을 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 선택하여 역할 할당을 만듭니다.

   몇 분이 지나면 구독 범위에서 보안 주체에 역할이 할당됩니다.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>리소스 그룹 범위에서 역할 할당 만들기

1. 왼쪽 목록에서 **리소스 그룹**을 선택합니다.

1. 리소스 그룹을 선택합니다.

1. **액세스 제어(IAM)** 를 선택하면 리소스 그룹 범위의 현재 역할 할당 목록을 볼 수 있습니다.

   ![리소스 그룹의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. **추가**를 선택하여 **권한 추가** 창을 엽니다.

   역할을 할당할 수 있는 권한이 없는 경우 **추가** 옵션이 표시되지 않습니다.

   ![권한 추가 창](./media/role-assignments-portal/add-permissions.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹 또는 응용 프로그램을 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 선택하여 역할 할당을 만듭니다.

   몇 분이 지나면 리소스 그룹 범위에서 보안 주체에 역할이 할당됩니다.

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 역할 할당을 제거해야 합니다.

### <a name="remove-a-role-assignment"></a>역할 할당 제거

1. 제거할 역할 할당이 있는 구독, 리소스 그룹 또는 리소스의 **액세스 제어(IAM)** 블레이드를 엽니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **제거**를 선택합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 선택합니다.

상속된 역할 할당은 제거할 수 없습니다. 상속된 역할 할당을 제거하려면 역할 할당이 만들어진 범위에서 역할 할당을 제거해야 합니다. **범위** 열에서 **상속됨** 옆쪽을 이 역할이 할당된 리소스로 이동하는 링크가 있습니다. 나열된 범위로 이동하여 역할 할당을 제거합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: RBAC 및 Azure Portal을 사용하여 사용자에게 액세스 권한 부여](quickstart-assign-role-user-portal.md)
* [자습서: RBAC 및 Azure PowerShell을 사용하여 사용자에게 액세스 권한 부여](tutorial-role-assignments-user-powershell.md)
* [기본 제공 역할](built-in-roles.md)

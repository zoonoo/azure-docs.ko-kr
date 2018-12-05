---
title: RBAC 및 Azure Portal을 사용하여 엑세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure Portal을 사용하여 사용자, 그룹, 서비스 주체 및 관리 ID의액세스 권한을 관리하는 방법을 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f5d48a074f8069e243af5644f86ad3c3d8f559b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634866"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>RBAC 및 Azure Portal을 사용하여 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure Portal을 사용하여 사용자, 그룹, 서비스 주체 및 관리 ID의 액세스 권한을 관리하는 방법을 설명합니다.

## <a name="open-access-control-iam"></a>액세스 제어(IAM) 열기

**액세스 제어(IAM)** 블레이드(또는 ID 및 액세스 관리)는 포털 전체에서 나타납니다. 포털에서 액세스 권한을 보거나 관리하기 위해, 일반적으로 가장 먼저 해야 할 일은 사용자가 보거나 변경하려는 범위에서 액세스 제어(IAM) 블레이드를 여는 것입니다.

1. Azure Portal에서 **모든 서비스**를 클릭한 다음, 보거나 관리하려는 범위 또는 리소스를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 보거나 관리하려는 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음 구독에 대한 액세스 제어(IAM) 블레이드의 예를 보여줍니다.

    ![구독의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>역할 및 권한 보기

역할 정의는 역할 할당에 사용되는 권한 컬렉션입니다. Azure에는 70개가 넘는 [기본 제공 역할](built-in-roles.md)이 있습니다. 다음 단계에 따라 관리 및 데이터 창에서 수행할 수 있는 역할 및 권한을 볼 수 있습니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 역할 및 권한을 보려는 범위에서 **액세스 제어(IAM)** 를 엽니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

   이 범위의 각 역할에 할당된 사용자 및 그룹 수를 볼 수 있습니다.

   ![역할 목록](./media/role-assignments-portal/roles-list.png)

1. 개별 역할을 클릭하여 이 역할에 할당된 사람을 보거나 해당 역할에 대한 권한을 봅니다.

   ![역할 할당](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>역할 할당 보기

액세스 권한을 관리할 때 누가, 어떤 수준에서, 어떤 액세스 권한을 갖고 있는지 확인하고 싶은 경우가 있습니다. 사용자, 그룹, 서비스 주체 또는 관리 ID의 액세스 권한을 표시하려면 역할 할당을 봅니다.

### <a name="view-role-assignments-for-a-single-user"></a>단일 사용자의 역할 할당 보기

다음 단계에 따라 특정 범위에서 단일 사용자, 그룹, 서비스 주체 또는 관리 ID의 액세스 권한을 볼 수 있습니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 보려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **액세스 권한 확인** 탭을 클릭합니다.

    ![액세스 제어 - 액세스 권한 확인 탭](./media/role-assignments-portal/access-control-check-access.png)

1. **찾기** 목록에서 액세스 권한을 확인하려는 보안 주체의 유형을 선택합니다.

1. 검색 상자에 표시 이름, 이메일 주소 또는 개체 ID에 대한 디렉터리를 검색할 문자열을 입력합니다.

    ![액세스 권한 확인 선택 목록](./media/role-assignments-portal/check-access-select.png)

1. 보안 주체를 클릭하여 **할당** 창을 엽니다.

    ![할당 창](./media/role-assignments-portal/check-access-assignments.png)

    이 창에서 선택한 보안 주체 및 범위에 할당된 역할을 볼 수 있습니다. 이 범위 또는 이 범위로 상속된 거부 할당이 있는 경우 목록으로 표시됩니다.

### <a name="view-all-role-assignments-at-a-scope"></a>특정 범위의 모든 역할 할당 보기

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 보려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭(또는 역할 할당 보기 타일의 **보기** 버튼을 클릭)하여 이 범위에 모든 역할 할당을 봅니다.

   ![액세스 제어 - 역할 할당 탭](./media/role-assignments-portal/access-control-role-assignments.png)

   역할 할당 탭에서 이 범위에서 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스 권한은 이 리소스에 특정적으로 할당되거나 부모 범위에 대한 할당에서 상속됩니다.

## <a name="add-a-role-assignment"></a>역할 할당 추가

RBAC에서 액세스 권한을 부여하려면 사용자, 그룹, 서비스 주체 또는 관리 ID에 역할을 할당합니다. 다음 단계를 수행하여 서로 다른 범위에서 액세스 권한을 부여합니다.

### <a name="assign-a-role-at-a-scope"></a>특정 범위에서 역할 할당

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 부여하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

1. **역할 할당 추가**를 클릭하여 역할 할당 추가 창을 엽니다.

   역할을 할당할 권한이 없으면 역할 할당 추가 옵션을 사용할 수 없게 됩니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹, 서비스 주체 또는 관리 ID르 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>구독 관리자로서 사용자 할당

다른 사용자를 Azure 구독의 관리자로 추가하려면 해당 사용자에게 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당합니다. 소유자 역할은 다른 사용자에게 액세스를 위임할 수 있는 권한을 비롯한 구독의 리소스에 대한 모든 권한을 사용자에게 부여할 수 있습니다. 이러한 단계는 다른 역할 할당과 동일합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 액세스 권한을 부여하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. **역할 할당 추가**를 클릭하여 역할 할당 추가 창을 엽니다.

   역할을 할당할 권한이 없으면 역할 할당 추가 옵션을 사용할 수 없게 됩니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **소유자** 역할을 선택합니다.

1. **선택** 목록에서 사용자를 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 직접 입력하여 표시 이름 및 이메일 주소에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 사용자에게는 구독 범위의 소유자 역할이 할당됩니다.

## <a name="remove-role-assignments"></a>역할 할당 제거

RBAC에서 액세스 권한을 제거하려면 역할 할당을 제거해야 합니다. 다음 단계에 따라 액세스 권한을 제거합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **제거**를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 클릭합니다.

    상속된 역할 할당은 제거할 수 없습니다. 상속된 역할 할당을 제거하려면 역할 할당이 만들어진 범위에서 역할 할당을 제거해야 합니다. **범위** 열에서 **(상속됨)** 옆쪽에 이 역할이 할당된 범위로 이동하는 링크가 있습니다. 나열된 범위로 이동하여 역할 할당을 제거합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>다음 단계

* [자습서: RBAC 및 Azure Portal을 사용하여 사용자에게 액세스 권한 부여](quickstart-assign-role-user-portal.md)
* [자습서: RBAC 및 Azure PowerShell을 사용하여 사용자에게 액세스 권한 부여](tutorial-role-assignments-user-powershell.md)
* [기본 제공 역할](built-in-roles.md)
* [Azure 관리 그룹으로 리소스 구성](../azure-resource-manager/management-groups-overview.md)

---
title: Azure Portal를 사용 하 여 Azure 역할 할당 나열-Azure RBAC
description: Azure Portal 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 사용자 또는 관리 id가 액세스할 수 있는 리소스를 확인 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/16/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 280e5012f7c41a906b5b2e266840ea3c0cc315ba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151685"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 역할 할당 나열

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 이 문서에서는 Azure Portal를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

> [!NOTE]
> 조직에서 [Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하는 서비스 공급자에 대해 아웃소싱 된 관리 기능을 사용 하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시 되지 않습니다.

## <a name="list-role-assignments-for-a-user-or-group"></a>사용자 또는 그룹에 대 한 역할 할당 나열

구독에서 사용자 또는 그룹에 할당 된 역할을 확인 하는 가장 쉬운 방법은 **Azure 역할 할당** 창을 사용 하는 것입니다.

1. Azure Portal에서 Azure Portal 메뉴의 **모든 서비스** 를 선택 합니다.

1. **Azure Active Directory** 선택 하 고 **사용자** 또는 **그룹**을 선택 합니다.

1. 원하는 역할 할당을 나열할 사용자 또는 그룹을 클릭 합니다.

1. **Azure 역할 할당**을 클릭 합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양 한 범위에서 선택한 사용자 또는 그룹에 할당 된 역할 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![사용자에 대한 역할 할당](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

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

## <a name="list-role-assignments-for-a-managed-identity"></a>관리 id에 대 한 역할 할당 나열

앞에서 설명한 것 처럼 **액세스 제어 (IAM)** 블레이드를 사용 하 여 특정 범위에서 시스템 할당 및 사용자 할당 관리 id에 대 한 역할 할당을 나열할 수 있습니다. 이 섹션에서는 관리 되는 id에 대해서만 역할 할당을 나열 하는 방법을 설명 합니다.

### <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

1. Azure Portal에서 시스템 할당 관리 id를 엽니다.

1. 왼쪽 메뉴에서 **id**를 클릭 합니다.

    ![시스템 할당 관리 ID](./media/shared/identity-system-assigned.png)

1. **사용 권한**아래에서 **Azure 역할 할당**을 클릭 합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양 한 범위에서 선택한 시스템 할당 관리 id에 할당 된 역할 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![시스템 할당 관리 id에 대 한 역할 할당](./media/shared/role-assignments-system-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

### <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

1. Azure Portal에서 사용자 할당 관리 id를 엽니다.

1. **Azure 역할 할당**을 클릭 합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같은 다양 한 범위에서 선택한 사용자 할당 관리 id에 할당 된 역할 목록이 표시 됩니다. 이 목록에는 읽을 수 있는 권한이 있는 모든 역할 할당이 포함 됩니다.

    ![시스템 할당 관리 id에 대 한 역할 할당](./media/shared/role-assignments-user-assigned.png)

1. 구독을 변경 하려면 **구독** 목록을 클릭 합니다.

## <a name="list-number-of-role-assignments"></a>역할 할당의 수를 나열 합니다.

각 구독에서 최대 **2000**개의 역할 할당을 유지할 수 있습니다. 이 제한에는 구독, 리소스 그룹 및 리소스 범위의 역할 할당이 포함됩니다. 이 제한을 추적 하는 데 도움이 되도록 **역할 할당** 탭에는 현재 구독에 대 한 역할 할당 수를 나열 하는 차트가 포함 됩니다.

![액세스 제어-역할 할당의 수 차트](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

최대 개수에 근접 하 고 더 많은 역할 할당을 추가 하려고 하면 **역할 할당 추가** 창에 경고가 표시 됩니다. 역할 할당 수를 줄일 수 있는 방법은 [AZURE RBAC 문제 해결](troubleshooting.md#azure-role-assignments-limit)을 참조 하세요.

![액세스 제어-역할 할당 추가 경고](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>역할 할당 다운로드

CSV 또는 JSON 형식으로 된 범위에서 역할 할당을 다운로드할 수 있습니다. 이는 스프레드시트에서 목록을 검사 하거나 구독을 마이그레이션할 때 인벤토리를 수행 해야 하는 경우에 유용 합니다.

역할 할당을 다운로드 하는 경우 다음 조건을 염두에 두어야 합니다.

- 디렉터리 판독기 역할과 같은 디렉터리를 읽을 수 있는 권한이 없는 경우 DisplayName, SignInName 및 ObjectType 열이 비어 있게 됩니다.
- 보안 주체가 삭제 된 역할 할당은 포함 되지 않습니다.
- 클래식 관리자에 게 부여 된 액세스 권한은 포함 되지 않습니다.

범위에서 역할 할당을 다운로드 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭 한 다음 역할 할당을 다운로드 하려는 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당 다운로드** 를 클릭 하 여 역할 할당 다운로드 창을 엽니다.

    ![액세스 제어-역할 할당 다운로드](./media/role-assignments-list-portal/download-role-assignments.png)

1. 확인란을 사용 하 여 다운로드 한 파일에 포함 하려는 역할 할당을 선택 합니다.

    - **상속** 됨-현재 범위에 대해 상속 된 역할 할당을 포함 합니다.
    - **현재 범위에서** -현재 범위에 대 한 역할 할당을 포함 합니다.
    - **자식** -현재 범위 아래 수준에서 역할 할당을 포함 합니다. 이 확인란은 관리 그룹 범위에서 사용할 수 없습니다.

1. 쉼표로 구분 된 값 (CSV) 또는 JavaScript Object Notation (JSON) 일 수 있는 파일 형식을 선택 합니다.

1. 파일 이름을 지정 합니다.

1. **시작** 을 클릭 하 여 다운로드를 시작 합니다.

    다음은 각 파일 형식에 대 한 출력 예를 보여 줍니다.

    ![역할 할당을 CSV로 다운로드](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![역할 할당을 CSV로 다운로드](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](role-assignments-portal.md)
- [Azure RBAC 문제 해결](troubleshooting.md)

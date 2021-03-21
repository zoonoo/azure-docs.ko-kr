---
title: Synapse Studio에서 Synapse RBAC 할당을 관리 하는 방법
description: 이 문서에서는 AAD 보안 주체에 Synapse RBAC 역할을 할당 하 고 해지 하는 방법을 설명 합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: bed0c00b8cb5718456302dff06e98ff2f7f2b4e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102192"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Synapse Studio에서 Synapse RBAC 역할 할당을 관리 하는 방법

Synapse RBAC는 역할을 사용 하 여 사용자, 그룹 및 기타 보안 주체에 게 사용 권한을 할당 하 여 Synapse 리소스 및 코드 아티팩트의 액세스 및 사용을 가능 하 게 합니다.  [자세히 알아보기](./synapse-workspace-synapse-rbac.md)

이 문서에서는 Synapse RBAC 역할 할당을 추가 하 고 삭제 하는 방법을 보여 줍니다.

>[!Note]
>- Synapse RBAC 역할 할당을 관리 하려면 작업 영역에 대 한 Synapse 관리자 역할이 나 관리 하려는 개체를 포함 하는 하위 수준 범위에 있어야 합니다. 작업 영역에서 Synapse 관리자 인 경우 작업 영역의 모든 개체에 대 한 액세스 권한을 부여할 수 있습니다. 
>- Synapse 관리자 역할이 할당 된 경우에도 다른 AD 테 넌 트에서 **게스트 사용자** 는 역할 할당을 보거나 관리할 수 없습니다.
>- Synapse 관리자를 할당 하거나 사용할 수 없는 경우 작업 영역에 대 한 액세스 권한을 다시 얻으려면 작업 영역에 대 한 **AZURE RBAC** 역할 할당을 관리할 수 있는 권한이 있는 사용자는 **Synapse RBAC** 역할 할당을 관리할 수도 있습니다 .이를 통해 Synapse 관리자나 기타 Synapse 역할 할당을 추가할 수 있습니다.
>- Sql 풀에 대 한 액세스는 SQL 권한을 사용 하 여 관리 됩니다.  Synapse 관리자 및 Synapse SQL 관리자 역할을 제외 하 고 Synapse RBAC 역할은 SQL 풀에 대 한 액세스 권한을 부여 하지 않습니다.

>[!important]
>- Synapse RBAC 역할 할당에 대 한 변경 내용은 적용 되는 데 2-5 분 정도 걸릴 수 있습니다. 
>- 보안 그룹의 멤버 자격을 수정 하 여 Synapse RBAC 권한을 관리 하는 경우 멤버 자격에 대 한 변경 내용은 Azure Active Directory를 사용 하 여 관리 됩니다.  그룹 멤버 자격에 대 한 변경 내용은 적용 되는 데 10-15 분 이상 걸릴 수 있습니다.

## <a name="open-synapse-studio"></a>Synapse Studio 열기  

사용자, 그룹, 서비스 주체 또는 관리 id에 역할을 할당 하려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택 합니다. 

![작업 영역에 로그인](./media/common/login-workspace.png) 
 
 작업 영역을 연 후 왼쪽의 **보안** 섹션을 확장 하 고 **액세스 제어** 를 선택 합니다. 

 ![왼쪽의 보안 섹션에서 Access Control 선택](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

액세스 제어 화면에 현재 역할 할당이 나열 됩니다.  사용자 이름 또는 전자 메일을 기준으로 목록을 필터링 하 고 포함 된 개체 유형, 역할 또는 범위를 선택적으로 필터링 할 수 있습니다. 이 화면에서 역할 할당을 추가 하거나 제거할 수 있습니다.  

## <a name="add-a-synapse-role-assignment"></a>Synapse 역할 할당 추가

액세스 제어 화면에서 **+ 추가** 를 선택 하 여 새 역할 할당을 만듭니다.

![+ 추가를 클릭 하 여 새 역할 할당을 만듭니다.](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

역할 할당 추가 탭에서 작업 영역 범위 또는 작업 영역 항목 범위에 역할 할당을 만들 수 있습니다. 

## <a name="add-workspace-scoped-role-assignment"></a>작업 영역 범위 역할 할당 추가

먼저 범위로 **작업 영역** 을 선택 하 고 **Synapse RBAC 역할** 을 선택 합니다.  역할을 할당할 **사용자** 를 선택 하 고 역할 할당을 만듭니다. 

![작업 영역 역할 할당 추가-역할 선택](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

할당 된 역할은 작업 영역에서 적용 가능한 모든 개체에 적용 됩니다.

## <a name="add-workspace-item-scoped-role-assignment"></a>작업 영역 항목 범위 역할 할당 추가

보다 세분화 된 범위에서 역할을 할당 하려면 **작업 영역 항목** 을 범위로 선택한 다음 범위 지정 **항목 유형을** 선택 합니다.       

![작업 영역 항목 역할 할당 추가-항목 유형 선택](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

범위로 사용할 특정 **항목** 을 선택 하 고 드롭다운에서 할당할 **역할** 을 선택 합니다.  드롭다운 목록에는 선택한 항목 유형에 유효한 역할만 나열 됩니다. [자세한 정보를 알아보세요](./synapse-workspace-synapse-rbac.md).  

![작업 영역 항목 역할 할당 추가-역할 선택](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
그런 다음 역할을 할당할 **주 서버를 선택** 합니다.  반복적으로 여러 보안 주체를 선택할 수 있습니다.  **적용** 을 선택 하 여 역할 할당을 만듭니다.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Synapse RBAC 역할 할당 제거

Synapse RBAC 액세스를 취소 하려면 적절 한 역할 할당을 제거 합니다.  액세스 제어 화면에서 필터를 사용 하 여 제거할 역할 할당을 찾습니다.  역할 할당을 확인 한 다음 **액세스 권한 제거** 를 선택 합니다.   

![역할 할당을 삭제 하 여 액세스 권한 제거](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

역할 할당에 대 한 변경 내용은 적용 되는 데 2-5 분이 소요 됩니다.   

## <a name="next-steps"></a>다음 단계

[일반적인 작업을 수행 하는 데 필요한 Synapse RBAC 역할 이해](./synapse-workspace-understand-what-role-you-need.md)
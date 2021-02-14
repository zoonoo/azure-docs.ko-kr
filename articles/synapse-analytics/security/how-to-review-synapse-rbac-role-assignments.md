---
title: Synapse Studio에서 Synapse RBAC 역할 할당을 검토 하는 방법
description: 이 문서에서는 Synapse Studio를 사용 하 여 Synapse RBAC 역할 할당을 검토 하는 방법을 설명 합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102158"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Synapse RBAC 역할 할당을 검토 하는 방법

Synapse RBAC 역할은 사용자, 그룹 및 기타 보안 주체에 게 사용 권한을 할당 하 여 Synapse 리소스에 대 한 액세스 및 사용을 가능 하 게 하는 데 사용 됩니다.  [자세한 정보](./synapse-workspace-synapse-rbac.md)

이 문서에서는 작업 영역에 대 한 현재 역할 할당을 검토 하는 방법을 설명 합니다.

모든 Synapse RBAC 역할을 사용 하 여 액세스할 수 없는 개체에 대 한 할당을 포함 하 여 모든 범위에 대 한 Synapse RBAC 역할 할당을 나열할 수 있습니다. Synapse 관리자만 Synapse RBAC 액세스 권한을 부여할 수 있습니다.  

>[!Note]
>게스트 사용자 (다른 AD 테 넌 트의 사용자)는 Synapse 관리자 역할이 할당 된 경우에도 역할 할당을 보거나 관리할 수 없습니다.    

## <a name="open-synapse-studio"></a>Synapse Studio 열기  

역할 할당을 검토 하려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택 합니다. 

![작업 영역에 로그인](./media/common/login-workspace.png) 
 
 작업 영역을 연 후 왼쪽에서 **관리** 허브를 선택한 다음 **보안** 섹션을 확장 하 고 **액세스 제어** 를 선택 합니다. 

 ![왼쪽의 보안 섹션에서 Access Control 선택](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>작업 영역 역할 할당 검토

액세스 제어 화면에는 작업 영역에 대 한 모든 현재 역할 할당이 나열 되며 역할별 그룹화 됩니다. 각 할당에는 보안 주체 이름, 보안 주체 유형, 역할 및 해당 범위가 포함 됩니다.

![Access Control 화면](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

보안 주체가 서로 다른 범위에서 동일한 역할을 할당 하는 경우 각 범위 마다 하나씩, 주 서버에 대 한 여러 할당이 표시 됩니다.  

역할이 보안 그룹에 할당 된 경우 그룹에 명시적으로 할당 된 역할이 있지만 부모 그룹에서 상속 된 역할은 표시 되지 않습니다.  

사용자 이름 또는 전자 메일을 기준으로 목록을 필터링 하 고 개체 유형, 역할 및 범위를 선택적으로 필터링 할 수 있습니다. 사용자에 게 할당 된 역할을 보려면 이름 필터에 이름 또는 전자 메일 별칭을 입력 합니다. Synapse 관리자만 역할을 변경할 수 있습니다.

>[!Important] 
>역할이 할당 된 그룹의 멤버를 직접 또는 간접적으로 사용 하는 경우에는 표시 되지 않는 사용 권한이 있을 수 있습니다.

>[!tip]
>Azure Portal에서 Azure Active Directory를 사용 하 여 그룹 멤버 자격을 찾을 수 있습니다.  

새 작업 영역을 만드는 경우 작업 영역 MSI 서비스 주체는 작업 영역 범위에서 Synapse Administrator 역할을 자동으로 지정 합니다.

## <a name="next-steps"></a>다음 단계

[SYNAPSE RBAC 역할 할당을 관리 하는 방법](./how-to-manage-synapse-rbac-role-assignments.md)에 대해 알아봅니다.

[특정 작업을 수행 하는 데 필요한 역할](./synapse-workspace-understand-what-role-you-need.md) 알아보기
---
title: Synapse Studio에서 Synapse RBAC 역할 할당을 검토하는 방법
description: 이 문서에서는 Synapse Studio를 사용하여 Synapse RBAC 역할 할당을 검토하는 방법을 설명합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: acc1ed8cece087628f6f8deb41696acefe124874
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110784037"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Synapse RBAC 역할 할당을 검토하는 방법

Synapse RBAC 역할은 사용자, 그룹 및 기타 보안 주체가 사용 권한을 할당하여 Synapse 리소스에 대한 액세스 및 사용을 가능하게 하는 데 사용됩니다.  [자세히 알아보기](./synapse-workspace-synapse-rbac.md)

이 문서에서는 작업 영역에 대한 현재 역할 할당을 검토하는 방법을 설명합니다.

Synapse RBAC 역할을 사용하여 액세스할 수 없는 개체에 대한 할당을 포함하여 모든 범위의 Synapse RBAC 역할 할당을 나열할 수 있습니다. Synapse 관리자만 Synapse RBAC 액세스 권한을 부여할 수 있습니다.  

>[!Note]
> 게스트 사용자(다른 AD 테넌트의 사용자)도 Synapse 관리자 역할이 할당된 후 역할 할당을 보고 관리할 수 있습니다.    

## <a name="open-synapse-studio"></a>Synapse Studio 열기  

역할 할당을 검토하려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다. 

![작업 영역에 로그인](./media/common/login-workspace.png) 
 
 작업 영역을 연 후 왼쪽에서 **관리** 허브를 선택한 다음 **보안** 섹션을 확장하고 **액세스 제어** 를 선택합니다. 

 ![왼쪽의 보안 섹션에서 액세스 제어 선택](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>작업 영역 역할 할당 검토

액세스 제어 화면에는 작업 영역에 대한 현재 모든 역할 할당이 나열되며 역할별로 그룹화됩니다. 각 할당에는 보안 주체 이름, 보안 주체 유형, 역할 및 해당 범위가 포함됩니다.

![액세스 제어 화면](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

보안 주체가 서로 다른 범위에서 동일한 역할을 할당하는 경우, 각 범위마다 하나씩, 주 서버에 대한 여러 할당이 표시됩니다.  

보안 그룹에 역할이 할당된 경우, 부모 그룹에서 상속된 역할이 아니라 그룹에 명시적으로 할당된 역할이 표시됩니다.  

사용자 이름 또는 메일을 기준으로 목록을 필터링하고, 개체 유형, 역할 및 범위를 선택적으로 필터링할 수 있습니다. 자신에게 할당된 역할을 보려면 이름 필터에 이름 또는 메일 별칭을 입력합니다. Synapse 관리자만 역할을 변경할 수 있습니다.

>[!Important] 
>직접 또는 간접적으로 역할이 할당된 그룹의 멤버라면 표시되지 않는 사용 권한이 있을 수 있습니다.

>[!tip]
>Azure Portal에서 Azure Active Directory를 사용하여 그룹 멤버 자격을 찾을 수 있습니다.  

새 작업 영역을 만드는 경우, 작업 영역 범위에서 Synapse 관리자 역할이 자신과 작업 영역 MSI 서비스 주체에 자동으로 지정됩니다.

## <a name="next-steps"></a>다음 단계

[Synapse RBAC 역할 할당을 관리하는 방법](./how-to-manage-synapse-rbac-role-assignments.md) 알아보기.

[특정 작업을 수행하는 데 필요한 역할](./synapse-workspace-understand-what-role-you-need.md) 알아보기
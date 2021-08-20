---
title: '빠른 시작: 관리자 추가 시작'
description: 이 자습서에서는 작업 영역에 다른 관리자를 추가하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7a03715a89b5319e341d1704719b020e1b61ef1d
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219423"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Synapse 작업 영역에 관리자 추가

이 자습서에서는 Synapse 작업 영역에 관리자를 추가하는 방법을 알아봅니다. 이 사용자는 작업 영역을 완전히 제어할 수 있습니다.

## <a name="overview"></a>개요

지금까지 시작 가이드에서는 작업 영역에서 *사용자* 가 수행하는 작업에 초점을 두었습니다. 1단계에서 작업 영역을 만들었으므로 Synapse 작업 영역의 관리자가 됩니다. 이제 다른 사용자 Ryan(`ryan@contoso.com`)을 관리자로 만듭니다. 작업이 완료되면 Ryan은 작업 영역에서 수행할 수 있는 모든 작업을 수행할 수 있습니다.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: 작업 영역에 대한 소유자 역할

1. Azure Portal을 열고 Synapse 작업 영역을 엽니다.
1. 왼쪽에서 **액세스 제어(IAM)** 를 선택합니다.
1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | 소유자 |
    | 다음에 대한 액세스 할당 | USER |
    | 멤버 | ryan@contoso.com |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. **저장** 을 선택합니다. 
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: 작업 영역에 대한 Synapse 관리자 역할

작업 영역에서 `ryan@contoso.com`을 Synapse RBAC **Synapse 관리자** 역할에 할당합니다.

1. Synapse Studio에서 작업 영역을 엽니다.
1. 왼쪽에서 **관리** 를 선택하여 관리 허브를 엽니다.
1. **보안** 에서 **액세스 제어** 를 선택합니다.
1. **추가** 를 선택합니다.
1. **작업 영역** 으로 설정된 **범위** 를 그대로 둡니다.
1. `ryan@contoso.com`을 **Synapse 관리자** 역할에 추가합니다. 
1. 그런 다음, **적용** 을 선택합니다.
 
## <a name="azure-rbac-role-assignments-on-the-workspaces-primary-storage-account"></a>Azure RBAC: 작업 영역의 기본 스토리지 계정에 대한 역할 할당

1. Azure Portal에서 작업 영역의 기본 스토리지 계정을 엽니다.
1. 왼쪽에서 **액세스 제어(IAM)** 를 선택합니다.
1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 1 | 소유자 |
    | 역할 2| Azure Storage Blob 데이터 기여자|
    | 다음에 대한 액세스 할당 | USER |
    | 멤버 | ryan@contoso.com |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="dedicated-sql-pools-db_owner-role"></a>전용 SQL 풀: db_owner 역할

`ryan@contoso.com`을 작업 영역에서 각 전용 SQL 풀의 **db_owner** 에 할당합니다.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics 시작](get-started.md)
* [작업 영역 만들기](quickstart-create-workspace.md)
* [서버리스 SQL 풀 사용](quickstart-sql-on-demand.md)

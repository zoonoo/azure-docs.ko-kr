---
title: Azure Resource Mover를 사용하여 지역 간에 Azure SQL 리소스 이동
description: Azure Resource Mover를 사용하여 Azure SQL 리소스를 다른 지역으로 이동하는 방법을 알아봅니다.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e3e2c9aa42ff3189e90f57d7c6e92b2a71f46639
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061610"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>자습서: Azure SQL Database 리소스를 다른 지역으로 이동

이 자습서에서는 [Azure Resource Mover](overview.md)를 사용하여 Azure SQL 데이터베이스 및 탄력적 풀을 다른 Azure 지역으로 이동하는 방법에 대해 알아봅니다.

> [!NOTE]
> Azure Resource Mover는 현재 미리 보기로 제공됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 필수 구성 요소 및 요구 사항을 확인합니다.
> * 이동하려는 리소스를 선택합니다.
> * 리소스 종속성을 확인합니다.
> * SQL Server를 준비하고 대상 지역으로 이동합니다.
> * 데이터베이스 및 탄력적 풀을 준비하고 이동합니다.
> * 이동을 취소할지 아니면 커밋할지를 결정합니다. 
> * 이동 후 필요에 따라 원본 지역에서 리소스를 제거합니다. 

> [!NOTE]
> 이 자습서에서는 시나리오를 시도하기 위한 가장 빠른 경로를 보여 주고 기본 옵션을 사용합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다. 그런 다음 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="prerequisites"></a>필수 구성 요소

-  이동하려는 리소스가 포함된 구독에 대한 *소유자* 액세스 권한이 있는지 확인합니다.
    - Azure 구독에서 특정 원본 및 대상 쌍에 대한 리소스를 처음 추가하는 경우 Resource Mover를 통해 구독에서 신뢰할 수 있는 [시스템이 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)(이전에는 MSI(관리되는 서비스 ID)라고 함)를 만듭니다.
    - ID를 만들고 필요한 역할(원본 구독의 기여자 또는 사용자 액세스 관리자)을 할당하려면 구독에 대한 *소유자* 권한이 리소스를 추가하는 데 사용하는 계정에 필요합니다. Azure 역할에 대해 [자세히 알아보세요](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).
- 구독에는 대상 지역에서 이동하는 리소스를 만드는 데 충분한 할당량이 필요합니다. 할당량이 없는 경우 [추가 제한](/azure/azure-resource-manager/management/azure-subscription-service-limits)을 요청합니다.
- 리소스를 이동하는 대상 지역과 관련된 가격 책정 및 요금을 확인합니다. [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 사용자를 도와줍니다.
    

## <a name="check-sql-requirements"></a>SQL 요구 사항 확인

1. 다른 지역으로 이동하는 데 지원되는 데이터베이스/탄력적 풀 기능을 [확인](support-matrix-move-region-sql.md)합니다.
2. 대상 지역에서 각 원본 서버에 대한 대상 서버를 만듭니다. [자세히 알아보기](/azure/azure-sql/database/active-geo-replication-security-configure#how-to-configure-logins-and-users).
4. 데이터베이스가 TDE(투명한 데이터 암호화)를 사용하여 암호화되어 있고 Azure Key Vault에서 사용자 고유의 암호화 키를 사용하는 경우 키 자격 증명 모음을 다른 지역으로 [이동하는 방법을 알아봅니다](../key-vault/general/move-region.md).
5. SQL 데이터 동기화를 사용하도록 설정되면 멤버 데이터베이스 이동이 지원됩니다. 이동 후에는 새 대상 데이터베이스에 대한 SQL 데이터 동기화를 설정해야 합니다.
6. Advanced Data Security 설정을 제거한 후에 이동합니다. 이동 후 대상 지역의 SQL Server 수준에서 [설정을 구성합니다](/azure/sql-database/sql-database-advanced-data-security).
7. 감사를 사용하도록 설정되면 이동 후 정책이 기본값으로 다시 설정됩니다. 이동 후 [감사를 다시 설정합니다](/azure/sql-database/sql-database-auditing).
7. 원본 데이터베이스에 대한 백업 보존 정책은 대상 데이터베이스로 전달됩니다. 이동 후 설정을 수정하는 방법에 대해 [자세히 알아보세요](/azure/sql-database/sql-database-long-term-backup-retention-configure ).
8. 서버 수준 방화벽 규칙을 제거한 후에 이동합니다. 데이터베이스 수준 방화벽 규칙은 이동 중에 원본 서버에서 대상 서버로 복사됩니다. 이동 후 대상 지역에서 SQL Server에 대한 [방화벽 규칙을 설정합니다](/azure/sql-database/sql-database-server-level-firewall-rule).
9. 자동 튜닝 설정을 제거한 후에 이동합니다. 이동 후 [자동 튜닝을 다시 설정합니다](/azure/sql-database/sql-database-automatic-tuning-enable).
10. 데이터베이스 경고 설정을 제거한 후에 이동합니다. 이동 후 [다시 설정합니다](/azure/sql-database/sql-database-insights-alerts-portal).
    
## <a name="select-resources"></a>리소스 선택

이동하려는 리소스를 선택합니다.

- 선택한 원본 지역의 모든 리소스 그룹에서 지원되는 리소스 종류를 선택할 수 있습니다.
- 리소스를 원본 지역과 동일한 구독의 대상 지역으로 이동합니다. 구독을 변경하려면 리소스를 이동한 후에 변경할 수 있습니다.

1. Azure Portal에서 *리소스 이동기*를 검색합니다. 그런 다음, **서비스** 아래에서 **Azure Resource Mover**를 선택합니다.

     ![Azure Portal의 리소스 이동기에 대한 검색 결과](./media/tutorial-move-region-sql/search.png)

2. **개요**에서 **시작**을 클릭합니다.

    ![다른 지역으로 이동할 리소스를 추가하는 단추](./media/tutorial-move-region-sql/get-started.png)

3. **리소스 이동** > **원본 + 대상**에서 원본 구독과 지역을 선택합니다.
4. **대상**에서 리소스를 이동하려는 지역을 선택합니다. 그런 다음 **다음**을 클릭합니다.

    ![원본 및 대상 지역을 선택하는 페이지](./media/tutorial-move-region-sql/source-target.png)

6. **이동할 리소스**에서 **리소스 선택**을 클릭합니다.
7. **리소스 선택**에서 리소스를 선택합니다. 이동에 지원되는 리소스만 추가할 수 있습니다. **완료**를 클릭합니다.

    ![이동할 SQL 리소스를 선택하는 페이지](./media/tutorial-move-region-sql/select-resources.png)

8. **이동할 리소스**에서 **다음**을 클릭합니다.

9. **검토 + 추가**에서 원본 및 대상 설정을 확인합니다. 이동에 대한 메타데이터가 저장을 목적으로 생성된 메타데이터 지역의 리소스 그룹에 저장된다는 것을 이해해야 합니다.


    ![설정을 검토하고 이동을 계속하는 페이지](./media/tutorial-move-region-sql/review.png)

10. **계속**을 클릭하여 리소스 추가를 시작합니다.
11. 추가 프로세스가 성공적으로 완료되면 알림 아이콘에서 **이동할 리소스 추가 중**을 클릭합니다.
12. 알림을 클릭한 후 **지역 간** 페이지에서 리소스를 검토합니다.


> [!NOTE]
> 
> - 이제 SQL Server는 *수동 할당 보류 중* 상태입니다.
> - 추가된 다른 리소스는 *준비 보류 중* 상태입니다.
> - 이동 컬렉션에서 리소스를 제거하려는 경우 이를 수행하는 방법은 이동 프로세스의 위치에 따라 달라집니다. [자세히 알아보기](remove-move-resources.md).

## <a name="resolve-dependencies"></a>종속성 오류 해결


1. **지역 간**에서 리소스의 **문제** 열에 *종속성 유효성 검사* 메시지가 표시되는 경우 **종속성 유효성 검사** 단추를 클릭합니다. 유효성 검사 프로세스가 시작됩니다.
2. 종속성이 있으면 **종속성 추가**를 클릭합니다.

    ![종속성을 추가하는 단추](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. **종속성 추가**에서 종속 리소스 > **종속성 추가**를 차례로 선택합니다. 알림에서 진행률을 모니터링합니다.

4. 필요한 경우 추가 종속성을 추가하고 종속성의 유효성을 다시 검사합니다. 

5. **지역 간** 페이지에서 리소스가 이제 문제 없이 *준비 보류 중* 상태인지 확인합니다.

    ![준비 보류 중 상태인 리소스를 보여 주는 페이지](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>SQL Server 이동

대상 지역에서 대상 SQL Server를 할당하고 이동을 커밋합니다.

### <a name="assign-a-target-sql-server"></a>대상 SQL Server 할당

1. **지역 간**의 SQL Server 리소스에 대한 **대상 구성** 열에서 **리소스 할당 안 됨**을 클릭합니다.
2. 대상 지역에서 기존 SQL Server 리소스를 선택합니다. 
    
    ![이동 커밋 보류 중으로 설정된 SQL Server 상태를 보여 주는 항목](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> 원본 SQL Server 상태가 *이동 커밋 보류 중*으로 변경됩니다. 

### <a name="commit-the-sql-server-move"></a>SQL Server 이동 커밋

1. **지역 간**에서 SQL Server를 선택한 다음, **이동 커밋**을 클릭합니다.
2. **리소스 커밋**에서 **커밋**을 클릭합니다.

    ![SQL Server 이동을 커밋하는 페이지](./media/tutorial-move-region-sql/commit-sql-server.png)

3. 알림 표시줄에서 이동 진행률을 추적합니다.

> [!NOTE]
> 커밋이 완료되면 이제 SQL Server는 *원본 삭제 보류 중* 상태입니다.


## <a name="prepare-resources-to-move"></a>이동할 리소스 준비

원본 SQL Server가 이동되면 다른 리소스를 이동하도록 준비할 수 있습니다.

## <a name="prepare-an-elastic-pool"></a>탄력적 풀 준비

1. **지역 간**에서 원본 탄력적 풀(이 연습의 경우 demo-test1-elasticpool)을 선택한 다음, **준비**를 클릭합니다.

    ![리소스를 준비하는 단추](./media/tutorial-move-region-sql/prepare-elastic.png)

2. **리소스 준비**에서 **준비**를 클릭합니다.
3. 준비 프로세스에 성공했다는 알림이 표시되면 **새로 고침**을 클릭합니다.

> [!NOTE]
> 이제 탄력적 풀은 *이동 시작 보류 중* 상태입니다.

## <a name="prepare-a-single-database"></a>단일 데이터베이스 준비

1. **지역 간**에서 단일 데이터베이스(탄력적 풀에 없음)를 선택한 다음, **준비**를 클릭합니다.

    ![선택한 리소스를 준비하는 단추](./media/tutorial-move-region-sql/prepare-db.png)

2. **리소스 준비**에서 **준비**를 클릭합니다.
3. 준비 프로세스에 성공했다는 알림이 표시되면 **새로 고침**을 클릭합니다.

> [!NOTE]
> 이제 데이터베이스는 *이동 시작 보류 중* 상태이고 대상 지역에 만들어졌습니다.


## <a name="move-the-pool-and-prepare-pool-databases"></a>풀 이동 및 풀 데이터베이스 준비

탄력적 풀에서 데이터베이스를 준비하려면 탄력적 풀이 *이동 커밋 보류 중* 상태여야 합니다. 이 상태로 전환하려면 풀 이동을 시작합니다.

#### <a name="initiate-move---elastic-pool"></a>이동 시작 - 탄력적 풀

1. **지역 간**에서 원본 탄력적 풀(이 연습의 경우 demo-test1-elasticpool)을 선택한 다음, **이동 시작**을 클릭합니다.
2. **리소스 이동**에서 **이동 시작**을 클릭합니다.

    
    ![탄력적 풀의 이동을 시작하는 단추](./media/tutorial-move-region-sql/initiate-elastic.png)

1. 알림 표시줄에서 이동 진행률을 추적합니다.
1. 성공적으로 이동되었다는 알림이 표시되면 **새로 고침**을 클릭합니다.

> [!NOTE]
> 이제 탄력적 풀은 *이동 커밋 보류 중* 상태입니다.

#### <a name="prepare-database"></a>데이터베이스 준비

1. **지역 간**에서 데이터베이스(이 연습의 경우 demo-test2-sqldb)를 선택한 다음, **준비**를 클릭합니다.
2. **리소스 준비**에서 **준비**를 클릭합니다.

    ![탄력적 풀에서 데이터베이스를 준비하는 단추](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

준비하는 동안 대상 데이터베이스가 대상 지역에 만들어지고 데이터 복제가 시작됩니다. 준비 후 데이터베이스는 *이동 시작 보류 중* 상태입니다. 

![탄력적 풀에서 선택한 데이터베이스를 준비하는 단추](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>데이터베이스 이동

데이터베이스 이동을 시작합니다.
1. **지역 간**에서 **이동 시작 보류 중** 상태인 리소스를 선택합니다. 그런 다음, **이동 시작**을 클릭합니다.
2. **리소스 이동**에서 **이동 시작**을 클릭합니다.

    ![이동을 시작하는 페이지](./media/tutorial-move-region-sql/initiate-move.png)

3. 알림 표시줄에서 이동 진행률을 추적합니다.

> [!NOTE]
> 이제 데이터베이스는 *이동 커밋 보류 중* 상태입니다.


## <a name="discard-or-commit"></a>취소 또는 커밋?

처음 이동한 후에는 이동을 커밋할지 아니면 취소할지를 결정할 수 있습니다. 

- **취소**: 테스트하는 중에 원본 리소스를 실제로 이동하지 않으려는 경우 이동을 취소할 수 있습니다. 이동이 취소되면 리소스가 **이동 시작 보류 중** 상태로 돌아갑니다.
- **커밋**: 커밋은 대상 지역으로의 이동을 완료합니다. 커밋 후 원본 리소스는 **원본 삭제 보류 중** 상태가 되며 삭제 여부를 결정할 수 있습니다.


## <a name="discard-the-move"></a>이동 취소 

다음과 같이 이동을 취소할 수 있습니다.

1. **지역 간**에서 **이동 커밋 보류 중** 상태인 리소스를 선택하고, **이동 취소**를 클릭합니다.
2. **이동 취소**에서 **취소**를 클릭합니다.
3. 알림 표시줄에서 이동 진행률을 추적합니다.


> [!NOTE]
> - 리소스가 삭제되면 *이동 시작 보류 중* 상태가 됩니다.
> - 탄력적 풀만 있는 경우 취소가 진행되고 대상 지역에 만든 탄력적 풀이 삭제됩니다.
> - 연결된 데이터베이스가 있고 *이동 커밋 보류 중* 상태인 탄력적 풀이 있는 경우 탄력적 풀을 삭제할 수 없습니다.
> - SQL 데이터베이스를 취소하더라도 대상 지역 리소스는 삭제되지 않습니다. 

취소 후 이동을 다시 시작하려면 SQL 데이터베이스 또는 탄력적 풀을 선택하고 이동을 다시 시작합니다.


## <a name="commit-the-move"></a>이동 커밋

다음과 같이 데이터베이스 및 탄력적 풀의 이동을 완료합니다.


1. SQL Server가 *원본 삭제 보류 중* 상태인지 확인합니다.
2. 커밋하기 전에 데이터베이스 연결 문자열을 대상 지역으로 업데이트합니다.
3. **지역 간**에서 SQL 리소스를 선택한 다음, **이동 커밋**을 클릭합니다.
4. **리소스 커밋**에서 **커밋**을 클릭합니다.

    ![이동 커밋](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. 알림 표시줄에서 커밋 진행률을 추적합니다.


> [!NOTE]
> 커밋 프로세스 중에 SQL 데이터베이스에 대한 약간의 가동 중지 시간이 발생합니다.
> 이제 커밋된 데이터베이스 및 탄력적 풀은 *원본 삭제 보류 중* 상태입니다.
> 커밋 후 대상 데이터베이스에서 방화벽 규칙, 정책 및 경고를 포함한 데이터베이스 관련 설정을 업데이트합니다.


## <a name="delete-source-resources-after-commit"></a>커밋 후 원본 리소스 삭제

이동 후 필요에 따라 원본 지역에서 리소스를 삭제할 수 있습니다. 

1. **지역 간**에서 삭제하려는 각 원본 리소스의 이름을 클릭합니다.
2. 각 리소스에 대한 속성 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure SQL 데이터베이스를 다른 Azure 지역으로 이동했습니다.
> * Azure SQL 탄력적 풀을 다른 지역으로 이동했습니다.

이제 Azure VM을 다른 지역으로 이동하려고 합니다.

> [!div class="nextstepaction"]
> [Azure VM 이동](./tutorial-move-region-virtual-machines.md)

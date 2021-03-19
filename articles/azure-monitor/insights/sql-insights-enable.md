---
title: SQL insights 사용
description: Azure Monitor에서 SQL 정보 사용
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 5ab51fc4ea64dfd678f5c9acfc80b5e380782153
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609875"
---
# <a name="enable-sql-insights-preview"></a>SQL insights 사용 (미리 보기)
이 문서에서는 sql [정보](sql-insights-overview.md) 를 사용 하 여 sql 배포를 모니터링 하는 방법을 설명 합니다. SQL 배포에 연결 하 고 Dmv (동적 관리 뷰)를 사용 하 여 모니터링 데이터를 수집 하는 Azure 가상 머신에서 모니터링이 수행 됩니다. 모니터링 프로필을 사용 하 여 수집 되는 데이터 집합 및 수집 빈도를 제어할 수 있습니다.

## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
SQL insights는 하나 이상의 [Log Analytics 작업 영역](../logs/data-platform-logs.md#log-analytics-workspaces)에 데이터를 저장 합니다.  SQL Insights를 사용 하도록 설정 하려면 먼저 [작업 영역을 만들거나 기존 작업 영역](../logs/quick-create-workspace.md) 을 선택 해야 합니다. 단일 작업 영역을 여러 모니터링 프로필에 사용할 수 있지만 작업 영역 및 프로필은 동일한 Azure 지역에 있어야 합니다. SQL insights의 기능을 사용 하도록 설정 하 고 액세스 하려면 작업 영역에 [Log Analytics 참가자 역할이](../logs/manage-access.md) 있어야 합니다. 

## <a name="create-monitoring-user"></a>모니터링 사용자 만들기 
모니터링할 SQL 배포에 대 한 사용자가 필요 합니다. 다른 유형의 SQL 배포에 대해서는 아래 절차를 따르세요.

### <a name="azure-sql-database"></a>Azure SQL 데이터베이스
Azure Portal에서 [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 또는 [쿼리 편집기 (미리 보기)](../../azure-sql/database/connect-query-portal.md) 를 사용 하 여 Azure SQL Database를 엽니다.

다음 스크립트를 실행 하 여 필요한 권한이 있는 사용자를 만듭니다. 사용자를 password로 바꾸고 *mystrongpassword* 을 *사용자* 이름으로 바꿉니다.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Telegraf 사용자 스크립트를 만듭니다." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

사용자가 만들어졌는지 확인 합니다.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Telegraf 사용자 스크립트를 확인 합니다." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Azure SQL Managed Instance에 로그인 하 고 [SSMS](../../azure-sql/database/connect-query-ssms.md) 또는 유사한 도구를 사용 하 여 다음 스크립트를 실행 하 고 필요한 권한으로 모니터링 사용자를 만듭니다. 사용자를 password로 바꾸고 *mystrongpassword* 을 *사용자* 이름으로 바꿉니다.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
SQL Server를 실행 하는 Azure 가상 머신에 로그인 하 고 [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 또는 유사한 도구를 사용 하 여 다음 스크립트를 실행 하 고 필요한 권한으로 모니터링 사용자를 만듭니다. 사용자를 password로 바꾸고 *mystrongpassword* 을 *사용자* 이름으로 바꿉니다.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Azure 가상 머신 만들기 
SQL을 모니터링 하기 위해 데이터를 수집 하는 데 사용 되는 Azure virtual machines를 하나 이상 만들어야 합니다.  

> [!NOTE]
>  [모니터링 프로필](#create-sql-monitoring-profile) 은 모니터링 하려는 다양 한 유형의 SQL에서 수집할 데이터를 지정 합니다. 각 모니터링 가상 컴퓨터에는 하나의 모니터링 프로필만 연결할 수 있습니다. 여러 모니터링 프로필이 필요한 경우 각각에 대 한 가상 컴퓨터를 만들어야 합니다.

### <a name="azure-virtual-machine-requirements"></a>Azure 가상 머신 요구 사항
Azure 가상 컴퓨터에는 다음과 같은 요구 사항이 있습니다.

- 운영 체제: Ubuntu 18.04 
- 권장 되는 Azure virtual machine 크기: Standard_B2s (cpu 2 개, GiB 메모리 4 개) 
- 지원 되는 지역: [Azure Monitor 에이전트에서 지 원하는 모든 지역](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Standard_B2s (cpu 2 개, GiB 메모리 4 개) 가상 머신 크기는 최대 100 개의 연결 문자열을 지원 합니다. 단일 가상 머신에 100 개 이상의 연결을 할당 해서는 안 됩니다.

가상 컴퓨터는 모니터링 데이터를 수집 하는 네트워크 연결을 만들 수 있도록 SQL 시스템과 동일한 VNET에 배치 해야 합니다. 모니터링 가상 컴퓨터를 사용 하 여 Azure 가상 컴퓨터 또는 Azure Managed Instance에서 실행 되는 SQL을 모니터링 하는 경우 SQL server 모니터링을 위한 공용 네트워크 끝점을 제공할 필요가 없도록 응용 프로그램 보안 그룹 또는 해당 리소스와 동일한 가상 네트워크에 모니터링 가상 컴퓨터를 배치 하는 것이 좋습니다. 

## <a name="configure-network-settings"></a>네트워크 설정 구성
SQL의 각 유형은 모니터링 가상 머신이 SQL에 안전 하 게 액세스할 수 있는 방법을 제공 합니다.  다음 섹션에서는 SQL의 유형을 기반으로 하는 옵션을 설명 합니다.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

[자습서-Azure 개인 끝점을 사용 하 여 AZURE SQL server에 연결-Azure Portal](../../private-link/tutorial-private-endpoint-sql-portal.md) 데이터베이스에 액세스 하는 데 사용할 수 있는 개인 끝점을 설정 하는 방법에 대 한 예제를 제공 합니다.  이 방법을 사용 하는 경우 모니터링 가상 컴퓨터가 개인 끝점에 사용 되는 것과 동일한 VNET 및 서브넷에 있는지 확인 해야 합니다.  그런 다음 아직 수행 하지 않은 경우 데이터베이스에서 개인 끝점을 만들 수 있습니다. 

[방화벽 설정을](../../azure-sql/database/firewall-configure.md) 사용 하 여 SQL Database에 대 한 액세스를 제공 하는 경우 모니터링 가상 컴퓨터의 공용 IP 주소에서 액세스할 수 있도록 방화벽 규칙을 추가 해야 합니다. 포털의 **Azure SQL Database 개요** 페이지에서 방화벽 설정에 액세스할 수 있습니다. 

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="서버 방화벽 설정" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="방화벽 설정." lightbox="media/sql-insights-enable/firewall-settings.png":::

### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 

모니터링 가상 머신이 SQL MI 리소스와 동일한 VNet에 있는 경우 [동일한 vnet 내부에 연결](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet)을 참조 하세요. 모니터링 가상 컴퓨터가 SQL MI 리소스와 다른 VNet에 있는 경우 [다른 vnet 내부에 연결](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)을 참조 하세요.


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Azure 가상 컴퓨터 및 Azure SQL 가상 컴퓨터  
모니터링 가상 컴퓨터가 SQL 가상 컴퓨터 리소스와 동일한 VNet에 있는 경우 [가상 네트워크 내에서 SQL Server에 연결을](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network)참조 하세요. 모니터링 가상 컴퓨터가 SQL 가상 컴퓨터 리소스와는 다른 VNet에 있는 경우  [인터넷을 통해 SQL Server에 연결을](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)참조 하세요.

## <a name="store-monitoring-password-in-key-vault"></a>Key Vault에 모니터링 암호 저장
SQL 사용자 연결 암호는 모니터링 프로필 연결 문자열에 직접 입력 하는 대신 Key Vault에 저장 해야 합니다.

SQL 모니터링을 위한 프로필을 설정 하는 경우 사용 하려는 Key Vault 리소스에 대해 다음 권한 중 하나가 필요 합니다.

- Microsoft.Authorization/roleAssignments/write 
- Microsoft. Authorization/roleAssignments/delete 권한 (예: 사용자 액세스 관리자 또는 소유자) 

지정한 Key Vault를 사용 하는 SQL Monitoring 프로필을 만드는 과정에서 새 액세스 정책이 자동으로 만들어집니다. Key Vault 네트워킹 설정에 *모든 네트워크에서 액세스 허용* 을 사용 합니다.


## <a name="create-sql-monitoring-profile"></a>SQL 모니터링 프로필 만들기
Azure Portal **Azure Monitor** 메뉴의 **Insights** 섹션에서 **sql (미리 보기)** 을 선택 하 여 sql insights를 엽니다. **새 프로필 만들기** 를 클릭합니다. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="새 프로필을 만듭니다." lightbox="media/sql-insights-enable/create-new-profile.png":::

이 프로필은 SQL 시스템에서 수집 하려는 정보를 저장 합니다.  다음에 대 한 특정 설정이 있습니다. 

- Azure SQL Database 
- Azure SQL Managed Instance 
- 가상 컴퓨터에서 실행 중인 SQL Server  

예를 들어, 데이터 수집 빈도, 수집할 데이터 및 데이터를 보낼 작업 영역에 대 한 다른 설정을 사용 하 여 *Sql 프로덕션* *이라는 이름의 프로필* 을 하나 만들 수 있습니다. 

프로필은 선택한 구독 및 리소스 그룹에 [데이터 수집 규칙](../agents/data-collection-rule-overview.md) 리소스로 저장 됩니다. 각 프로필에는 다음이 필요 합니다.

- 이름. 만든 후에는 편집할 수 없습니다.
- 위치입니다. Azure 지역입니다.
- 모니터링 데이터를 저장할 작업 영역을 Log Analytics 합니다.
- 수집할 sql 모니터링 데이터의 빈도 및 유형에 대 한 컬렉션 설정입니다.

> [!NOTE]
> 프로필의 위치는 모니터링 데이터를 전송 하려는 Log Analytics 작업 영역과 동일한 위치에 있어야 합니다.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="프로필 세부 정보입니다." lightbox="media/sql-insights-enable/profile-details.png":::

모니터링 프로필에 대 한 세부 정보를 입력 한 후 **모니터링 프로필 만들기** 를 클릭 합니다. 프로필을 배포 하는 데 최대 1 분 정도 걸릴 수 있습니다.  **모니터링 프로필** 콤보 상자에 나열 된 새 프로필이 표시 되지 않으면 새로 고침 단추를 클릭 합니다. 배포가 완료 되 면 표시 됩니다.  새 프로필을 선택 했으면 **프로필 관리** 탭을 선택 하 여 프로필에 연결할 모니터링 컴퓨터를 추가 합니다.

### <a name="add-monitoring-machine"></a>모니터링 컴퓨터 추가
**모니터링 컴퓨터 추가** 를 선택 하 여 상황에 맞는 패널을 열고 SQL 인스턴스를 모니터링 하 고 연결 문자열을 제공 하도록 설정할 가상 컴퓨터를 선택 합니다.

구독 및 모니터링 가상 컴퓨터의 이름을 선택 합니다. Key Vault를 사용 하 여 모니터링 사용자에 대 한 암호를 저장 하는 경우 이러한 암호를 사용 하 여 Key Vault 리소스를 선택 하 고 연결 문자열에 사용할 URL 및 암호 이름을 입력 합니다. 다른 SQL 배포에 대 한 연결 문자열을 식별 하는 방법에 대 한 자세한 내용은 다음 섹션을 참조 하십시오.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="모니터링 컴퓨터를 추가 합니다." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>연결 문자열 추가 
연결 문자열은 sql 정보를 사용 하 여 동적 관리 뷰를 실행 하기 위해 SQL에 로그인 할 때 사용 해야 하는 사용자 이름을 지정 합니다. Key Vault를 사용 하 여 모니터링 사용자에 대 한 암호를 저장 하는 경우 사용할 암호의 URL과 이름을 제공 합니다. 

연결 문자열은 각 SQL 리소스 유형에 따라 달라 집니다.

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
다음 형식으로 연결 문자열을 입력 합니다.

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

데이터베이스에 대 한 **연결 문자열** 메뉴 항목에서 세부 정보를 가져옵니다.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL 데이터베이스 연결 문자열" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

읽을 수 있는 보조 데이터베이스를 모니터링 하려면 연결 문자열에 키-값을 포함 `ApplicationIntent=ReadOnly` 합니다.


#### <a name="azure-virtual-machines-running-sql-server"></a>SQL Server를 실행 하는 Azure virtual machines 
다음 형식으로 연결 문자열을 입력 합니다.

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

모니터링 가상 머신이 동일한 VNET에 있는 경우 서버의 개인 IP 주소를 사용 합니다.  그렇지 않으면 공용 IP 주소를 사용 합니다. Azure SQL 가상 컴퓨터를 사용 하는 경우 리소스에 대 한 **보안** 페이지에서 여기에서 사용할 포트를 확인할 수 있습니다.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL 가상 컴퓨터 보안" lightbox="media/sql-insights-enable/sql-vm-security.png":::

읽을 수 있는 보조 데이터베이스를 모니터링 하려면 연결 문자열에 키-값을 포함 `ApplicationIntent=ReadOnly` 합니다.


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 
다음 형식으로 연결 문자열을 입력 합니다.

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
관리 되는 인스턴스의 **연결 문자열** 메뉴 항목에서 세부 정보를 가져옵니다.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance 연결 문자열" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

읽을 수 있는 보조 데이터베이스를 모니터링 하려면 연결 문자열에 키-값을 포함 `ApplicationIntent=ReadOnly` 합니다.



## <a name="profile-created"></a>프로필이 만들어짐 
SQL 배포에서 데이터를 수집 하도록 가상 컴퓨터를 구성 하려면 **모니터링 가상 컴퓨터 추가** 를 선택 합니다. **개요** 탭으로 돌아오지 않습니다.  몇 분 후에 상태 열이 "수집 중"으로 변경 되어야 합니다. 모니터링 하도록 선택한 시스템에 대 한 데이터가 표시 되어야 합니다.

데이터가 표시 되지 않으면 [SQL 정보 문제 해결](sql-insights-troubleshoot.md) 을 참조 하 여 문제를 식별 합니다. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="프로필이 만들어짐" lightbox="media/sql-insights-enable/profile-created.png":::

## <a name="next-steps"></a>다음 단계

- Sql insights가 사용 하도록 설정 된 후 제대로 작동 하지 않는 경우 [sql 정보 문제 해결](sql-insights-troubleshoot.md) 을 참조 하세요.

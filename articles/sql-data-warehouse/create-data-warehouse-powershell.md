---
title: '빠른 시작: Azure SQL Data Warehouse 만들기 - Azure Powershell | Microsoft Docs'
description: Azure Powershell을 사용하여 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터 웨어하우스를 빠르게 만듭니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 4/11/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d76f7ac6c8b60e2dec7d7d95cf419e1352b97f15
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545131"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure SQL 데이터 웨어하우스 만들기 및 쿼리

Azure PowerShell을 사용하여 Azure SQL Data Warehouse를 빠르게 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!NOTE]
> SQL Data Warehouse를 만들면 새로운 유료 서비스가 발생할 수 있습니다.  자세한 내용은 [SQL Data Warehouse 가격](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

사용 중인 구독을 보려면 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)을 실행합니다.

```powershell
Get-AzSubscription
```

기본 구독과는 다른 구독을 사용해야 할 경우 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)를 실행합니다.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>변수 만들기

이 빠른 시작의 스크립트에서 사용할 변수를 정의합니다.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>논리 서버 만들기

[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) 명령을 사용하여 [Azure SQL 논리 서버](../sql-database/sql-database-logical-servers.md)를 만듭니다. 논리 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다. 다음 예제에서는 관리자 사용자 이름이 `ServerAdmin`이고 암호가 `ChangeYourAdminPassword1`인 리소스 그룹에 임의로 이름이 지정된 서버를 생성합니다. 이러한 미리 정의된 값은 필요에 따라 바꿉니다.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>서버 방화벽 규칙 구성

[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) 명령을 사용하여 [Azure SQL 서버 수준 방화벽 규칙](../sql-database/sql-database-firewall-configure.md)을 만듭니다. 서버 수준 방화벽 규칙을 통해 외부 애플리케이션(예: SQL Server Management Studio 또는 SQLCMD 유틸리티)이 SQL Data Warehouse 서비스 방화벽을 통해 SQL Data Warehouse에 연결되도록 할 수 있습니다. 다음 예제에서 방화벽은 다른 Azure 리소스에 대해서만 열립니다. 외부 연결을 사용하려면 IP 주소를 사용자 환경에 적절한 주소로 변경합니다. 모든 IP 주소를 열려면 시작 IP 주소로 0.0.0.0을, 끝나는 IP 주소로 255.255.255.255를 사용합니다.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database 및 SQL Data Warehouse는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 1433 포트를 열지 않으면 Azure SQL 서버에 연결할 수 없습니다.
>


## <a name="create-a-data-warehouse"></a>데이터 웨어하우스 만들기
이 예제에서는 이전에 정의된 변수를 사용하여 데이터 웨어하우스를 만듭니다.  여기서는 서비스 목표를 데이터 웨어하우스를 저렴하게 시작하기 좋은 DW100c로 지정합니다. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

필수 매개 변수는 다음과 같습니다.

* **RequestedServiceObjectiveName**: 요청 중인 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)의 양입니다. 이 양을 늘리면 계산 비용이 증가합니다. 지원되는 값 목록에 대해서는 [메모리와 동시성 제한](memory-and-concurrency-limits.md)을 참조하세요.
* **DatabaseName**: 만들려는 SQL Data Warehouse의 이름입니다.
* **ServerName**: 만드는 데 사용할 서버의 이름입니다.
* **ResourceGroupName**: 사용 중인 리소스 그룹입니다. 구독에서 사용 가능한 리소스 그룹을 찾으려면 Get-AzureResource를 사용합니다.
* **Edition**: SQL Data Warehouse를 만들려면 "DataWarehouse"여야 합니다.

선택적 매개 변수는 다음과 같습니다.

- **CollationName**: 지정되지 않은 경우 기본 데이터 정렬은 SQL_Latin1_General_CP1_CI_AS입니다. 데이터베이스에서 데이터 정렬을 변경할 수 없습니다.
- **MaxSizeBytes**: 데이터베이스의 기본 최대 크기는 240TB입니다. 최대 크기로 인해 rowstore 데이터가 제한됩니다. 칼럼 데이터에는 무제한 스토리지가 있습니다.

매개 변수 옵션에 대한 자세한 내용은 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)를 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 

> [!TIP]
> 후속 빠른 시작 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>다음 단계

지금까지 데이터 웨어하우스를 만들고, 방화벽 규칙을 만들고, 데이터 웨어하우스에 연결하고, 몇 가지 쿼리를 실행했습니다. Azure SQL Data Warehouse에 대해 자세히 알아보려면 데이터 로드에 대한 자습서를 계속 진행하세요.
> [!div class="nextstepaction"]
>[SQL 데이터 웨어하우스로 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)

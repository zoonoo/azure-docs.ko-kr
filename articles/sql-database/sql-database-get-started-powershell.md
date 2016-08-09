<properties
    pageTitle="PowerShell 사용한 새 SQL 데이터베이스 설치 | Microsoft Azure"
    description="PowerShell을 사용하여 새 SQL 데이터베이스를 만드는 방법에 대해 알아봅니다. PowerShell cmdlet을 통해 일반적인 데이터베이스 설치 작업을 관리할 수 있습니다."
    keywords="새 sql 데이터베이스 만들기, 데이터베이스 설치"
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/09/2016"
    ms.author="sstein"/>

# PowerShell cmdlet을 사용하여 새 SQL 데이터베이스 만들기 및 일반적인 데이터베이스 설치 작업 수행하기


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C\#](sql-database-get-started-csharp.md)



PowerShell cmdlet을 사용하여 새 SQL 데이터베이스를 만드는 방법을 알아봅니다. \(탄력적 데이터베이스를 만드는 방법은 [PowerShell을 사용하여 새 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md)를 참조하세요.\)


[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]

## 데이터베이스 설치: 리소스 그룹, 서버, 방화벽 규칙 만들기

선택한 Azure 구독에 대해 cmdlet을 실행하도록 액세스하게 되면 다음 단계는 데이터베이스를 만들 서버가 포함된 리소스 그룹을 설정하는 것입니다. 다음 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다. **\(Get-AzureRmLocation \| Where-Object { $\_.Providers -eq "Microsoft.Sql" }\).Location**을 실행하여 유효한 위치의 목록을 가져옵니다.

다음 명령을 실행하여 새 리소스 그룹을 만듭니다.

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

성공적으로 새 리소스 그룹을 만든 후에 **ProvisioningState: 성공**이 표시됩니다.


### 서버 만들기

SQL 데이터베이스는 Azure SQL 데이터베이스 서버 내부에 만들어집니다. **New-AzureRMSqlServer**를 실행하여 새 서버를 만듭니다. *ServerName*을 서버 이름으로 바꿉니다. 이 이름은 모든 Azure SQL 데이터베이스 서버에 대해 고유해야 합니다. 이미 사용한 서버 이름인 경우 오류가 발생합니다. 또한 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있다는 점도 유의해야 합니다. 명령을 편집하여 선택한 유효한 위치를 사용할 수 있지만 이전 단계에서 만든 리소스 그룹에 사용한 동일한 위치를 사용해야 합니다.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

이 명령을 실행하면 사용자 이름과 암호를 묻는 메시지가 나타납니다. Azure 자격 증명을 입력하지 않습니다. 대신 새 서버에 만들려는 관리자 자격 증명이 될 사용자 이름과 암호를 입력해야 합니다.

서버가 성공적으로 생성된 후 서버 세부 정보가 표시됩니다.

### 서버에 대한 액세스를 허용할 수 있도록 서버 방화벽 규칙 구성

서버에 액세스할 수 있도록 방화벽 규칙을 설정합니다. 다음 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

규칙이 성공적으로 생성된 후 방화벽 규칙 세부 정보가 표시됩니다.

다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 추가하고 StartIpAddress와 EndIpAddress를 모두 0.0.0.0으로 설정합니다. 이렇게 하면 모든 Azure 구독에서 Azure 트래픽이 서버에 액세스할 수 있습니다.

자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요.


## 새 SQL 데이터베이스 만들기

리소스 그룹, 서버, 방화벽 규칙을 구성했으므로 이제 서버에 액세스할 수 있습니다.

다음 명령은 표준 서비스 계층에서 S1 성능 수준인 새\(비어 있는\) SQL 데이터베이스를 만듭니다.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


데이터베이스가 성공적으로 생성된 후 데이터베이스 세부 정보가 표시됩니다.

## 새 SQL 데이터베이스 PowerShell 스크립트 만들기

다음은 새 SQL 데이터베이스 PowerShell 스크립트입니다.

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"

    $ServerName = "uniqueservername"

    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"

    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"

    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp

    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel

    $SqlDatabase



## 다음 단계
새 SQL 데이터베이스를 만들고 기본 데이터베이스 설치 작업을 수행한 후 다음에 대한 준비가 됩니다.

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다](sql-database-connect-query-ssms.md).


## 추가 리소스

- [Azure SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0803_2016-->
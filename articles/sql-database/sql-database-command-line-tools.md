<properties 
	pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스 관리" 
	description="PowerShell을 사용한 Azure SQL 데이터베이스 관리." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="sstein"/>

# PowerShell을 사용하여 Azure SQL 데이터베이스 관리


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-manage-portal.md)
- [Transact-SQL(SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

이 항목에서는 많은 Azure SQL 데이터베이스 작업을 수행하기 위한 PowerShell 명령을 제공 합니다.

[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]


## 리소스 그룹 만들기

서버를 포함 하는 리소스 그룹을 만듭니다. 다음 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다.

유효한 Azure SQL 데이터베이스 서버 위치 목록을 보려면 다음 cmdlet을 실행합니다.

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

이미 리소스 그룹이 있다면 서버를 만드는 단계로 건너뛰거나, 아래 명령을 실행하여 새 리소스 그룹을 만들 수 있습니다.

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## 서버 만들기 

새 V12 서버를 만들려면 [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet를 사용합니다. Server12을 서버 이름으로 바꿉니다 서버 이름은 Azure SQL Server에 대해 고유해야 하며 서버 이름이 이미 사용 중인 경우 오류가 발생할 수 있습니다. 또한 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있다는 점도 유의해야 합니다. 서버가 성공적으로 생성된 후 서버 세부 정보와 PowerShell 프롬프트가 표시됩니다. 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

이 명령을 실행하면 **사용자 이름**과 **암호**를 묻는 창이 열립니다. 이는 Azure 자격 증명이 아니며, 새 서버에 만들려는 관리자 자격 증명이 될 사용자 이름과 암호를 입력해야 합니다.

## 서버 방화벽 규칙 만들기

서버에 액세스하기 위한 방화벽 규칙을 만들려면 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) 명령을 사용합니다. 다음 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다.

서버에서 다른 Azure 서비스에 대한 액세스를 허용해야 하는 경우, 특별한 방화벽 규칙을 추가하고 모든 Azure 트래픽이 서버에 액세스할 수 있도록 하는 **-AllowAllAzureIPs** 스위치를 추가합니다.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 참조하세요.

## SQL 데이터베이스 만들기

데이터베이스를 만들려면 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) 명령을 사용합니다. 데이터베이스를 만들려면 서버가 필요 합니다. 다음 예제에서는 TestDB12 라는 SQL 데이터베이스를 만듭니다. 데이터베이스는 표준 S1 데이터베이스로 생성 됩니다.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## SQL 데이터베이스의 성능 수준을 변경 합니다.

[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) 명령을 사용하여 데이터베이스 규모를 확장하거나 축소할 수 있습니다. 다음 예제에서는 표준 S3 수준의 현재 성능 수준에서 TestDB12 라는 SQL 데이터베이스를 조정 합니다.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## SQL 데이터베이스 삭제하기

[Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) 명령을 사용하여 SQL 데이터베이스를 삭제할 수 있습니다. 다음 예제에서는 TestDB12 라는 SQL 데이터베이스를 삭제 합니다.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## 서버 삭제

[Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) 명령을 사용하여 서버를 삭제할 수도 있습니다. 다음 예제에서는 server12 이라는 서버를 삭제 합니다.


>[AZURE.NOTE]  삭제 작업이 비동기이며 다소 시간이 걸릴 수도 있으므로 작업 확인이 예를 들어, 동일한 이름으로 새로운 서버 만들기와 같이 서버가 완전히 삭제되는 것을 전제로 하는 추가 작업을 수행하기 전에 완료되어야 합니다.


	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"




## 다음 단계

명령을 결합 하 고 자동화 합니다. 예를 들어 < and > 문자를 포함하여 따옴표 안의 모든 항목을 서버, 방화벽 규칙 및 데이터베이스를 만드는 데 사용할 값으로 바꿉니다.


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## 관련 정보

- [Azure SQL 데이터베이스 cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0713_2016-->
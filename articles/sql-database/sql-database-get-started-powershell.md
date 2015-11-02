<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스 만들기" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스 만들기" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="10/20/2015"
    ms.author="sstein"/>

# PowerShell을 사용하여 SQL 데이터베이스 만들기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## 개요

이 문서는 PowerShell을 사용하여 SQL 데이터베이스를 만드는 방법을 보여 줍니다.

> [AZURE.IMPORTANT]Azure PowerShell 1.0 Preview 릴리스부터는 Switch-AzureMode cmdlet을 더 이상 사용할 수 없으며 Azure ResourceManger 모듈에 있던 cmdlet은 이름이 바뀌었습니다. 이 문서의 예제에서는 새 PowerShell 1.0 Preview 명명 규칙을 사용합니다. 자세한 내용은 [Azure PowerShell에서 Switch-AzureMode 중단](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)을 참조하세요.


PowerShell cmdlet을 실행하려면 Azure PowerShell을 설치 및 실행해야 하고 Switch-AzureMode를 제거했기 때문에 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행하여 최신 Azure PowerShell을 다운로드하고 설치해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

- Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.


## 자격 증명 구성 및 구독 선택

이제 Azure 리소스 관리자 모듈을 실행하고 있으므로 SQL 데이터베이스를 만드는 데 필요한 모든 cmdlet에 액세스할 수 있습니다.

먼저 다음 cmdlet을 실행하도록 Azure 계정에 대한 액세스를 설정하해야 하며 자격 증명을 입력하는 로그인 화면이 나타납니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

	Add-AzureRMAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID가 필요합니다. 이는 이전 단계에서 복사하거나, 또는 구독이 여러 개일 경우 **Get-AzureRMSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독을 설정한 후 다음 cmdlet을 실행합니다.

	Select-AzureRMSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

성공적으로 **Select-AzureRMSubscription**을 실행한 후 PowerShell 프롬프트로 돌아갑니다. 둘 이상의 구독이 있는 경우 **Get-AzureRMSubscription**을 실행하고 사용하려는 구독이 **IsCurrent: True**를 표시하는지 확인할 수 있습니다.

## 리소스 그룹, 서버, 방화벽 규칙 만들기

이제 선택한 Azure 구독에 대한 cmdlet 실행에 대한 액세스가 가능하므로 다음 단계는 데이터베이스를 만들 서버가 포함된 리소스 그룹을 설정하는 것입니다. 다음 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다. **(Get-AzureRMLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations**를 실행하여 유효한 위치의 목록을 가져옵니다.

다음 명령을 실행하여 새 리소스 그룹을 만듭니다.

	New-AzureRMResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

성공적으로 새 리소스 그룹을 만든 후 **ProvisioningState: Succeeded**를 포함하는 화면에 정보가 표시됩니다.


### 서버 만들기 

SQL 데이터베이스는 Azure SQL 데이터베이스 서버 내부에서 만들어집니다. **New-AzureRMSqlServer**를 실행하여 새 서버를 만듭니다. ServerName을 사용자의 서버 이름으로 바꿉니다. 서버 이름은 모든 Azure SQL Server에 대해 고유해야 하며 서버 이름이 이미 사용 중인 경우 오류가 발생할 수 있습니다. 또한 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있다는 점도 유의해야 합니다. 선택한 모든 유효한 위치를 사용하도록 명령을 편집할 수는 있지만 이전 단계에서 만든 리소스 그룹에 사용한 동일한 위치를 사용해야 합니다.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

이 명령을 실행하면 **사용자 이름**과 **암호**를 묻는 창이 열립니다. 이는 Azure 자격 증명이 아니며, 새 서버에 만들려는 관리자 자격 증명이 될 사용자 이름과 암호를 입력해야 합니다.

서버가 성공적으로 생성된 후 서버 세부 정보가 표시됩니다.

### 서버에 대한 액세스를 허용할 수 있도록 서버 방화벽 규칙 구성

서버에 액세스할 수 있도록 방화벽 규칙을 설정합니다. 다음 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

규칙이 성공적으로 생성된 후 방화벽 규칙 세부 정보가 표시됩니다.

다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 추가하고 StartIpAddress와 EndIpAddress를 모두 0.0.0.0으로 설정합니다. 이렇게 하면 모든 Azure 구독에서 Azure 트래픽이 서버에 액세스할 수 있습니다.

자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요.


## SQL 데이터베이스 만들기

리소스 그룹, 서버, 방화벽 규칙을 구성했으므로 이제 서버에 액세스할 수 있습니다.

다음 명령은 S1 성능 수준으로 표준 서비스 계층에서 새(빈) SQL 데이터베이스를 만듭니다.


	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


데이터베이스가 성공적으로 생성된 후 데이터베이스 세부 정보가 표시됩니다.

## SQL 데이터베이스 PowerShell 스크립트 만들기

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
    
    
    Add-AzureRMAccount
    Select-AzureRMSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRMResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRMSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRMSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## 다음 단계

- [SQL Server Management Studio(SSMS)로 연결](sql-database-connect-to-database.md)


## 추가 리소스

- [Azure SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO4-->
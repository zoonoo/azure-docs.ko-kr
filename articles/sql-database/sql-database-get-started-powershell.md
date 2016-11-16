---
title: "PowerShell 사용한 새 SQL Database 설치 | Microsoft Docs"
description: "PowerShell을 사용하여 SQL 데이터베이스를 만드는 방법에 대해 알아봅니다. PowerShell cmdlet을 통해 일반적인 데이터베이스 설치 작업을 관리할 수 있습니다."
keywords: "새 sql 데이터베이스 만들기, 데이터베이스 설치"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87e52fe29f659577d7dc0c9661ebde2c1c475cfc


---
# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 SQL 데이터베이스 만들기 및 일반적인 데이터베이스 설치 작업 수행하기
> [!div class="op_single_selector"]
> * [Azure 포털](sql-database-get-started.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> * [C#](sql-database-get-started-csharp.md)
> 
> 

PowerShell cmdlet을 사용하여 SQL 데이터베이스를 만드는 방법을 알아봅니다. (탄력적 데이터베이스를 만드는 방법은 [PowerShell을 사용하여 새 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md)를 참조하세요.)

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>데이터베이스 설치: 리소스 그룹, 서버, 방화벽 규칙 만들기
선택한 Azure 구독에 대해 cmdlet을 실행하도록 액세스하게 되면 다음 단계는 데이터베이스를 만들 서버가 포함된 리소스 그룹을 설정하는 것입니다. 다음 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다. **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** 명령을 실행하여 유효한 위치의 목록을 가져옵니다.

다음 명령을 실행하여 리소스 그룹을 만듭니다.

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>서버 만들기
SQL 데이터베이스는 Azure SQL 데이터베이스 서버 내부에 만들어집니다. [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx)를 실행하여 서버를 만듭니다. 서버의 이름은 모든 Azure SQL 데이터베이스 서버에 대해 고유해야 합니다. 이미 사용한 서버 이름인 경우 오류가 발생합니다. 또한 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있다는 점도 유의해야 합니다. 명령을 편집하여 선택한 유효한 위치를 사용할 수 있지만 이전 단계에서 만든 리소스 그룹에 사용한 동일한 위치를 사용해야 합니다.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

이 명령을 실행하면 사용자 이름과 암호를 묻는 메시지가 나타납니다. Azure 자격 증명을 입력하지 않습니다. 대신, 사용자 이름 및 암호를 입력하여 서버 관리자로 만듭니다. 이 문서의 맨 아래에 있는 스크립트에서는 코드에서 서버 자격 증명을 설정하는 방법을 보여 줍니다.

서버가 성공적으로 생성된 후 서버 세부 정보가 표시됩니다.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>서버에 대한 액세스를 허용할 수 있도록 서버 방화벽 규칙 구성
서버에 액세스하려면 방화벽 규칙을 설정해야 합니다. [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

규칙이 성공적으로 생성된 후 방화벽 규칙 세부 정보가 표시됩니다.

다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 추가하고 StartIpAddress와 EndIpAddress를 모두 0.0.0.0으로 설정합니다. 이 규칙을 사용하면 Azure 구독의 Azure 트래픽이 서버에 액세스할 수 있습니다.

자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요.

## <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기
리소스 그룹, 서버, 방화벽 규칙을 구성했으므로 이제 서버에 액세스할 수 있습니다.

다음 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) 명령은 표준 서비스 계층에서 S1 성능 수준인 (비어 있는) SQL Database를 만듭니다.

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


데이터베이스가 성공적으로 생성된 후 데이터베이스 세부 정보가 표시됩니다.

## <a name="create-a-sql-database-powershell-script"></a>SQL 데이터베이스 PowerShell 스크립트 만들기
다음 PowerShell 스크립트는 SQL 데이터베이스 및 모든 종속 리소스를 만듭니다. 모든 `{variables}` 을 구독 및 리소스에 지정된 값으로 바꿉니다(값을 설정한 경우 **{}** 을 삭제함).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation

    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"

    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds

    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip

    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp


    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"

    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo


    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>다음 단계
SQL 데이터베이스를 만들고 기본 데이터베이스 설치 작업을 수행하면 다음을 수행할 준비가 되었습니다.

* [PowerShell을 사용하여 SQL Database 관리](sql-database-manage-powershell.md)
* [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>추가 리소스
* [Azure SQL Database cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO2-->



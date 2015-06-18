<properties 
	pageTitle="PowerShell 사용한 Azure SQL 데이터베이스 리소스 관리" 
	description="명령줄을 사용한 Azure SQL 데이터베이스 관리" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# PowerShell 사용한 Azure SQL 데이터베이스 리소스 관리


이 항목에서는 PowerShell 스크립트를 사용하여 Azure SQL 데이터베이스 논리 서버, 데이터베이스 및 방화벽 규칙을 만듭니다.

## 1단계: Azure SDK 설치

Azure PowerShell을 아직 설치하지 않았다면 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다. 그런 다음 Azure PowerShell 명령 프롬프트를 엽니다.


## 2단계: PowerShell 스크립트 구성
이 PowerShell 스크립트는 서버, 데이터베이스 및 서버 방화벽 규칙을 만듭니다.


1. 다음 블록의 PowerShell cmdlet을 텍스트 편집기에 복사합니다.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. < > 내의 모든 항목을 원하는 값으로 대체합니다. Azure Powershell 명령 프롬프트에서 다음 cmdlet을 실행할 수 있는 유효한 Azure SQL 데이터베이스 서버 위치의 목록

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##3단계: PowerShell 스크립트 실행

조합한 Azure PowerShell cmdlet을 검토합니다.

2 단계에서 구성한 PowerShell cmdlet을 복사하여 Azure PowerShell 명령 프롬프트에 붙여넣습니다. 이렇게 하면 cmdlet이 일련의 PowerShell 명령으로 실행되어 Azure SQL 서버, 데이터베이스 및 서버 방화벽 규칙을 만듭니다.

Azure SQL 리소스를 다시 만들거나 유사한 리소스를 만들 경우 다음과 같이 할 수 있습니다.

- 이 명령을 PowerShell 스크립트 파일(*.ps1)으로 저장
- Azure 관리 포털의 자동화 섹션에서 이 명령을 Azure 자동화 Runbook으로 저장 

##예제

이 PowerShell 스크립트는 미국 서부에 리소스를 만듭니다.

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##리소스

Azure SQL PowerShell cmdlet에 대한 자세한 내용은 [여기를 클릭](https://msdn.microsoft.com/library/dn546726.aspx)하십시오.

<!---HONumber=58--> 
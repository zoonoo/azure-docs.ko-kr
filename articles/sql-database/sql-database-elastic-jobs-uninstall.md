<properties 
	pageTitle="탄력적 데이터베이스 작업 도구를 제거하는 방법" 
	description="탄력적 데이터베이스 작업 도구를 제거하는 방법" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# 탄력적 데이터베이스 작업 구성 요소를 제거하는 방법

탄력적 데이터베이스 작업 서비스를 설치할 때 오류가 발생하는 경우 해당 서비스에 대한 리소스 그룹을 삭제해야 합니다.

## 서비스 구성 요소를 제거하려면

1. [Azure Preview 포털](https://ms.portal.azure.com/)을 엽니다.
2. 탄력적 작업이 포함된 구독으로 이동합니다.
3. **찾아보기**를 클릭하고 **리소스 그룹**을 클릭합니다.
4. "\_\_ElasticDatabaseJob"이라는 이름의 리소스 그룹을 선택합니다.
5. 해당 리소스 그룹을 삭제합니다.

또는 다음 PowerShell 스크립트를 사용합니다.

1. [Microsoft Azure PowerShell 창](../powershell-install-configure.md)을 엽니다. 
2. PowerShell SDK 버전 0.8.10 이상을 사용하고 있는지 확인합니다.
3. 스크립트를 실행합니다.

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## 다음 단계

탄력적 데이터베이스 작업을 다시 설치하려면 [탄력적 데이터베이스 작업 서비스 설치](sql-database-elastic-jobs-service-installation.md)를 참조하세요.

탄력적 데이터베이스 작업 서비스에 대한 개요는 [탄력적 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=July15_HO5-->
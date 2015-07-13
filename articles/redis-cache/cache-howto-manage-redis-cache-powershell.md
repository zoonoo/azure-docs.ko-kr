<properties 
 pageTitle="Azure PowerShell을 사용하여 Azure Redis Cache 관리" 
 description="Azure PowerShell을 사용하여 Azure Redis Cache에 대한 관리 작업을 수행하는 방법을 알아봅니다." 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   writer="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="04/23/2015"
   ms.author="riande"/>

# Azure PowerShell을 사용하여 Azure Redis Cache 관리

이 자습서에서는 Azure Redis Cache를 만들고 업데이트 및 삭제하기 위한 빠른 시작을 제공합니다.

## 필수 조건 ##

리소스 관리자에서 Windows PowerShell을 사용하려면 다음이 있어야 합니다.

- Windows PowerShell, 버전 3.0 또는 4.0. Windows PowerShell 버전을 확인하려면 `$PSVersionTable`을 입력하고 `PSVersion` 값이 3.0 또는 4.0인지를 확인합니다. 호환되는 버전을 설치하려면 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)을 참조하세요.
	
- Azure PowerShell 버전 0.8.0 이상. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

이 자습서는 Windows PowerShell 초보자를 위한 것입니다. Windows PowerShell에 대한 자세한 내용은 [Windows PowerShell 시작](http://technet.microsoft.com/library/hh857337.aspx)(영문)을 참조하십시오.

이 설명서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 Get-Help cmdlet을 사용합니다.

	Get-Help <cmdlet-name> -Detailed

예를 들어 Add-AzureAccount cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

	Get-Help Add-AzureAccount -Detailed

## Redis Cache에 대한 간단한 Azure PowerShell 스크립트  ##

다음 스크립트는 Azure Redis Cache를 만들고 업데이트 및 삭제하는 방법을 보여 줍니다.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## 다음 단계
Microsoft Azure PowerShell 사용에 대해 자세히 알아보려면 다음을 참조하세요.
 
- [Azure 리소스 관리자 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): AzureResourceManager 모듈에서 cmdlet을 사용하는 방법에 대해 알아봅니다.
- [리소스 그룹을 사용하여 Azure 리소스 관리](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups): Azure 관리 포털에서 리소스 그룹을 만들고 관리하는 방법에 대해 알아봅니다.
- [Azure 블로그](http://blogs.msdn.com/windowsazure): Azure의 새로운 기능에 대해 알아봅니다.
- [Windows PowerShell 블로그](http://blogs.msdn.com/powershell): Windows PowerShell의 새로운 기능에 대해 알아봅니다.
- ["Hey, Scripting Guy!" 블로그](http://blogs.technet.com/b/heyscriptingguy/): Windows PowerShell 커뮤니티에서 실제 팁과 요령을 확인합니다.

<!---HONumber=July15_HO1-->
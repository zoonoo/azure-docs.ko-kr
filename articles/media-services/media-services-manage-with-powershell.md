<properties 
	pageTitle="PowerShell을 사용하여 Azure 미디어 서비스 계정 관리" 
	description="PowerShell cmdlet를 사용하여 Azure 미디어 서비스 계정을 관리하는 방법에 대해 알아봅니다." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="juliako"/>


#PowerShell을 사용하여 Azure 미디어 서비스 계정 관리

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](https://msdn.microsoft.com/library/azure/dn167014.aspx)

##개요 

이 문서에서는 PowerShell cmdlet를 사용하여 Azure 미디어 서비스 계정을 관리하는 방법을 보여줍니다.

>[AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##Microsoft Azure PowerShell Cmdlet 설치

최신 Azure PowerShell cmdlet을 설치하려면 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

##Azure 구독 선택

PowerShell cmdlet를 설치 및 구성하면 작업하려는 구독에서 지정해야 합니다.

사용 가능한 구독 목록을 가져오려면 다음 cmdlet를 실행합니다.

	PS C:\> Get-AzureSubscription

그리고 다음 수행하여 하나를 선택합니다.

	PS C:\> Select-AzureSubscription "TestSubscription"

 
##저장소 계정 이름 가져오기

Azure 미디어 서비스는 Azure 저장소를 사용하여 미디어 콘텐츠를 저장합니다. 새 미디어 서비스 계정을 만들 때 저장소 계정과 연결해야 합니다. 저장소 계정은 미디어 서비스 계정에 사용하려고 하는 계정과 동일한 구독에 속해야 합니다.

이 예에서는 기존 저장소 계정이 사용됩니다. [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) cmdlet은 현재 구독에서 저장소 계정을 가져옵니다. 미디어 계정과 연결하려는 저장소 계정의 이름(StorageAccountName)을 가져옵니다.

	StorageAccountDescription : 
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

##새 미디어 서비스 계정 만들기

새 Azure 미디어 서비스 계정을 만들려면 미디어 서비스 계정 이름을 제공하는 [New-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) cmdlet, 만들어질 데이터 센터 위치 및 저장소 계정 이름을 사용합니다.


	PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

##미디어 서비스 계정 가져오기

하나 이상의 미디어 서비스 계정을 만들면 [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)를 사용하여 정보를 나열할 수 있습니다.

	
	PS C:\> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

이름 매개 변수를 제공하여 계정 키를 포함하는 보다 자세한 정보를 가져옵니다.

	PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

##미디어 서비스 액세스 키 다시 생성

미디어 서비스 기본 또는 보조 액세스 키를 업데이트하려는 경우 [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx)를 사용합니다. 계정 이름을 제공하고 다시 생성하려는 키(기본 또는 보조)를 지정해야 합니다.

PowerShell이 확인 질문을 하지 않기를 원하는 경우 -Force 스위치를 지정합니다.

	PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

##미디어 서비스 계정 제거

Azure 미디어 계정을 삭제할 준비가 되면 [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx)를 사용합니다.

	PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force

 

<!---HONumber=August15_HO6-->
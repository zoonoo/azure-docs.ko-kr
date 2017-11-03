---
title: "Azure Storage와 함께 Azure PowerShell 사용 | Microsoft Docs"
description: "Azure PowerShell cmdlet을 Azure Storage에 사용하는 방법에 대해 알아봅니다."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: a116b4c15046e704e374ca67c5695ff3f01ba7fb
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>Azure 저장소와 함께 Azure PowerShell 사용

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell이 사용됩니다. Azure Storage의 경우 이러한 cmdlet는 제어 평면과 데이터 평면 등, 두 범주로 나뉩니다. 제어 평면 cmdlet는 저장소 계정 관리(저장소 계정 만들기, 속성 설정, 저장소 계정 삭제, 액세스 키 회전)에 사용됩니다. 데이터 평면 cmdlet는 저장소 계정*에* 저장된 데이터를 관리하는 데 사용됩니다. 예를 들어 Blob 업로드, 파일 공유 만들기, 큐에 메시지 추가 등이 있습니다.

이 방법 문서에서는 관리 평면 cmdlet를 사용하여 저장소 계정을 관리 는 일반적인 작업에 대해 설명합니다. 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * 저장소 계정 나열
> * 기존 저장소 계정에 대한 참조 가져오기
> * 저장소 계정 만들기 
> * 저장소 계정 속성 설정
> * 액세스 키를 검색하고 다시 생성
> * 저장소 계정에 대한 액세스 보호 
> * Storage Analytics를 사용하도록 설정

또한 Storage Analyics 사용 및 액세스 방법, 데이터 평면 cmdlet 사용 방법 등과 같이, Storage에 대한 몇 가지 다른 PowerShell 문서 링크도 제공합니다.
<!-- also how to access the china and government clouds  -->

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

이 연습에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 

이 연습에서는 일반 PowerShell 창에 명령을 입력하거나, [Windows PowerShell ISE(Integrated Scripting Environment)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-)를 사용하여 예제를 진행하면서 편집기에 명령을 입력한 다음 한 번에 하나 이상의 명령을 테스트할 수 있습니다. 실행하려는 행을 강조 표시한 다음 선택 항목 실행을 클릭하여 해당 명령만 실행할 수 있습니다.

저장소 계정에 대한 자세한 내용은 [Storage 소개](storage-introduction.md) 및 [Azure Storage 계정 정보](storage-create-storage-account.md)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>구독의 저장소 계정 나열

[Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) cmdlet를 실행하여 현재 구독의 저장소 계정 목록을 검색합니다. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>저장소 계정에 대한 참조 가져오기

이제 저장소 계정에 대한 참조를 가져와야 합니다. 새 저장소 계정을 만들거나 기존 저장소 계정에 대한 참조를 가져올 수 있습니다. 다음 섹션에서는 두 방법을 모두 보여 줍니다. 

### <a name="use-an-existing-storage-account"></a>기존 저장소 계정 사용 

기존 저장소 계정을 검색하려면 리소스 그룹의 이름 및 저장소 계정의 이름이 필요합니다. 이 두 필드에 대해 변수를 설정한 다음 [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) cmdlet를 사용합니다. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

이제 기존 저장소 계정을 가리키는 $storageAccount가 있습니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기 

다음 스크립트는 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount)를 사용하여 범용 저장소 계정을 만드는 방법을 보여 줍니다. 계정을 만든 후 이후의 명령에서 각각의 호출에 대한 인증을 지정하는 대신 사용할 수 있는 컨텍스트를 검색합니다.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard\_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

이 스크립트는 다음 PowerShell cmdlet를 사용합니다. 

*   [Get AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) -유효한 위치 목록을 검색합니다. 이 예제에서는 위치에 `eastus`를 사용합니다.

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) - 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 여기서는 `teststoragerg`입니다. 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) -- 실제 저장소 계정을 만듭니다. 이 예제에서는 `testpshstorage`를 사용합니다.

SKU 이름은 LRS(로컬 중복 저장소)처럼 저장소 계정에 대한 복제 유형을 나타냅니다. 복제에 대한 자세한 내용은 [Azure Storage 복제](storage-redundancy.md)를 참조하세요.

> [!IMPORTANT]
> 저장소 계정의 이름은 Azure 내에서 고유하며 소문자여야 합니다. 명명 규칙 및 제한에 대해서는 [컨테이너와 Blob, 메타데이터의 명명 및 참조](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)를 참조하세요.
> 

이제 새 저장소 계정과 그에 대한 참조가 있습니다. 

## <a name="managing-the-storage-account"></a>저장소 계정 관리

이제 새 저장소 계정 또는 기존 저장소 계정에 대한 참조가 있으므로 다음 섹션에서는 저장소 계정 관리에 사용할 수 있는 몇 가지 명령을 설명합니다.

### <a name="storage-account-properties"></a>저장소 계정 속성

저장소 계정에 대한 설정을 변경하려면 [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount)를 사용합니다. 저장소 계정 위치 또는 속하는 리소스 그룹은 변경할 수 없지만 다른 여러 속성은 변경할 수 있습니다. 다음은 PowerShell을 사용하여 변경할 수 있는 몇 가지 속성의 목록입니다.

* 저장소 계정에 할당된 **사용자 지정 도메인**

* 저장소 계정에 할당된 **태그**. 태그는 청구 목적으로 리소스를 분류하는 데 종종 사용됩니다.

* **SKU**는 LRS(로컬 중복 저장소)처럼 저장소 계정에 대한 복제 설정을 나타냅니다. 예를 들어 Standard\_LRS를 Standard\_GRS 또는 Standard\_RAGRS로 변경할 수 있습니다. Standard ZRS 또는 Premium LRS를 다른SKU로, 또는 다른 SKU를 해당 항목으로 변경할 수 없습니다. 

* Blob Storage 계정에 대한 **액세스 계층**. 액세스 계층의 값은 **hot** 또는 **cool**로 설정되며 저장소 계정을 사용하는 방법에 맞게 액세스 계층을 선택하여 비용을 최소화할 수 있습니다. 자세한 내용은 [핫, 쿨, 보관 저장소 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요.

* Blob Storage 및/또는 파일 저장소에 대한 저장소 서비스 암호화. SSE에 대한 자세한 내용은 [저장소 서비스 암호화](storage-service-encryption.md)를 참조하세요.

* HTTPS 트래픽만 허용됩니다. 

### <a name="managing-the-access-keys"></a>액세스 키 관리

Azure 저장소 계정과 두 계정 키를 함께 제공합니다. 키를 검색하려면 [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey)를 사용합니다. 이 예에서는 첫 번째 키를 검색합니다. 다른 항목을 검색하려면 `Value[0]` 대신 `Value[1]`을 사용합니다.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

키를 다시 생성하려면 [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey)를 사용합니다. 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

다른 키를 생성하려면 `key1` 대신 `key2`를 키 이름으로 사용합니다.

키 중 하나를 다시 생성한 다음 다시 검색하여 새 값을 확인합니다.

> [!NOTE] 
> 프러덕션 저장소 계정에 대해 키를 다시 생성하기 전에 신중한 계획이 필요합니다. 하나 이상의 키를 다시 생성하면 다시 생성되는 키를 사용하던 모든 응용 프로그램에 대한 액세스가 무효화됩니다. 자세한 내용은 [저장소 액세스 키 다시 생성](storage-create-storage-account.md#regenerate-storage-access-keys)을 참조하세요.


### <a name="delete-a-storage-account"></a>저장소 계정 삭제 

저장소 계정을 삭제하려면[Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount)를 사용합니다. 

> [!IMPORTANT]
> 저장소 계정을 삭제하면 계정에 저장된 모든 자산이 함께 삭제됩니다. 계정을 실수로 삭제한 경우 즉시 지원을 요청하고 저장소 계정을 복원하기 위한 티켓을 엽니다. 데이터 복구는 보장되지 않지만 복구될 때도 있습니다. 지원 티켓이 해결되기 전까지는 기존 이름과 같은 이름으로 저장소 계정을 만들지 않습니다. 
>

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

### <a name="protecting-your-storage-account-using-vnets-and-firewalls"></a>Vnet 및 방화벽을 사용하여 저장소 계정 보호

기본적으로 모든 저장소 계정은 인터넷에 액세스할 수 있는 네트워크를 통해 액세스 가능합니다. 그러나 특정 가상 네트워크의 응용 프로그램만 저장소 계정에 액세스하도록 허용하는 네트워크 규칙을 구성할 수 있습니다. 자세한 내용은 [Azure Storage 방화벽 및 Virtual Networks 구성](storage-network-security.md)을 참조하세요. 

이 문서에서는 PowerShell cmdlet를 사용하여 이러한 설정을 관리하는 방법을 보여 줍니다.
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="using-storage-analytics"></a>저장소 분석 사용  

[Azure Storage Analytics](storage-analytics.md)는[Storage Analytics 메트릭](/rest/api/storageservices/about-storage-analytics-metrics)과 [Storage Analytics 로깅](/rest/api/storageservices/about-storage-analytics-logging)으로 구성됩니다. 

**Storage Analytics 메트릭**은 저장소 계정의 상태를 모니터링하는 데 사용할 수 있는 Azure Storage 게정의 메트릭을 수집하는 데 사용됩니다. Blob, 파일, 테이블 및 큐에 대해 메트릭을 사용할 수 있습니다.

**Storage Analytics 로깅**은 서버 쪽에서 발생하며, 이를 통해 Storage 계정의 성공한 요청 및 실패한 요청에 대한 세부 정보를 기록할 수 있습니다. 이러한 로그를 사용하여 테이블, 큐 및 Blob에 대한 읽기, 쓰기 및 삭제 작업뿐만 아니라 실패한 요청의 이유에 대한 세부 정보를 볼 수 있습니다. 로깅은 Azure Files에 사용할 수 없습니다.

[Azure Portal](https://portal.azure.com) 또는 PowerShell을 사용하거나 저장소 클라이언트 라이브러리를 사용하여 프로그래밍 방식으로 모니터링을 구성할 수 있습니다. 

> [!NOTE]
> PowerShell을 사용하여 상세 분석을 활성화할 수 있습니다. 이 기능은 포털에서 사용할 수 없습니다.
>

* PowerShell을 사용하여 Storage 메트릭 데이터를 사용하도록 설정하고 확인하는 방법을 알아보려면 [PowerShell을 사용하여 Storage 메트릭을 사용하도록 설정](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell)을 참조하세요.

* PowerShell을 사용하여 저장소 로깅 데이터를 사용하도록 설정하고 검색하는 방법을 알아보려면 [PowerShell을 사용하여 저장소 로깅을 활성화하는 방법](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell)과 [저장소 로깅 로그 데이터 찾기](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data)를 참조하세요.

* 저장소 메트릭 및 저장소 로깅을 사용하여 저장소 문제를 해결하는 방법에 대한 자세한 정보는 [Microsoft Azure 저장소 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

## <a name="managing-the-data-in-the-storage-account"></a>저장소 계정의 데이터 관리

이제 PowerShell에서 저장소 계정을 관리하는 방법을 이해했으므로 다음 문서를 통해 저장소 계정의 데이터 개체 액세스에 PowerShell을 사용하는 방법을 살펴봅니다.

* [PowerShell을 사용하여 Blob를 관리하는 방법](../blobs/storage-how-to-use-blobs-powershell.md)
* [PowerShell을 사용하여 파일을 관리하는 방법](../files/storage-how-to-use-files-powershell.md)
* [PowerShell을 사용하여 큐를 관리하는 방법](../queues/storage-powershell-how-to-use-queues.md)

<!--## Government Cloud and China Cloud

ROBINROBINROBIN 

To access the Government cloud of the China datacenters, you have to use some special steps. The following article shows how to access these special cloud accounts using PowerShell.

* [How to manage storage accounts in Government Cloud and China](storage-powershell-govt-china.md)
-->

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 관리 평면 cmdlet를 사용하여 저장소 계정을 관리 는 일반적인 작업에 대해 설명합니다. 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * 저장소 계정 나열
> * 기존 저장소 계정에 대한 참조 가져오기
> * 저장소 계정 만들기 
> * 저장소 계정 속성 설정
> * 액세스 키를 검색하고 다시 생성
> * 저장소 계정에 대한 액세스 보호 
> * Storage Analytics를 사용하도록 설정

데이터 개체 관리 방법, Storage Analytics를 사용하도록 설정하는 방법 등, 몇 가지 다른 문서에 대한 링크도 있습니다. 다음은 참고를 위한 몇 가지 다른 관련 문서와 리소스입니다. 
<!--, and how to access storage with PowerShell using the Government Cloud and the China Cloud.
-->

* [Azure Storage 제어 평면 PowerShell cmdles](/powershell/module/AzureRM.Storage/)
* [Azure Storage 데이터 평면 PowerShell cmdles](/powershell/module/azure.storage/)
* [Windows PowerShell 참조](https://msdn.microsoft.com/library/ms714469.aspx)
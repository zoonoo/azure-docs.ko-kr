---
title: Blob 저장소 - Azure 저장소에 대한 불변성 정책 설정 및 관리
description: Blob(개체) 저장소에 대한 WORM(한 번 쓰기, 많은 읽기) 지원을 사용하여 지정된 간격동안 데이터를 지울 수 없는 수정할 수 없는 상태로 저장하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970108"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Blob 저장소에 대한 불변성 정책 설정 및 관리

Azure Blob 저장소에 대한 변경할 수 없는 저장소를 사용하면 사용자가 업무에 중요한 데이터 개체를 WORM(한 번 쓰기, 다수 읽기) 상태에 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다. 보존 기간 동안 Blob을 만들고 읽을 수 있지만 수정하거나 삭제할 수는 없습니다. 변경할 수 없는 저장소는 모든 Azure 리전에서 범용 v2 및 Blob 저장소 계정에 사용할 수 있습니다.

이 문서에서는 Azure 포털, PowerShell 또는 Azure CLI를 사용하여 Blob 저장소의 데이터에 대한 불변성 정책 및 법적 보존을 설정하고 관리하는 방법을 보여 주며 있습니다. 변경할 수 없는 저장소에 대한 자세한 내용은 [변경할 수 없는 저장소가 있는 비즈니스 에 중요한 Blob 데이터 저장을](storage-blob-immutable-storage.md)참조하십시오.

## <a name="set-retention-policies-and-legal-holds"></a>보존 정책 및 법적 보존 설정

### <a name="portal"></a>[포털](#tab/azure-portal)

1. 변경할 수 없는 상태로 유지해야 하는 BLOB을 저장할 새 컨테이너를 만들거나 기존 컨테이너를 선택합니다. 컨테이너는 범용 v2 또는 Blob 저장소 계정에 있어야 합니다.

2. 컨테이너 설정에서 **액세스 정책**을 선택합니다. 그런 다음 **변경할 수 없는 Blob 저장소**아래에서 정책 **추가를** 선택합니다.

    ![포털의 컨테이너 설정](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. 시간 기준 보존을 사용하도록 설정하려면 드롭다운 메뉴에서 **시간 기준 보존**을 선택합니다.

    !["정책 유형" 아래에서 선택된 "시간 기준 보존"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. 보존 간격을 일 수(허용 값은 1~146000일)로 입력합니다.

    !["다음으로 보존 기간 업데이트" 상자](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    정책의 초기 상태가 잠금 해제되어 기능을 테스트하고 정책을 잠그기 전에 정책을 변경할 수 있습니다. SEC 17a-4와 같은 규정을 준수하기 위해서는 정책 잠금이 필수적입니다.

5. 정책을 잠급니다. 타원 **(...**)을 마우스 오른쪽 버튼으로 클릭하고 다음 메뉴가 추가 작업과 함께 나타납니다 .

    ![메뉴의 "잠금 정책"](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. **잠금 정책을** 선택하고 잠금을 확인합니다. 이제 정책이 잠겨 있고 삭제할 수 없으며 보존 간격의 확장만 허용됩니다. Blob 삭제 및 재정의는 허용되지 않습니다. 

    ![메뉴에서 "잠금 정책" 확인](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. 법적 보류를 사용하려면 **정책 추가를**선택합니다. 드롭다운 메뉴에서 **법적 보존**을 선택합니다.

    ![메뉴의 "정책 유형" 아래에 있는 "법적 보존"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. 하나 이상의 태그를 사용하여 법적 보존을 만듭니다.

    ![정책 유형 아래의 "태그 이름" 상자](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. 법적 보류를 지우려면 적용된 법적 보류 식별자 태그를 제거합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 기능은 `az storage container immutability-policy` 및 `az storage container legal-hold` 명령 그룹에 포함되어 있습니다. 이러한 그룹에 `-h`를 실행하여 명령을 확인합니다.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az.Storage 모듈은 변경할 수 없는 스토리지를 지원합니다.  기능을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. `Install-Module PowerShellGet –Repository PSGallery –Force`
2. Azure PowerShell의 이전 설치를 제거합니다.
3. Azure PowerShell을 설치합니다. `Install-Module Az –Repository PSGallery –AllowClobber`

다음 PowerShell 스크립트 샘플은 참조하기 위한 목적으로 제공되는 것입니다. 이 스크립트는 새 스토리지 계정과 컨테이너를 만듭니다. 그런 다음 법적 보존 을 설정하고 지우고 시간 기반 보존 정책(불변성 정책이라고도 함)을 만들고 잠그고 보존 간격을 연장하는 방법을 보여 주며, 보존 간격을 연장합니다.

먼저 Azure 저장소 계정을 만듭니다.

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

법적 보존 설정 및 지우기:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

시간 기반 불변성 정책을 만들거나 업데이트합니다.

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

불변성 정책 검색:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

잠금 변경 가능성 정책(프롬프트 해제에 추가): `-Force`

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

불변성 정책 확장:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

잠금 해제된 불변성 정책을 `-Force` 제거합니다(프롬프트 해제에 추가):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>보호된 부속 블라브 쓰기 허용 사용

### <a name="portal"></a>[포털](#tab/azure-portal)

![추가 추가 쓰기 허용](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 기능은 `az storage container immutability-policy` 및 `az storage container legal-hold` 명령 그룹에 포함되어 있습니다. 이러한 그룹에 `-h`를 실행하여 명령을 확인합니다.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>다음 단계

[변경할 수 없는 저장소로 비즈니스에 중요한 Blob 데이터 저장](storage-blob-immutable-storage.md)

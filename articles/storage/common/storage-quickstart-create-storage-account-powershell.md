---
title: "Azure 빠른 시작 - PowerShell을 사용하여 저장소 계정 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 새 저장소 계정을 만드는 방법을 간단히 알아봅니다."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>PowerShell을 사용하여 저장소 계정 만들기

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 가이드에서는 PowerShell을 사용하여 Azure Storage 계정을 만드는 방법을 설명합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```

사용하려는 위치를 모르는 경우 사용 가능한 위치를 나열할 수 있습니다. 목록이 표시되면 사용할 위치를 찾습니다. 이 예제에서는 **eastus**를 사용합니다. 이 위치를 변수에 저장하고 변수를 사용하면 한 곳에서 변경할 수 있습니다.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>범용 표준 저장소 계정 만들기

사용되는 방법 및 서비스 형식(Blob, File, Table 또는 Queue)에 따라 여러 가지 저장소 계정 형식이 있습니다. 이 표에서는 가능성을 보여 줍니다.

|**저장소 계정의 유형**|**범용 표준**|**범용 프리미엄**|**Blob Storage, 핫 및 쿨 액세스 계층**|
|-----|-----|-----|-----|
|**지원되는 서비스**| Blob, File, Table, Queue 서비스 | Blob 서비스 | Blob 서비스|
|**지원되는 Blob 유형**|블록 Blob, 페이지 Blob, 추가 Blob | 페이지 Blob | 블록 Blob 및 추가 Blob|

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount)를 사용하여 모든 네 가지 서비스에 사용할 수 있는 범용 표준 저장소 계정을 만듭니다. 저장소 계정 이름을 *contosomvcstandard*로 지정하고 로컬 중복 저장소 및 Blob 암호를 사용하도록 구성합니다.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다. 이 경우 만든 저장소 계정이 제거됩니다.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 범용 표준 저장소 계정을 만들었습니다. 저장소 계정에서 Blob을 업로드 및 다운로드하는 방법을 알아보려면 계속해서 Blob 저장소 빠른 시작을 진행합니다.
> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Blob Storage에서 개체 전송](../blobs/storage-quickstart-blobs-powershell.md)
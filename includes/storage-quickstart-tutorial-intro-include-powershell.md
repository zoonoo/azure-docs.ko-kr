---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 027b370d2497822dcbd6f3958556357957f9e8f5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50964665"
---
## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzureRmAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

사용하려는 위치를 모르는 경우 사용 가능한 위치를 나열할 수 있습니다. 다음 코드 예제를 사용하여 위치 목록을 표시하고 사용하려는 것을 찾습니다. 이 예제에서는 **eastus**를 사용합니다. 위치를 변수에 저장하고 변수를 사용하면 한 곳에서 변경할 수 있습니다.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount)를 사용하여 LRS 복제로 표준 범용 저장소 계정을 만듭니다. 다음으로 사용하려는 저장소 계정을 정의하는 저장소 계정 컨텍스트를 가져옵니다. 저장소 계정에서 작업할 때 반복적으로 자격 증명을 전달하는 대신 컨텍스트를 참조합니다. 다음 예제를 사용하여 LRS(로컬 중복 저장소) 및 Blob 암호화(기본적으로 사용)를 사용하여 *mystorageaccount*라는 저장소 계정을 만듭니다.

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `
  -Kind Storage

$ctx = $storageAccount.Context
```

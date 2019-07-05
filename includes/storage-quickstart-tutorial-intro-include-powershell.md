---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182427"
---
## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

사용하려는 위치를 모르는 경우 사용 가능한 위치를 나열할 수 있습니다. 다음 코드 예제를 사용하여 위치 목록을 표시하고 사용하려는 것을 찾습니다. 이 예제에서는 **eastus**를 사용합니다. 위치를 변수에 저장하고 변수를 사용하면 한 곳에서 변경할 수 있습니다.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 사용하여 LRS 복제가 포함된 표준 범용 스토리지 계정을 만듭니다. 다음으로 사용하려는 저장소 계정을 정의하는 저장소 계정 컨텍스트를 가져옵니다. 저장소 계정에서 작업할 때 반복적으로 자격 증명을 전달하는 대신 컨텍스트를 참조합니다. 다음 예제를 사용하여 LRS(로컬 중복 스토리지) 및 Blob 암호화(기본적으로 사용)를 사용하여 *mystorageaccount*라는 스토리지 계정을 만듭니다.

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```

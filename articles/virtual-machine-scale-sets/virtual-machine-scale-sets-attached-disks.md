---
title: "Azure Virtual Machine Scale Sets 연결 데이터 디스크 | Microsoft Docs"
description: "가상 머신 확장 집합과 연결된 데이터 디스크를 사용하는 방법 알아보기"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 355865b963c313097f7f5900007f341dba92bf67
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2017
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 가상 머신 확장 집합 및 연결된 데이터 디스크
이제 Azure [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/)은 연결된 데이터 디스크가 있는 가상 머신을 지원합니다. Azure Managed Disks를 사용하여 만든 확장 집합에 대한 저장소 프로필에서 데이터 디스크를 정의할 수 있습니다. 이전에 크기 집합에서 VM과 함께 사용할 수 있는 직접 연결된 저장소 옵션에는 OS 드라이브 및 임시 드라이브가 있었습니다.

> [!NOTE]
>  정의된 연결된 데이터 디스크를 사용하여 크기 집합을 만드는 경우 독립 실행형 Azure VM의 경우와 마찬가지로 사용할 VM 내에서 디스크를 탑재하고 포맷해야 합니다. 이 프로세스를 완료하는 편리한 방법은 사용자 지정 스크립트 확장을 사용하여 VM에서 모든 데이터 디스크를 분할하고 포맷하는 표준 스크립트를 호출하는 것입니다.

## <a name="create-a-scale-set-with-attached-data-disks"></a>연결된 데이터 디스크를 포함한 크기 집합 만들기
연결된 디스크를 포함하는 확장 집합을 만드는 간단한 방법은 [az vmss create](/cli/azure/vmss#create) 명령을 사용하는 것입니다. 다음 예제에서는 Azure 리소스 그룹 및 각각 50GB 및 100GB의 연결된 데이터 디스크 2개를 포함하는 10개의 Ubuntu VM으로 구성된 가상 머신 확장 집합을 만듭니다.

```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

기본값을 지정하지 않으면 [az vmss create](/cli/azure/vmss#create) 명령은 특정 구성 값을 기본값으로 지정합니다. 재정의할 수 있는 사용 가능한 옵션을 확인하려면 다음을 사용하세요.

```bash
az vmss create --help
```

연결된 데이터 디스크를 포함한 크기 집합을 만드는 또 다른 방법은 Azure Resource Manager 템플릿에서 크기 집합을 정의하고 _storageProfile_에서 _dataDisks_ 섹션을 포함하며 템플릿을 배포하는 것입니다. 앞의 예제에서 50GB 및 100GB 디스크는 다음 템플릿 예제와 같이 정의됩니다.

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```

다음에 정의된 연결된 디스크를 포함한 크기 집합 템플릿의 예제를 배포하기 위해 완벽하게 준비되었습니다. [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data)

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>기존 크기 집합에 데이터 디스크 추가
> [!NOTE]
>  [Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md)를 사용하여 만든 확장 집합에만 데이터 디스크를 연결할 수 있습니다.

Azure CLI _az vmss disk attach_ 명령을 사용하여 가상 머신 확장 집합에 데이터 디스크를 추가할 수 있습니다. 이미 사용 중이 아닌 LUN을 지정해야 합니다. 다음 CLI 예제는 50GB 드라이브를 LUN 3에 추가합니다.

```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

다음 PowerShell 예제는 50GB 드라이브를 LUN 3에 추가합니다.

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> 다양한 VM 크기에 따라 지원하는 연결된 드라이브의 숫자가 제한됩니다. 새 디스크를 추가하기 전에 [가상 컴퓨터 크기 특성](../virtual-machines/windows/sizes.md)을 확인합니다.

크기 집합 정의의 _storageProfile_에 있는 _dataDisks_ 속성에 새 항목을 추가하고 변경 내용을 적용하여 디스크를 추가할 수도 있습니다. 이를 테스트하려면 [Azure 리소스 탐색기](https://resources.azure.com/)에서 기존 크기 집합을 찾습니다. _편집_을 선택하고 다음 예제와 같이 데이터 디스크의 목록에 새 디스크를 추가합니다.

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

_배치_를 선택하여 크기 집합에 변경 내용을 적용합니다. 두 개 이상의 연결된 데이터 디스크를 지원하는 VM 크기를 사용하는 경우 이 예제가 정상적으로 작동합니다.

> [!NOTE]
> 데이터 디스크를 추가하거나 제거하는 등 크기 집합 정의를 변경하는 경우 새로 만든 VM에 적용되지만 _upgradePolicy_ 속성이 "자동"으로 설정된 경우 기존 VM에만 적용됩니다. "수동"으로 설정하면 수동으로 기존 VM에 새 모델을 적용해야 합니다. 포털에서 _Update-AzureRmVmssInstance_ PowerShell 명령 또는 _az vmss update-instances_ CLI 명령을 사용하여 수행할 수 있습니다.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>기존 확장 집합에 미리 지정된 데이터 디스크 추가 
> 기존 확장 집합 모델에 디스크를 추가하는 경우 설계 상 디스크는 항상 빈 상태로 만들어집니다. 이 시나리오는 확장 집합에서 만든 새 인스턴스도 포함합니다. 확장 집합 정의에 빈 데이터 디스크가 있기 때문에 이 동작이 발생합니다. 기존 확장 집합 모델에 미리 지정된 데이터 드라이브를 만들기 위해 다음 두 가지 옵션 중 하나를 선택할 수 있습니다.

* 사용자 지정 스크립트를 실행하여 인스턴스 0 VM에서 다른 VM의 데이터 디스크에 데이터를 복사합니다.
* OS 디스크 및 데이터 디스크(필요한 데이터 포함)에서 관리되는 이미지를 만들고 이미지를 포함한 새 확장 집합을 만듭니다. 이렇게 만든 모든 새 VM에는 확장 집합의 정의에 제공되는 데이터 디스크가 있습니다. 이 정의가 사용자 지정된 데이터를 포함한 데이터 디스크를 사용하여 이미지를 참조하기 때문에 확장 집합의 모든 가상 머신은 이러한 변경 내용이 적용됩니다.

> 사용자 지정 이미지를 만들 수 있는 방법은 [Azure에서 일반화된 VM의 관리되는 이미지 만들기](/azure/virtual-machines/windows/capture-image-resource/)에서 확인할 수 있습니다 

> 사용자는 필수 데이터를 포함한 인스턴스 0 VM을 캡처해야 하고 이미지 정의에 VHD를 사용해야 합니다.

## <a name="removing-a-data-disk-from-a-scale-set"></a>크기 집합에서 데이터 디스크 제거
Azure CLI _az vmss disk detach_ 명령을 사용하여 가상 머신 확장 집합에서 데이터 디스크를 제거할 수 있습니다. 예를 들어, 다음 명령은 LUN 2에 정의된 디스크를 제거합니다.
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
마찬가지로 _storageProfile_에 있는 _dataDisks_ 속성에서 항목을 제거하고 변경 내용을 적용하여 크기 집합에서 디스크를 제거할 수도 있습니다. 

## <a name="additional-notes"></a>추가적인 참고 사항
Azure Managed Disks 및 확장 집합 연결 데이터 디스크에 대한 지원은 Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 이상의 API 버전에서 사용할 수 있습니다.

크기 집합에 대한 연결된 디스크 지원의 초기 구현 중에 크기 집합에 있는 개별 VM 간에 데이터 디스크를 연결하거나 분리할 수 없습니다.

크기 집합에 있는 연결된 데이터 디스크에 대한 Azure Portal 지원은 처음부터 제한됩니다. 요구 사항에 따라 Azure 템플릿, CLI, PowerShell, SDK 및 REST API를 사용하여 연결된 디스크를 관리할 수 있습니다.



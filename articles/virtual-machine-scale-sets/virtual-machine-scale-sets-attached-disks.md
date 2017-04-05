---
title: "Azure Virtual Machine Scale Sets 연결 데이터 디스크 | Microsoft Docs"
description: "가상 컴퓨터 크기 집합과 연결된 데이터 디스크를 사용하는 방법 알아보기"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 91d36d5321f455a2af31093fa460ddf6640942d4
ms.lasthandoff: 03/31/2017


---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Azure VM Scale Sets 및 연결된 데이터 디스크
이제 Azure [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/)은 연결된 데이터 디스크가 있는 가상 컴퓨터를 지원합니다. Azure Managed Disks를 사용하여 만든 크기 집합에 대한 저장소 프로필에서 데이터 디스크를 정의할 수 있습니다. 이전에 크기 집합에서 VM과 함께 사용할 수 있는 직접 연결된 저장소 옵션에는 OS 드라이브 및 임시 드라이브가 있었습니다.

> [!NOTE]
>  정의된 연결된 데이터 디스크를 사용하여 크기 집합을 만드는 경우 독립 실행형 Azure VM의 경우와 마찬가지로 사용할 VM 내에서 디스크를 탑재하고 포맷해야 합니다. 이 작업을 수행하는 편리한 방법은 사용자 지정 스크립트 확장을 사용하여 VM에서 모든 데이터 디스크를 분할하고 포맷하는 표준 스크립트를 호출하는 것입니다.

## <a name="create-a-scale-set-with-attached-data-disks"></a>연결된 데이터 디스크를 포함한 크기 집합 만들기
연결된 디스크를 포함하는 크기 집합을 만드는 간단한 방법은 [Azure CLI](https://github.com/Azure/azure-cli) _vmss create_ 명령을 사용하는 것입니다. 다음 예제에서는 Azure 리소스 그룹 및 각각 50GB 및 100GB라는 두 개의 연결된 데이터 디스크를 포함한 10개의 Ubuntu VM인 VM 크기 집합을 만듭니다.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
_vmss create_ 명령을 지정하지 않으면 특정 구성 값을 기본값으로 지정합니다. 재정의할 수 있는 사용 가능한 옵션을 확인하려면 다음을 사용하세요.
```bash
az vmss create --help
```
연결된 데이터 디스크를 포함한 크기 집합을 만드는 또 다른 방법은 Azure Resource Manager 템플릿에서 크기 집합을 정의하고 _storageProfile_에서 _dataDisks_ 섹션을 포함하며 템플릿을 배포하는 것입니다. 위에 있는 50GB 및 100GB 디스크 예제는 템플릿에서 다음과 같이 정의됩니다.
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
Azure CLI _az vmss disk attach_ 명령을 사용하여 VM 크기 집합에 데이터 디스크를 추가할 수 있습니다. 사용될 준비가 되지 않은 lun을 지정해야 합니다. 다음 CLI 예제는 50GB 드라이브를 lun 3에 추가합니다.
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```
> [!NOTE]
> 다양한 VM 크기에 따라 지원하는 연결된 드라이브의 숫자가 제한됩니다. 새 디스크를 추가하기 전에 [가상 컴퓨터 크기 특성](../virtual-machines/windows/sizes.md)을 확인합니다.

크기 집합 정의의 _storageProfile_에 있는 _dataDisks_ 속성에 새 항목을 추가하고 변경 내용을 적용하여 디스크를 추가할 수도 있습니다. 이를 테스트하려면 [Azure 리소스 탐색기](https://resources.azure.com/)에서 기존 크기 집합을 찾습니다. _편집_을 선택하고 데이터 디스크의 목록에 새 디스크를 추가합니다. 예: 위의 예제를 사용합니다.
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

## <a name="removing-a-data-disk-from-a-scale-set"></a>크기 집합에서 데이터 디스크 제거
Azure CLI _az vmss disk detach_ 명령을 사용하여 VM 크기 집합에서 데이터 디스크를 제거할 수 있습니다. 예를 들어, 다음 명령은 lun 2에 정의된 디스크를 제거합니다.
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
마찬가지로 _storageProfile_에 있는 _dataDisks_ 속성에서 항목을 제거하고 변경 내용을 적용하여 크기 집합에서 디스크를 제거할 수도 있습니다. 

## <a name="additional-notes"></a>추가적인 참고 사항
Azure Managed Disks 및 크기 집합 연결 데이터 디스크에 대한 지원이 Microsoft.Compute API의 [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 버전에 추가되었습니다. 이 버전 이상의 API를 사용하여 빌드된 SDK 또는 명령줄 도구를 사용할 수 있습니다.

크기 집합에 대한 연결된 디스크 지원의 초기 구현 중에 크기 집합에 있는 개별 VM 간에 데이터 디스크를 연결하거나 분리할 수 없습니다.

크기 집합에 있는 연결된 데이터 디스크에 대한 Azure Portal 지원은 처음부터 제한됩니다. 요구 사항에 따라 Azure 템플릿, CLI, PowerShell, SDK 및 REST API를 사용하여 연결된 디스크를 관리할 수 있습니다.




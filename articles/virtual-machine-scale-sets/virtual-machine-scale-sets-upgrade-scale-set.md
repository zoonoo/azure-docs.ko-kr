---
title: Azure 가상 머신 확장 집합 수정 | Microsoft Docs
description: Azure 가상 머신 확장 집합 수정
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 수정
이 문서에서는 기존 가상 머신 확장 집합을 수정하는 방법을 설명합니다. 작업에는 확장 집합의 구성을 변경하는 방법, 확장 집합에서 실행되는 응용 프로그램의 구성을 변경하는 방법, 가용성을 관리하는 방법이 포함됩니다.

## <a name="fundamental-concepts"></a>기본 개념

### <a name="scale-set-model"></a>확장 집합 모델

확장 집합에는 확장 집합의 *desired* 상태를 전체적으로 캡처하는 모델이 있습니다. 확장 집합의 모델을 쿼리하려면 다음을 사용할 수 있습니다.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get)를 참조하세요.

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)를 참조하세요.

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)를 참조하세요.

[Azure Resource Explorer(미리 보기)](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합에 대한 모델을 쿼리할 수도 있습니다.

출력의 정확한 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음은 Azure CLI의 샘플 출력입니다.

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

여기서 볼 수 있듯이 이러한 속성은 확장 집합에 전체적으로 적용됩니다.



### <a name="scale-set-instance-view"></a>확장 집합 인스턴스 보기

또한 확장 집합에는 확장 집합의 현재 *런타임* 상태를 캡처하는 인스턴스 보기가 있습니다. 확장 집합의 인스턴스 보기를 쿼리하려면 다음을 사용할 수 있습니다.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)를 참조하세요.

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)를 참조하세요.

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)를 참조하세요.

[Azure Resource Explorer(미리 보기)](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합에 대한 인스턴스 보기를 쿼리할 수도 있습니다.

출력의 정확한 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음은 Azure CLI의 샘플 출력입니다.

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

볼 수 있듯이 이러한 속성은 확장 집합에서 VM의 현재 런타임 상태에 대한 요약을 제공합니다. 요약은 확장 집합에 적용된 확장의 상태를 포함합니다(간단히 하기 위해 생략됨).



### <a name="scale-set-vm-model-view"></a>확장 집합 VM 모델 보기

확장 집합에는 모델 보기가 있는 것처럼, 확장 집합의 각 VM에도 고유한 모델 보기가 있습니다. 확장 집합의 모델 보기를 쿼리하려면 다음을 사용할 수 있습니다.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get)를 참조하세요.

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)를 참조하세요.

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)를 참조하세요.

[Azure Resource Explorer(미리 보기)](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합의 VM에 대한 모델을 쿼리할 수도 있습니다.

출력의 정확한 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음은 Azure CLI의 샘플 출력입니다.

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

여기서 볼 수 있듯이 이러한 속성은 확장 집합의 구성 전체가 아니라 VM 자체의 구성을 설명합니다. 예를 들어, 확장 집합 모델에는 `overprovision` 속성이 있지만 확장 집합의 VM에 대한 모델은 그렇지 않습니다. 이러한 차이에 대한 이유는 오버프로비전은 확장 집합에서 개별 VM이 아닌 전체의 확장 집합에 대한 속성이기 때문입니다. (오버프로비전에 대한 자세한 내용은 [확장 집합 디자인 고려 사항](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)을 참조하세요.)



### <a name="scale-set-vm-instance-view"></a>확장 집합 VM 인스턴스 보기

확장 집합에는 인스턴스 보기가 있는 것처럼, 확장 집합의 각 VM에도 고유한 인스턴스 보기가 있습니다. 확장 집합의 인스턴스 보기를 쿼리하려면 다음을 사용할 수 있습니다.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview)를 참조하세요.

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)를 참조하세요.

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)를 참조하세요.

[Azure Resource Explorer(미리 보기)](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합의 VM에 대한 인스턴스 보기를 쿼리할 수도 있습니다.

출력의 정확한 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음은 Azure CLI의 샘플 출력입니다.

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

볼 수 있듯이 이러한 속성은 VM 자체의 현재 런타임 상태를 설명합니다. 상태는 확장 집합에 적용된 모든 확장을 포함합니다(간단히 하기 위해 생략됨).




## <a name="techniques-for-updating-global-scale-set-properties"></a>전역 확장 집합 속성 업데이트를 위한 기술

전역 확장 집합 속성을 업데이트하려면 확장 집합 모델의 속성을 업데이트해야 합니다. 이러한 업데이트는 다음을 통해 수행할 수 있습니다.

* REST API: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)를 참조하세요.

  REST API의 속성을 사용하여 Azure Resource Manager 템플릿을 배포하여 전역 확장 집합 속성을 업데이트할 수도 있습니다.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss)를 참조하세요.

* Azure CLI:

  * 속성을 수정하려면: `az vmss update --set {propertyPath}={value}` 
  
  * 확장 집합의 목록 속성에 개체를 추가하려면: `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * 확장 집합의 목록 속성에서 개체를 제거하려면: `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)를 참조하세요. 
  
  또는 `az vmss create` 명령을 사용하여 이전에 확장 집합을 배포한 경우 `az vmss create` 명령을 다시 실행하여 확장 집합을 업데이트할 수 있습니다. 이렇게 하려면 수정하려는 속성을 제외하고 `az vmss create` 명령의 모든 속성이 이전과 같은지 확인합니다.



[Azure Resource Explorer(미리 보기)](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합 모델을 업데이트할 수도 있습니다.

확장 집합 모델이 업데이트되면 새 구성이 확장 집합에서 만들어진 모든 새 VM에 적용됩니다. 그러나 확장 집합의 기존 VM에 대한 모델은 최신 전체 확장 집합을 사용해서 여전히 최신 상태로 유지해야 합니다. 각 VM에 대한 모델에는 VM이 최신 전체 확장 집합 모델로 최신 상태를 유지하는지 여부를 나타내는 `latestModelApplied`라는 부울 속성이 있습니다. (`true`의 값은 VM이 최신 모델로 최신 상태를 유지하고 있음을 의미합니다.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>최신 확장 집합 모델로 VM을 최신 상태로 유지하기 위한 기술

확장 집합에는 VM이 최신 확장 집합 모델로 최신 상태를 유지하는 방법을 결정하는 *업그레이드 정책*이 있습니다. 업그레이드 정책에 대한 세 가지 모드는 다음과 같습니다.

- **자동**: 이 모드에서 확장 집합은 가져오는 VM의 순서를 보장하지 않습니다. 확장 집합은 모든 VM을 동시에 제거할 수 있습니다. 
- **롤링**: 이 모드에서 확장 집합은 일괄 처리 중간에 선택적 일시 중지 시간을 유지하면서 업데이트를 일괄로 롤아웃합니다.
- **수동**: 이 모드에서 확장 집합 모델을 업데이트하면 기존 VM에 아무런 변화도 발생하지 않습니다. 기존 VM을 업데이트하려면 각각을 수동으로 업그레이드해야 합니다. 다음을 통해 이 수동 업그레이드를 수행할 수 있습니다.

  - REST API: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances)를 참조하세요.

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance)를 참조하세요.

  - Azure CLI: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)를 참조하세요.

  [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합의 VM을 수동으로 업그레이드할 수도 있습니다.

>[!NOTE]
> Azure Service Fabric 클러스터는 자동 모드만 사용할 수 있지만 업데이트는 다르게 처리됩니다. Service Fabric 업데이트에 대한 자세한 내용은 [Service Fabric 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade)를 참조하세요.

전역 확장 집합 속성에 대한 한 가지 유형의 수정 작업은 업그레이드 정책을 따르지 않습니다. OS 프로필을 설정하는 확장에 대한 변경 (예는 관리자 사용자 이름 및 암호입니다.) 이러한 속성은 2017-12-01 이상의 API 버전에서만 변경될 수 있습니다. 이러한 변경은 확장 집합 모델이 변경된 후에 만들어지는 VM에만 적용됩니다. 기존 VM을 최신 상태로 유지하려면 기존 각 VM을 이미지로 다시 설치해야 합니다. 다음을 통해 VM를 이미지로 다시 설치합니다.

* REST API: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)를 참조하세요.

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm)를 참조하세요.

* Azure CLI: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)를 참조하세요.

[Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합의 VM을 이미지로 다시 설치할 수도 있습니다.




## <a name="properties-with-restrictions-on-modification"></a>수정이 제한되는 속성

### <a name="create-time-properties"></a>만들기 시간 속성

일부 속성은 처음에 확장 집합을 만들 때만 설정할 수 있습니다. 이러한 속성은 다음과 같습니다.

- 영역
- 이미지 참조 게시자
- 이미지 참조 제공

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>현재 값에 따라서만 변경할 수 있는 속성

현재 값을 따른다는 점을 제외하고, 일부 속성을 변경할 수 있습니다. 이러한 속성은 다음과 같습니다.

- `singlePlacementGroup`: `singlePlacementGroup`이 true이면 false로 수정할 수 있습니다. 그러나 `singlePlacementGroup`이 false이면 true로 수정할 수 *없습니다*.
- `subnet`: 확장 집합의 서브넷은 원래 서브넷과 새 서브넷이 동일한 가상 네트워크에 있는 경우 수정할 수 있습니다.

### <a name="properties-that-require-deallocation-to-change"></a>변경하기 위해 할당을 취소해야 하는 속성

일부 속성은 확장 집합의 VM을 할당 취소해야만 특정 값으로 변경할 수 있습니다. 이러한 속성은 다음과 같습니다.

- `sku name`: 새 VM SKU가 확장 집합이 현재 있는 하드웨어에서 지원되지 않을 경우, `sku name`을 수정하기 전에 확장 집합에서 VM을 할당 취소해야 합니다. VM 크기 조정에 대한 자세한 내용은 [이 Azure 블로그 게시물](https://azure.microsoft.com/blog/resize-virtual-machines/)을 참조하세요.


## <a name="vm-specific-updates"></a>VM 관련 업데이트

특정 수정 내용은 전역 확장 집합 속성 대신 특정 VM에 적용될 수 있습니다. 현재, 지원되는 유일한 VM 관련 업데이트는 확장 집합의 VM에서 데이터 디스크 연결/분리 작업입니다. 이 기능은 미리 보기 상태입니다. 자세한 내용은 [미리 보기 설명서](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)를 참조하세요.

## <a name="scenarios"></a>시나리오

### <a name="application-updates"></a>응용 프로그램 업데이트

응용 프로그램이 확장을 통해 확장 집합에 배포되는 경우, 확장 구성을 업데이트하면 업그레이드 정책에 따라 응용 프로그램이 업데이트됩니다. 예를 들어, 새 버전의 스크립트를 사용자 지정 스크립트 확장에서 실행되도록 하려면 새 스크립트를 가리키도록 `fileUris` 속성을 업데이트할 수 있습니다. 

경우에 따라 확장 구성이 변경되지 않더라도 강제로 업데이트하는 것이 좋습니다. (예를 들어 스크립트의 URI를 변경하지 않고 스크립트를 업데이트했습니다.) 이러한 경우 업데이트를 강제로 수행하도록 `forceUpdateTag`를 수정할 수 있습니다. Azure 플랫폼은 이 속성을 해석하지 않으므로 해당 값을 변경해도 확장이 실행되는 방식에는 영향을 주지 않습니다. 수정하면 확장이 강제로 다시 실행됩니다. 

`forceUpdateTag`에 대한 자세한 내용은 [확장에 대한 REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate)를 참조하세요.

응용 프로그램을 사용자 지정 이미지를 통해 배포하는 것도 일반적입니다. 이 시나리오는 다음 섹션에서 설명됩니다.

### <a name="os-updates"></a>OS 업데이트

플랫폼 이미지를 사용하는 경우 `imageReference`를 수정하여 이미지를 업데이트할 수 있습니다. 자세한 내용은 [REST API 설명서](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)를 참조하세요.

>[!NOTE]
> 플랫폼 이미지를 사용하는 경우 이미지 참조 버전으로 "최신"을 지정하는 것이 일반적입니다. 즉, 확장 집합 생성, 스케일 아웃 및 이미지로 다시 설치할 때 VM은 사용 가능한 최신 버전으로 만들어집니다. 그러나 새 이미지 버전이 출시될 때마다 OS 이미지가 자동으로 업데이트되는 것은 *아닙니다*. 이것은 현재, 미리 보기로 제공되는 별도의 기능입니다. 자세한 내용은 [자동 OS 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)를 참조하세요.

사용자 지정 이미지를 사용하는 경우 `imageReference` ID를 업데이트하여 이미지를 업데이트할 수 있습니다. 자세한 내용은 [REST API 설명서](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)를 참조하세요.

## <a name="examples"></a>예

### <a name="update-the-os-image-for-your-scale-set"></a>확장 집합에 대한 OS 이미지 업데이트

이전 버전의 Ubuntu LTS 16.04를 실행하는 확장 집합이 있다고 가정해보겠습니다. Ubuntu LTS 16.04의 최신 버전으로 업데이트하려고 합니다(예: 버전 16.04.201801090). 이미지 참조 버전 속성은 목록에 포함되어 있지 않으므로 다음 명령을 사용하여 이러한 속성을 직접 수정할 수 있습니다.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>확장 집합에 대한 부하 분산 장치 업데이트

Azure 부하 분산 장치가 있는 확장 집합이 있으며 부하 분산 장치를 Azure 응용 프로그램 게이트웨이로 바꾸려고 한다고 가정해보겠습니다. 확장 집합에 대한 부하 분산 장치 및 응용 프로그램 게이트웨이 속성은 목록의 일부입니다. 따라서 속성을 직접 수정하는 대신 목록 요소를 제거 및 추가하기 위한 명령을 사용할 수 있습니다.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> 이러한 명령은 확장 집합에 IP 구성 및 Load Balancer가 하나만 있다고 가정합니다. 이러한 항목이 여러 개 있을 경우, 0이 아닌 목록 인덱스를 사용해야 할 수 있습니다.
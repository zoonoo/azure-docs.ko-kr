---
title: 가용성 영역을 사용하는 Azure 확장 집합 만들기 | Microsoft Docs
description: 가동 중단에 대비해서 중복성을 늘리기 위해 가용성 영역을 사용하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: dee06eee045bc24c2864333a66a6d145a771b3ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>가용성 영역을 사용하는 가상 머신 확장 집합 만들기
데이터 센터 수준 오류로부터 가상 머신 확장 집합을 보호하려면 가용성 영역에서 확장 집합을 만들 수 있습니다. 가용성 영역을 지원하는 Azure 지역은 각각 독립적인 전원, 네트워크 및 냉각 기능을 갖춘 3개 이상의 별도 지역을 포함합니다. 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.


## <a name="single-zone-and-zone-redundant-scale-sets"></a>단일 영역 및 영역 중복 확장 집합
가상 머신 확장 집합을 배포하면 지역에서 단일 가용성 영역 또는 여러 영역을 사용할 수 있습니다.

단일 영역에서 확장 집합을 만들 경우 해당 VM 인스턴스가 모두 실행되는 영역을 제어하고 해당 영역 내에서만 확장 집합을 관리하고 확장 집합의 크기를 자동 조정합니다. 영역 중복 확장 집합을 사용하여 여러 영역에 걸쳐 있는 단일 확장 집합을 만들 수 있습니다. VM 인스턴스를 만들 때 기본적으로 영역에 균등하게 배포됩니다. 영역 중 하나가 중단되어야 확장 집합이 용량이 증가하도록 자동으로 확장되지 않습니다. 가장 좋은 방법은 CPU 또는 메모리 사용량을 기반으로 자동 크기 조정 규칙을 구성하는 것입니다. 확장 집합은 자동 크기 조정 규칙을 통해 다른 작업 영역에서 새 인스턴스를 확장하여 한 영역의 VM 인스턴스 손실에 대응할 수 있습니다.

가용성 영역을 사용하려면 [지원되는 Azure 지역](../availability-zones/az-overview.md#regions-that-support-availability-zones)에 확장 집합을 만들어야 합니다. 다음 방법 중 하나를 사용하여 가용성 영역을 사용하는 확장 집합을 만들 수 있습니다.

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 리소스 관리자 템플릿](#use-azure-resource-manager-templates)

## <a name="availability-considerations"></a>가용성 고려 사항
API 버전 2017-12-01부터 확장 집합을 하나 이상의 영역으로 배포하는 경우 "최대 분산" 또는 "정적 5 장애 도메인 분산"을 사용하는 배포 옵션이 있습니다. 최대 확산을 사용하여 확장 집합은 각 영역 내에서 가능한 많은 장애 도메인에서 VM을 분산합니다. 이 분산은 영역당 5개의 장애 도메인보다 크거나 작을 수 있습니다. 반대로 "정적 5 장애 도메인 확산"을 사용하여 확장 집합은 정확히 영역당 5개의 장애 도메인에서 VM을 분산합니다. 확장 집합은 할당 요청을 충족하는 영역당 5개의 고유 장애 도메인을 찾을 수 없으므로 요청은 실패합니다.

최대 분산은 대부분의 경우에서 최상의 분산을 제공하므로 **대부분의 워크로드에 대해 최대 분산을 사용하여 배포하는 것이 좋습니다**. 고유한 하드웨어 격리 단위에 분산될 복제본이 필요한 경우 가용성 영역에 분산하고 각 영역 내에서 최대 분산을 활용하는 것이 좋습니다. 최대 분산을 사용하면 VM이 실제로 분산되는 장애 도메인 수에 관계 없이 확장 집합 VM 인스턴스 보기 및 인스턴스 메타데이터에 하나의 장애 도메인만 표시됩니다. 각 영역 내 분산은 암시적입니다.

최대 분산을 사용하려면 "platformFaultDomainCount"를 1로 설정합니다. 정적 5 장애 도메인 분산을 사용하려면 "platformFaultDomainCount"를 5로 설정합니다. API 버전 2017-12-01에서 "platformFaultDomainCount"는 단일 영역 및 영역 간 확장 집합에 대한 기본값이 1로 설정됩니다. 현재 정적 5 장애 도메인 분산만 지역 확장 집합에 대해 지원됩니다.

또한 확장 집합을 배포할 때 가용성 영역당 단일 [배치 그룹](./virtual-machine-scale-sets-placement-groups.md) 또는 영역당 여러 배치 그룹을 사용하는 배포의 옵션이 있습니다(지역 확장 집합의 경우 선택은 영역에 단일 배치 그룹을 갖거나 영역에 여러 배치 그룹을 갖는 것임). 대부분의 작업의 경우 더 큰 확장을 허용하는 여러 배치 그룹을 사용하는 것이 좋습니다. API 버전 2017-12-01에서 확장 집합은 단일 영역 및 영역 간 확장 집합에 대해 여러 배치 그룹을 기본값으로 설정하지만 지역 확장 집합에 대해 단일 배치 그룹을 기본값으로 설정합니다.

>[!NOTE]
> 최대 분산을 사용하는 경우 여러 배치 그룹을 사용해야 합니다.

마지막으로 여러 영역에서 배포되는 확장 집합의 경우 "최상의 노력 영역 균형" 또는 "엄격한 영역 균형"을 선택하는 옵션을 갖습니다. 확장 집합은 각 영역에서 VM의 수가 확장 집합에 대한 다른 모든 영역의 VM 수 중 하나에 있는 경우 "균형"으로 간주됩니다. 인스턴스의 경우 영역 1에 2개의 VM, 영역 2에 3개의 VM, 영역 3에 3개의 VM이 있는 확장 집합은 균형으로 간주됩니다. 그러나 영역 1에 1개의 VM, 영역 2에 3개의 VM, 영역 3에 3개의 VM이 있는 확장 집합은 불균형으로 간주됩니다. 확장 집합에서 VM을 성공적으로 만드는 것은 가능하지만 해당 VM에서 확장은 실패합니다. 확장이 실패한 이러한 VM은 확장 집합이 분산된 경우를 결정할 때에 여전히 계산됩니다. 예를 들어 영역 1에 3개의 VM, 영역 2에 3개의 VM, 영역 3에 3개의 VM이 있는 확장 집합은 모든 확장이 영역 1에서 실패하고 모든 확장이 영역 2 및 3에서 성공한 경우에도 균형으로 간주됩니다. 최상의 노력 영역 균형을 사용하여 확장 집합은 균형을 유지하면서 규모 확장 및 축소를 시도합니다. 그러나 어떤 이유로 불가능한 경우(예: 하나의 영역이 다운되어 확장 집합에서 해당 영역에 새 VM을 만들 수 없는 경우) 확장 집합은 성공적으로 규모 확장 또는 축소를 위해 일시적인 불균형을 허용합니다. 후속 규모 확장 시도에서 확장 집합은 확장 집합이 균형을 이루기 위해 더 많은 VM이 필요한 영역에 VM을 추가합니다. 마찬가지로 후속 규모 축소 시도에서 확장 집합은 확장 집합이 균형을 이루기 위해 더 적은 VM이 필요한 영역에서 VM을 제거합니다. 반면에 "엄격한 영역 균형"을 사용하면 확장 집합은 규모 확장 또는 축소 시도에 실패하고 그렇게 하는 경우 불균형이 발생합니다.

최상의 노력 영역 균형을 사용하려면 "zoneBalance"를 false로 설정합니다(API 버전 2017-12-01에서 기본값). 엄격한 영역 균형을 사용하려면 "zoneBalance"를 true로 설정합니다.

## <a name="use-the-azure-portal"></a>Azure 포털 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-portal.md)에 자세히 설명된 프로세스와 같습니다. [가용성 영역 미리 보기에 등록](http://aka.ms/azenroll)했는지 확인합니다. 지원되는 Azure 지역을 선택할 때 다음 예제에 표시된 대로 사용 가능한 지역 중 하나에 확장 집합을 만들 수 있습니다.

![단일 가용성 영역에서 확장 집합 만들기](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

확장 집합과 Azure Load Balancer 및 공용 IP 주소와 같은 지원 리소스가 지정된 단일 지역에서 만들어집니다.


## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-cli.md)에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들고 [가용성 영역 미리 보기에 등록](http://aka.ms/azenroll)해야 합니다.

`--zones` 매개 변수를 [az vmss create](/cli/azure/vmss#az_vmss_create) 명령에 추가하고, 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다. 다음 예제에서는 영역 *1*에 *myScaleSet*이라는 단일 영역 확장 집합을 만듭니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```
단일 영역 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 CLI 스크립트](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)를 참조하세요.

### <a name="zone-redundant-scale-set"></a>영역 중복 확장 집합
영역 중복 확장 집합을 만들려면 *표준* SKU 공용 IP 주소 및 부하 분산 장치를 사용합니다. 향상된 중복성을 위해 *표준* SKU는 영역 중복 네트워크 리소스를 만듭니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../load-balancer/load-balancer-standard-overview.md)를 참조하세요. 

영역 중복 확장 집합을 만들려면 여러 영역을 `--zones` 매개 변수로 지정합니다. 다음 예제에서는 *1,2,3* 영역에 *myScaleSet*라는 영역 중복 확장 집합을 만듭니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

지정한 영역에 확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다. 영역 중복 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 CLI 스크립트](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)를 참조하세요.


## <a name="use-azure-powershell"></a>Azure PowerShell 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-powershell.md)에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들고 [가용성 영역 미리 보기에 등록](http://aka.ms/azenroll)해야 합니다. `-Zone` 매개 변수를 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 명령에 추가하고, 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다. 

다음 예제에서는 *미국 동부 2* 영역 *1*에 *vmssConfig*라는 단일 영역 확장 집합 구성을 만듭니다.

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

단일 영역 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 PowerShell 스크립트](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)를 참조하세요.

### <a name="zone-redundant-scale-set"></a>영역 중복 확장 집합
영역 중복 확장 집합을 만들려면 *표준* SKU 공용 IP 주소 및 부하 분산 장치를 사용합니다. 향상된 중복성을 위해 *표준* SKU는 영역 중복 네트워크 리소스를 만듭니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../load-balancer/load-balancer-standard-overview.md)를 참조하세요.

영역 중복 확장 집합을 만들려면 여러 영역을 `-Zone` 매개 변수로 지정합니다. 다음 예제에서는 *미국 동부 2* 영역 *1, 2, 3*에 *myScaleSet*라는 영역 중복 확장 집합 구성을 만듭니다.

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)를 사용하여 공용 IP 주소를 만들거나 [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer)를 사용하여 부하 분산 장치를 만드는 경우 *-SKU "Standard"*를 지정하여 영역 중복 네트워크 리소스를 만듭니다. 또한 모든 트래픽을 허용하는 네트워크 보안 그룹 및 규칙을 만들어야 합니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../load-balancer/load-balancer-standard-overview.md)를 참조하세요.

영역 중복 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 PowerShell 스크립트](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)를 참조하세요.


## <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows](quick-create-template-windows.md)용 시작 문서에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들고 [가용성 영역 미리 보기에 등록](http://aka.ms/azenroll)해야 합니다. `zones` 속성을 템플릿의 *Microsoft.Compute/virtualMachineScaleSets* 리소스 종류에 추가하고 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다.

다음 예제에서는 *미국 동부 2* 영역 *1*에 *myScaleSet*이라는 Linux 단일 영역 확장 집합을 만듭니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

단일 영역 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 Resource Manager 템플릿](https://github.com/Azure/vm-scale-sets/blob/master/zones/singlezone.json)을 참조하세요.

### <a name="zone-redundant-scale-set"></a>영역 중복 확장 집합
영역 중복 확장 집합을 만들려면 *Microsoft.Compute/virtualMachineScaleSets* 리소스 종류에 대한 `zones` 속성에 여러 값을 지정합니다. 다음 예제에서는 *미국 동부 2* 영역 *1,2,3*에 *myScaleSet*라는 영역 중복 확장 집합을 만듭니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

공용 IP 주소 또는 부하 분산 장치를 만드는 경우 *"sku": { "name": "Standard" }"* 속성을 지정하여 영역 중복 네트워크 리소스를 만듭니다. 또한 모든 트래픽을 허용하는 네트워크 보안 그룹 및 규칙을 만들어야 합니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../load-balancer/load-balancer-standard-overview.md)를 참조하세요.

영역 중복 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 Resource Manager 템플릿](https://github.com/Azure/vm-scale-sets/blob/master/zones/multizone.json)을 참조하세요.


## <a name="next-steps"></a>다음 단계
지금까지 가용성 영역에서 확장 집합을 만들었으므로 [가상 머신 확장 집합에 응용 프로그램 배포](virtual-machine-scale-sets-deploy-app.md) 또는 [가상 머신 확장 집합에 자동 크기 조정 사용](virtual-machine-scale-sets-autoscale-overview.md) 방법을 알아볼 수 있습니다.

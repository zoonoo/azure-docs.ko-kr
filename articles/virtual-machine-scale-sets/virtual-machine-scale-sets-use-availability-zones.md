---
title: "가용성 영역을 사용하는 Azure 확장 집합 만들기(미리 보기) | Microsoft Docs"
description: "가동 중단에 대비해서 중복성을 늘리기 위해 가용성 영역을 사용하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>가용성 영역을 사용하는 가상 머신 확장 집합 만들기(미리 보기)
데이터 센터 수준 오류로부터 가상 머신 확장 집합을 보호하려면 가용성 영역에서 확장 집합을 만들 수 있습니다. 가용성 영역을 지원하는 Azure 지역은 각각 독립적인 전원, 네트워크 및 냉각 기능을 갖춘 3개 이상의 별도 지역을 포함합니다. 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

가용성 영역을 사용하려면 [지원되는 Azure 지역](../availability-zones/az-overview.md#regions-that-support-availability-zones)에 확장 집합을 만들어야 합니다. 다음 방법 중 하나를 사용하여 가용성 영역을 사용하는 확장 집합을 만들 수 있습니다.

- [Azure 포털](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 리소스 관리자 템플릿](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Azure 포털 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](virtual-machine-scale-sets-create-portal.md)에 자세히 설명된 프로세스와 같습니다. 지원되는 Azure 지역을 선택할 때 다음 예제에 표시된 대로 사용 가능한 지역 중 하나에 확장 집합을 만들 수 있습니다.

![단일 가용성 영역에서 확장 집합 만들기](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

확장 집합과 Azure Load Balancer 및 공용 IP 주소와 같은 지원 리소스가 지정된 단일 지역에서 만들어집니다.


## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](virtual-machine-scale-sets-create-cli.md)에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들어야 합니다. `--zones` 매개 변수를 [az vmss create](/cli/azure/vmss#az_vmss_create) 명령에 추가하고, 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다. 다음 예제에서는 지역 *1*에 *myScaleSet*이라는 확장 집합을 만듭니다.

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

지정한 지역에 확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="use-azure-powershell"></a>Azure PowerShell 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](virtual-machine-scale-sets-create-powershell.md)에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들어야 합니다. `-Zone` 매개 변수를 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 명령에 추가하고, 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다. 다음 예제에서는 *미국 동부 2* 지역 *1*에 *vmssConfig*라는 확장 집합 구성을 만듭니다.

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

실제 확장 집합을 만들려면 [시작 문서](virtual-machine-scale-sets-create-powershell.md)에 설명된 대로 추가 단계를 따릅니다.


## <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용
가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [Linux](virtual-machine-scale-sets-create-template-linux.md) 또는 [Windows](virtual-machine-scale-sets-create-template-windows.md)용 시작 문서에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들어야 합니다. `zones` 속성을 템플릿의 *Microsoft.Compute/virtualMachineScaleSets* 리소스 종류에 추가하고 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다. 다음 예제에서는 *미국 동부 2* 지역 *1*에 *myScaleSet*이라는 Linux 확장 집합을 만듭니다.

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

실제 확장 집합을 만들려면 [Linux](virtual-machine-scale-sets-create-template-linux.md) 또는 [Windows](virtual-machine-scale-sets-create-template-windows.md)용 시작 문서에 설명된 대로 추가 단계를 따릅니다.


## <a name="next-steps"></a>다음 단계
지금까지 가용성 영역에서 확장 집합을 만들었으므로 [가상 머신 확장 집합에 응용 프로그램 배포](virtual-machine-scale-sets-deploy-app.md) 또는 [가상 머신 확장 집합에 자동 크기 조정 사용](virtual-machine-scale-sets-autoscale-overview.md) 방법을 알아볼 수 있습니다.

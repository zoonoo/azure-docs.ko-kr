---
title: Azure 가상 시스템 규모 집합을 사용하여 사용자 지정 확장 정책 사용
description: 자동 크기 조정 구성을 사용하여 인스턴스 수를 관리하는 Azure 가상 시스템 확장 집합을 사용하여 사용자 지정 확장 정책을 사용하는 방법에 대해 알아봅니다.
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919841"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Azure 가상 시스템 규모 집합을 사용하여 사용자 지정 확장 정책 사용

가상 시스템 규모 집합 배포는 플랫폼 및 사용자 정의 사용자 정의 메트릭을 비롯한 다양한 메트릭을 기반으로 확장 또는 확장할 수 있습니다. 확장 집합 모델을 기반으로 새 가상 컴퓨터를 만드는 동안 확장 집합 워크로드가 발전함에 따라 구성 및/또는 기능이 다를 수 있는 실행 중인 가상 시스템에 영향을 줍니다. 

확장 정책 기능은 사용자에게 세 가지 확장 구성을 통해 가상 시스템의 배율 조정 순서를 구성할 수 있는 방법을 제공합니다. 

1. 기본값
2. 최신 VM
3. 가장 오래된 VM

### <a name="default-scale-in-policy"></a>기본 확장 정책

기본적으로 가상 시스템 크기 집합은 이 정책을 적용하여 어떤 인스턴스의 배율을 조정할지 결정합니다. *기본* 정책에서는 다음과 같은 순서로 확장에 대해 VM이 선택됩니다.

1. 가용성 영역 간에 가상 컴퓨터의 균형 조정(확장 집합이 구역 구성으로 배포된 경우)
2. 장애 도메인 간에 가상 시스템의 균형 조정(최선의 노력)
3. 인스턴스 ID가 가장 높은 가상 시스템 삭제

기본 순서를 따르기를 원하는 경우 사용자는 확장 정책을 지정할 필요가 없습니다.

가용성 영역 또는 장애 도메인 간에 균형을 조정해도 가용성 영역 또는 장애 도메인 간에 인스턴스가 이동되지는 않습니다. 분산은 가상 시스템의 분산이 균형을 이룰 때까지 불균형 가용성 영역 또는 오류 도메인에서 가상 시스템을 삭제하여 수행됩니다.

### <a name="newestvm-scale-in-policy"></a>최신VM 확장 정책

이 정책은 가용성 영역(영역 배포의 경우)에서 VM의 균형을 조정한 후 규모 집합에서 새로 만든 가상 컴퓨터를 삭제합니다. 이 정책을 사용하도록 설정하려면 가상 시스템 규모 집합 모델에서 구성을 변경해야 합니다.

### <a name="oldestvm-scale-in-policy"></a>가장 오래된VM 확장 정책

이 정책은 가용성 영역(영역 배포의 경우)에서 VM의 균형을 조정한 후 규모 집합에서 가장 오래된 생성된 가상 컴퓨터를 삭제합니다. 이 정책을 사용하도록 설정하려면 가상 시스템 규모 집합 모델에서 구성을 변경해야 합니다.

## <a name="enabling-scale-in-policy"></a>확장 정책 사용

확장 정책은 가상 시스템 스케일 집합 모델에 정의되어 있습니다. 위의 섹션에서 설명한 것처럼 'NewestVM' 및 '가장 오래된 VM' 정책을 사용할 때는 확장 정책 정의가 필요합니다. 가상 시스템 규모 집합은 규모 집합 모델에 있는 확장 정책 정의가 없는 경우 '기본' 확장 정책을 자동으로 사용합니다. 

다음과 같은 방법으로 가상 시스템 스케일 집합 모델에서 확장 정책을 정의할 수 있습니다.

### <a name="azure-portal"></a>Azure portal
 
다음 단계에서는 새 축척 집합을 만들 때 확장 정책을 정의합니다. 
 
1. 가상 **컴퓨터 크기 집합으로**이동합니다.
1. **+ 추가를** 선택하여 새 축척 세트를 만듭니다.
1. **크기 조정** 탭으로 이동합니다. 
1. **배율 조정 정책** 섹션을 찾습니다.
1. 드롭다운에서 배율 조정 정책을 선택합니다.
1. 새 축척 세트 작성이 완료되면 **검토 + 만들기** 단추를 선택합니다.

### <a name="using-api"></a>API 사용

API 2019-03-01을 사용하여 가상 시스템 규모 집합에서 PUT을 실행합니다.

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

리소스 그룹을 만든 다음 확장 정책이 *가장 오래된 VM으로*설정된 새 축척 집합을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제에서는 새 축척 집합을 만드는 동안 확장 정책을 추가합니다. 먼저 리소스 그룹을 만든 다음 가장 *오래된 VM으로*확장 정책을 사용하여 새 축척 집합을 만듭니다. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>템플릿 사용

템플릿에서 "속성"에서 다음을 추가합니다.

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

위의 블록은 확장이 트리거될 때(자동 크기 조정 또는 수동 삭제를 통해) 가상 시스템 스케일 집합이 영역 균형 스케일 집합에서 가장 오래된 VM을 삭제하도록 지정합니다.

가상 시스템 배율 집합이 영역 균형이 맞지 않는 경우 배율 집합은 먼저 불균형 영역 전체에서 VM을 삭제합니다. 불균형 영역 내에서 배율 집합은 위에 지정된 배율 조정 정책을 사용하여 확장할 VM을 결정합니다. 이 경우 불균형 영역 내에서 배율 집합은 삭제할 해당 영역에서 가장 오래된 VM을 선택합니다.

비구역 가상 시스템 크기 집합의 경우 정책은 삭제를 위해 집합된 스케일 집합에서 가장 오래된 VM을 선택합니다.

위의 스케일 인 정책에서 'NewestVM'을 사용할 때도 동일한 프로세스가 적용됩니다.

## <a name="modifying-scale-in-policies"></a>확장 정책 수정

확장 정책을 수정하는 것은 확장 정책을 적용하는 것과 동일한 프로세스를 따릅니다. 예를 들어 위의 예제에서 정책을 '가장 오래된 VM'에서 'NewestVM'으로 변경하려는 경우 다음을 수행하여 변경할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure 포털을 통해 설정된 기존 축척의 확장 정책을 수정할 수 있습니다. 
 
1. 기존 가상 시스템 배율 집합에서 왼쪽 메뉴에서 **크기 조정을** 선택합니다.
1. 배율 정책 탭을 **선택합니다.**
1. 드롭다운에서 배율 조정 정책을 선택합니다.
1. 작업이 완료되면 **저장**을 선택합니다. 

### <a name="using-api"></a>API 사용

API 2019-03-01을 사용하여 가상 시스템 규모 집합에서 PUT을 실행합니다.

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

기존 축척 집합의 확장 정책 업데이트:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음은 기존 스케일 집합의 확장 정책을 업데이트하는 예제입니다. 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>템플릿 사용

템플릿에서 "속성"에서 아래와 같이 템플릿을 수정하고 다시 배포합니다. 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

'최신VM'을 '기본값' 또는 '가장 오래된 VM'으로 변경하기로 결정한 경우에도 동일한 프로세스가 적용됩니다.

## <a name="instance-protection-and-scale-in-policy"></a>인스턴스 보호 및 확장 정책

가상 시스템 크기 집합은 두 가지 유형의 [인스턴스 보호를 제공합니다.](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. 스케일 인으로부터 보호
2. 스케일 세트 작업으로부터 보호

보호된 가상 시스템은 적용된 확장 정책에 관계없이 확장 작업을 통해 삭제되지 않습니다. 예를 들어 VM_0(축척 집합에서 가장 오래된 VM)이 확장되지 않도록 보호되고 배율 집합에 '가장 오래된 VM' 확장 정책이 활성화된 경우 VM_0 확장된 VM인 경우에도 고려하지 않습니다. 

확장 집합에서 활성화된 확장 정책에 관계없이 보호된 가상 시스템은 언제든지 사용자가 수동으로 삭제할 수 있습니다. 

## <a name="usage-examples"></a>사용 예 

아래 예제에서는 가상 시스템 규모 집합이 스케일 인 이벤트가 트리거될 때 삭제할 VM을 선택하는 방법을 보여 줍니다. 인스턴스 가 가장 높은 가상 시스템은 스케일 집합에서 최신 VM으로 가정하고 가장 작은 인스턴스 아이디를 가진 VM은 스케일 집합에서 가장 오래된 VM으로 가정합니다. 

### <a name="oldestvm-scale-in-policy"></a>가장 오래된VM 확장 정책

| 행사                 | 영역 1의 인스턴스 아이디  | 영역 2의 인스턴스 아이디  | 영역 3의 인스턴스 아이디  | 배율 선택                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 스케일 인              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | 영역 3에 가장 오래된 VM이 있더라도 영역 1과 2 중에서 선택합니다. 해당 영역에서 가장 오래된 VM이기 때문에 영역 2에서 VM2를 삭제합니다.   |
| 스케일 인              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | 영역 3에 가장 오래된 VM이 있더라도 영역 1을 선택합니다. 해당 영역에서 가장 오래된 VM이기 때문에 영역 1에서 VM3를 삭제합니다.                  |
| 스케일 인              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | 영역은 균형을 이룹니다. 영역 3에서 배율 집합에서 가장 오래된 VM이기 때문에 VM1을 삭제합니다.                                               |
| 스케일 인              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | 영역 1과 영역 2 중에서 선택합니다. 두 영역에서 가장 오래된 VM이기 때문에 영역 1에서 VM4를 삭제합니다.                              |
| 스케일 인              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | 영역 1에 가장 오래된 VM이 있더라도 영역 2를 선택합니다. 영역 1에서 해당 영역에서 가장 오래된 VM으로 VM6를 삭제합니다.                    |
| 스케일 인              | ***5,*** 10            | 9, 11                  | 7, 8                   | 영역은 균형을 이룹니다. 영역 1에서 배율 집합에서 가장 오래된 VM이기 때문에 VM5를 삭제합니다.                                                |

비구역 가상 시스템 크기 집합의 경우 정책은 삭제를 위해 스케일 집합에서 가장 오래된 VM을 선택합니다. "보호된" VM은 삭제를 위해 건너뜁니다.

### <a name="newestvm-scale-in-policy"></a>최신VM 확장 정책

| 행사                 | 영역 1의 인스턴스 아이디  | 영역 2의 인스턴스 아이디  | 영역 3의 인스턴스 아이디  | 배율 선택                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 스케일 인              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | 영역 1과 2 중에서 선택합니다. 두 영역에서 최신 VM이기 때문에 영역 2에서 VM11을 삭제합니다.                                |
| 스케일 인              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | 다른 두 영역보다 더 많은 VM이 있으므로 영역 1을 선택합니다. 해당 영역의 최신 VM이기 때문에 영역 1에서 VM10을 삭제합니다.          |
| 스케일 인              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | 영역은 균형을 이룹니다. 영역 2에서 스케일 집합의 최신 VM이기 때문에 VM9를 삭제합니다.                                                |
| 스케일 인              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | 영역 1과 영역 3 중에서 선택합니다. 해당 영역의 최신 VM이기 때문에 영역 3에서 VM8을 삭제합니다.                                      |
| 스케일 인              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | 영역 3에 최신 VM이 있더라도 영역 1을 선택합니다. 영역 1에서 해당 영역의 최신 VM으로 VM5를 삭제합니다.                    |
| 스케일 인              | 3, 4                   | 2, 6                   | 1, ***7***             | 영역은 균형을 이룹니다. 영역 3에서 스케일 집합의 최신 VM이기 때문에 VM7을 삭제합니다.                                                |

비구역 가상 시스템 스케일 집합의 경우 정책은 삭제를 위해 스케일 집합에서 최신 VM을 선택합니다. "보호된" VM은 삭제를 위해 건너뜁니다. 

## <a name="troubleshoot"></a>문제 해결

1. scaleInPolicy를 활성화하지 못하는 경우 '속성' 형식의 개체에서 'scaleInPolicy'라는 오류 메시지가 있는 'BadRequest' 오류가 발생하면 가상 시스템 규모 집합에 사용되는 API 버전을 확인합니다. 이 기능에는 API 버전 2019-03-01 이상의 기능이 필요합니다.

2. 규모 에 대한 VM의 잘못된 선택은 위의 예제를 참조하십시오. 가상 시스템 확장 집합이 지역 배포인 경우 확장 정책이 먼저 불균형 영역에 적용된 다음 영역 균형이 잡히면 확장 집합에 적용됩니다. 확장 순서가 위의 예제와 일치하지 않는 경우 문제 해결을 위해 가상 시스템 규모 집합 팀과 함께 쿼리를 발생시킵니다.

## <a name="next-steps"></a>다음 단계

가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
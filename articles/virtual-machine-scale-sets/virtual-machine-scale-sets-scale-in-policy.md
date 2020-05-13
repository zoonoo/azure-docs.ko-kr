---
title: Azure 가상 머신 확장 집합에 사용자 지정 확장 정책 사용
description: 자동 크기 조정 구성을 사용 하 여 인스턴스 수를 관리 하는 Azure 가상 머신 확장 집합에서 사용자 지정 확장 정책을 사용 하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 479bbfaf8468329cd515799e5822497df2bb4c1d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125165"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합에 사용자 지정 확장 정책 사용

가상 머신 확장 집합 배포는 플랫폼 및 사용자 정의 사용자 지정 메트릭을 포함 하 여 메트릭 배열에 따라 확장 또는 축소할 수 있습니다. 스케일 아웃은 확장 집합 모델을 기반으로 새 가상 컴퓨터를 만들지만 확장 집합의 워크 로드가 진화 함에 따라 구성 및/또는 기능이 다를 수 있는 실행 중인 가상 컴퓨터에는 확장이 적용 됩니다. 

규모 확장 정책 기능을 통해 사용자는 다음과 같은 세 가지 규모의 구성으로 가상 컴퓨터를 확장 하는 순서를 구성할 수 있습니다. 

1. Default
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>기본 규모 조정 정책

기본적으로 가상 머신 확장 집합은이 정책을 적용 하 여 확장할 인스턴스를 결정 합니다. *기본* 정책을 사용 하면 다음과 같은 순서로 확장을 위해 vm을 선택할 수 있습니다.

1. 가용성 영역 간 가상 머신 균형 조정 (확장 집합이 영역 구성에 배포 된 경우)
2. 장애 도메인 간 가상 머신 균형 조정 (최상의 활동)
3. 가장 높은 인스턴스 ID의 가상 컴퓨터 삭제

기본 순서 지정을 수행 하려는 경우에는 사용자가 확장 정책을 지정할 필요가 없습니다.

가용성 영역 또는 장애 도메인 간의 분산은 가용성 영역 또는 장애 도메인 간에 인스턴스를 이동 하지 않습니다. 분산은 가상 컴퓨터의 배포가 분산 될 때까지 불균형 가용성 영역 또는 장애 도메인에서 가상 컴퓨터를 삭제 하는 방식으로 이루어집니다.

### <a name="newestvm-scale-in-policy"></a>NewestVM 확장 정책

이 정책은 가용성 영역 (영역 배포의 경우)에서 Vm을 분산 한 후 확장 집합에서 가장 최근에 만든 가상 머신을 삭제 합니다. 이 정책을 사용 하도록 설정 하려면 가상 머신 확장 집합 모델에 대 한 구성을 변경 해야 합니다.

### <a name="oldestvm-scale-in-policy"></a>OldestVM 규모 조정 정책

이 정책은 가용성 영역 (영역 배포의 경우)에서 Vm을 분산 한 후 확장 집합에서 가장 오래 전에 만든 가상 머신을 삭제 합니다. 이 정책을 사용 하도록 설정 하려면 가상 머신 확장 집합 모델에 대 한 구성을 변경 해야 합니다.

## <a name="enabling-scale-in-policy"></a>규모 확장 정책 사용

규모 확장 정책은 가상 머신 확장 집합 모델에서 정의 됩니다. 위의 섹션에서 설명한 것 처럼 ' NewestVM ' 및 ' OldestVM ' 정책을 사용할 때 확장 정책 정의가 필요 합니다. 확장 집합 모델에 확장 정책 정의가 없는 경우 가상 머신 확장 집합에서 ' 기본 ' 확장 정책을 자동으로 사용 합니다. 

다음과 같은 방법으로 가상 머신 확장 집합 모델에 대 한 확장 정책을 정의할 수 있습니다.

### <a name="azure-portal"></a>Azure portal
 
다음 단계는 새 확장 집합을 만들 때 확장 정책을 정의 합니다. 
 
1. **가상 머신 확장 집합**으로 이동 합니다.
1. **+ 추가** 를 선택 하 여 새 확장 집합을 만듭니다.
1. **크기 조정** 탭으로 이동 합니다. 
1. **크기 조정 정책** 섹션을 찾습니다.
1. 드롭다운에서 확장 정책을 선택 합니다.
1. 새 확장 집합 만들기를 완료 한 후 **검토 + 만들기** 단추를 선택 합니다.

### <a name="using-api"></a>API 사용

API 2019-03-01를 사용 하 여 가상 머신 확장 집합에 대 한 배치를 실행 합니다.

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

리소스 그룹을 만든 다음, 확장 정책을 *Oldestvm*으로 설정 하 여 새 확장 집합을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예에서는 새 확장 집합을 만드는 동안 확장 정책을 추가 합니다. 먼저 리소스 그룹을 만든 다음, 확장 정책을 사용 하 여 새 확장 집합을 *Oldestvm*으로 만듭니다. 

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

템플릿에서 "속성" 아래에 다음을 추가 합니다.

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

위의 블록은 크기 조정을 트리거할 때 (자동 크기 조정 또는 수동 삭제를 통해) 가상 머신 확장 집합을 통해 영역 균형 확장 집합에서 가장 오래 된 VM을 삭제 하도록 지정 합니다.

가상 머신 확장 집합이 영역 균형이 맞지 않으면 확장 집합은 먼저 불균형 영역에서 Vm을 삭제 합니다. 불균형 영역 내에서 확장 집합은 위에서 지정 된 확장 정책을 사용 하 여 확장할 VM을 결정 합니다. 이 경우 불균형 영역 내에서 확장 집합은 삭제할 해당 영역에서 가장 오래 된 VM을 선택 합니다.

비 영역 가상 머신 확장 집합의 경우 정책은 삭제를 위해 확장 집합에서 가장 오래 된 VM을 선택 합니다.

위의 확장 정책에서 ' NewestVM '를 사용 하는 경우에도 동일한 프로세스가 적용 됩니다.

## <a name="modifying-scale-in-policies"></a>규모 확장 정책 수정

규모 확장 정책을 수정 하는 것은 규모 확장 정책을 적용 하는 것과 동일한 프로세스를 따릅니다. 예를 들어 위의 예제에서 ' OldestVM '의 정책을 ' NewestVM '로 변경 하려면 다음을 수행 합니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal를 통해 기존 확장 집합의 확장 정책을 수정할 수 있습니다. 
 
1. 기존 가상 머신 확장 집합의 왼쪽에 있는 메뉴에서 **크기 조정** 을 선택 합니다.
1. **크기 조정 정책** 탭을 선택 합니다.
1. 드롭다운에서 확장 정책을 선택 합니다.
1. 작업이 완료되면 **저장**을 선택합니다. 

### <a name="using-api"></a>API 사용

API 2019-03-01를 사용 하 여 가상 머신 확장 집합에 대 한 배치를 실행 합니다.

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

기존 확장 집합의 규모 확장 정책을 업데이트 합니다.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

기존 확장 집합의 규모 확장 정책을 업데이트 하는 예는 다음과 같습니다. 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>템플릿 사용

템플릿의 "속성"에서 템플릿을 아래와 같이 수정 하 고 다시 배포 합니다. 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

' NewestVM '를 ' Default ' 또는 ' OldestVM '으로 변경 하기로 결정 한 경우에도 동일한 프로세스가 적용 됩니다.

## <a name="instance-protection-and-scale-in-policy"></a>인스턴스 보호 및 규모 확장 정책

가상 머신 확장 집합은 다음과 같은 두 가지 유형의 [인스턴스 보호](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)를 제공 합니다.

1. 규모에서 보호
2. 크기 집합 작업에서 보호

보호 된 가상 컴퓨터는 적용 된 확장 정책에 관계 없이 스케일 인 작업을 통해 삭제 되지 않습니다. 예를 들어 VM_0 (확장 집합에서 가장 오래 된 VM)이 규모 확장에서 보호 되 고 확장 집합에 ' OldestVM ' 스케일 인 정책이 설정 된 경우 확장 집합에서 가장 오래 된 VM 인 경우에도에서 크기를 조정 하는 것으로 간주 되지 않습니다 VM_0. 

확장 집합에서 사용 하도록 설정 된 확장 정책에 관계 없이 언제 든 지 보호 된 가상 컴퓨터를 수동으로 삭제할 수 있습니다. 

## <a name="usage-examples"></a>사용 예 

아래 예제에서는 가상 머신 확장 집합이 확장 이벤트를 트리거할 때 삭제할 Vm을 선택 하는 방법을 보여 줍니다. 인스턴스 Id가 가장 높은 가상 머신은 확장 집합의 최신 Vm으로 간주 되며, 인스턴스 Id가 가장 작은 Vm은 확장 집합에서 가장 오래 된 Vm으로 간주 됩니다. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM 규모 조정 정책

| 이벤트                 | 영역 1의 인스턴스 Id  | 영역 2의 인스턴스 Id  | 영역 3의 인스턴스 Id  | 선택 영역 확장                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 규모 확장              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | 영역 3에 가장 오래 된 VM이 있는 경우에도 영역 1와 2 중에서 선택 합니다. 해당 영역에서 가장 오래 된 VM 인 영역 2 V M 2에서 삭제 합니다.   |
| 규모 확장              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | 영역 3에 가장 오래 된 VM이 있는 경우에도 영역 1을 선택 합니다. 해당 영역에서 가장 오래 된 VM 인 영역 1 V M 3에서 삭제 합니다.                  |
| 규모 확장              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | 영역의 균형이 조정 됩니다. 크기 집합에서 가장 오래 된 VM 이므로 영역 3 VM1에서 삭제 합니다.                                               |
| 규모 확장              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | 영역 1와 영역 2 중에서 선택 합니다. 영역 1에서 VM4는 두 영역에서 가장 오래 된 VM 이므로 삭제 합니다.                              |
| 규모 확장              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | 영역 1에 가장 오래 된 VM이 있는 경우에도 영역 2을 선택 합니다. 영역 1에서 V M 6는 해당 영역에서 가장 오래 된 VM 이므로 삭제 합니다.                    |
| 규모 확장              | ***5***, 10            | 9, 11                  | 7, 8                   | 영역의 균형이 조정 됩니다. 크기 집합에서 가장 오래 된 VM 이므로 영역 1 VM5에서 삭제 합니다.                                                |

비 영역 가상 머신 확장 집합의 경우 정책은 삭제를 위해 확장 집합에서 가장 오래 된 VM을 선택 합니다. "보호 된" VM은 삭제를 건너뛸 수 있습니다.

### <a name="newestvm-scale-in-policy"></a>NewestVM 확장 정책

| 이벤트                 | 영역 1의 인스턴스 Id  | 영역 2의 인스턴스 Id  | 영역 3의 인스턴스 Id  | 선택 영역 확장                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 규모 확장              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | 영역 1와 2 중에서 선택 합니다. 영역 2에서 VM11는 두 영역에 있는 최신 VM 이므로 삭제 합니다.                                |
| 규모 확장              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | 다른 두 영역 보다 더 많은 Vm이 있으므로 영역 1을 선택 합니다. 영역 1에서 VM10을 삭제 합니다 .이는 해당 영역에서 최신 VM입니다.          |
| 규모 확장              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | 영역의 균형이 조정 됩니다. 크기 집합의 최신 VM 이므로 영역 2 VM9에서 삭제 합니다.                                                |
| 규모 확장              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | 영역 1와 영역 3 중에서 선택 합니다. 영역 3에서 VM8는 해당 영역에서 최신 VM 이므로 삭제 합니다.                                      |
| 규모 확장              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | 영역 3에 최신 VM이 있는 경우에도 영역 1을 선택 합니다. 영역 1에서 VM5는 해당 영역에서 최신 VM 이므로 삭제 합니다.                    |
| 규모 확장              | 3, 4                   | 2, 6                   | 1, ***7***             | 영역의 균형이 조정 됩니다. 크기 집합의 최신 VM 이므로 영역 3 VM7에서 삭제 합니다.                                                |

비 영역 가상 머신 확장 집합의 경우 정책은 삭제를 위해 확장 집합에서 최신 VM을 선택 합니다. "보호 된" VM은 삭제를 건너뛸 수 있습니다. 

## <a name="troubleshoot"></a>문제 해결

1. ' 속성 ' 형식의 개체에서 ' scaleInPolicy ' 멤버를 찾을 수 없습니다. 라는 오류 메시지와 함께 ' BadRequest ' 오류가 발생 하는 경우 scaleInPolicy를 사용 하도록 설정 하지 못했습니다. 가상 머신 확장 집합에 사용 되는 API 버전을 확인 하세요. 이 기능을 수행 하려면 API 버전 2019-03-01 이상이 필요 합니다.

2. 규모 확장을 위해 Vm을 잘못 선택 하는 것은 위의 예를 참조 하세요. 가상 머신 확장 집합이 영역 배포 인 경우 확장 정책은 먼저 불균형 영역에 적용 된 후 영역에 분산 된 후에 확장 집합 전체에 적용 됩니다. 규모의 순서가 위의 예제와 일치 하지 않는 경우 문제 해결을 위해 가상 머신 확장 집합 팀을 사용 하 여 쿼리를 발생 시킵니다.

## <a name="next-steps"></a>다음 단계

가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.

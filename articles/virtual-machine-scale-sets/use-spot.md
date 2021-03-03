---
title: Azure 스팟 Virtual Machines를 사용 하는 확장 집합 만들기
description: Azure 스팟 Virtual Machines를 사용 하 여 비용을 절감 하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b20a5bd9c06c3948097389d5439defa219a7931b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694991"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>가상 머신 확장 집합에 대 한 Azure 스팟 Virtual Machines 

확장 집합에서 Azure 스팟 Virtual Machines를 사용 하면 비용을 크게 절약 하면서 사용 하지 않은 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 azure 인프라는 Azure 스폿 가상 머신 인스턴스를 제거 합니다. 따라서 Azure 스폿 가상 머신 인스턴스는 일괄 처리 작업, 개발/테스트 환경, 대규모 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

사용 가능한 용량의 크기는 크기, 지역, 시간 등에 따라 달라질 수 있습니다. 확장 집합에 Azure 스폿 가상 머신 인스턴스를 배포 하는 경우 Azure는 사용 가능한 용량이 있는 경우에만 인스턴스를 할당 하지만 이러한 인스턴스에 대 한 SLA는 없습니다. Azure 스폿 가상 머신 확장 집합은 단일 장애 도메인에 배포 되 고 고가용성 보장을 제공 하지 않습니다.


## <a name="pricing"></a>가격 책정

Azure 스폿 가상 머신 인스턴스에 대 한 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)가격 책정을 참조 하세요. 


가변 가격 책정을 사용 하는 경우 최대 5 개의 소수 자릿수를 사용 하 여 미국 달러 (USD)로 최대 가격을 설정 하는 옵션을 사용할 수 있습니다. 예를 들어 값은 `0.98765` 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하면 `-1` 인스턴스는 가격에 따라 제거 되지 않습니다. 인스턴스의 가격은 Azure 스폿 Virtual Machine의 현재 가격 이거나, 사용 가능한 용량 및 할당량을 초과 하는 경우 더 작은 표준 인스턴스의 가격입니다.


## <a name="limitations"></a>제한 사항

Azure 지점 Virtual Machines에 대해 지원 되지 않는 크기는 다음과 같습니다.
 - B 시리즈
 - 모든 크기의 프로 모션 버전 (예: Dv2, NV, NC, H 프로 모션 크기)

Azure 스폿 가상 머신은 Microsoft Azure 중국 21Vianet을 제외 하 고 모든 지역에 배포할 수 있습니다.

<a name="channel"></a>

현재 지원 되는 [제품 유형은](https://azure.microsoft.com/support/legal/offer-details/) 다음과 같습니다.

-   기업 계약
-   종 량 제 제품 코드 003P
-   후원
- CSP (클라우드 서비스 공급자)의 경우 [파트너 센터](https://docs.microsoft.com/partner-center/azure-plan-get-started) 를 참조 하거나 파트너에 게 직접 문의 하세요.

## <a name="eviction-policy"></a>제거 정책

Azure 지점 Virtual Machines를 사용 하 여 확장 집합을 만들 때 제거 정책을 *할당* 취소 (기본값) 또는 *삭제* 로 설정할 수 있습니다. 

*할당* 취소 정책은 제거 된 인스턴스를 제거 된 인스턴스를 다시 배포할 수 있도록 중지-할당 취소 된 상태로 이동 합니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소된 VM은 확장 집합 인스턴스 할당량에 따라 계산되며 기본 디스크에 대한 요금이 청구됩니다. 

인스턴스가 제거 될 때 삭제 되도록 하려면 제거 정책을 *삭제* 로 설정 하면 됩니다. 삭제하도록 제거 정책을 설정하면 확장 집합 인스턴스 수 속성을 늘려 새 VM을 만들 수 있습니다. 제거된 VM은 기본 디스크와 함께 삭제되므로 스토리지에 대한 요금이 청구되지 않습니다. 확장 집합의 자동 크기 조정 기능을 사용하여 제거된 VM을 자동으로 시도하고 보정할 수 있지만 성공적인 할당을 보장하지는 않습니다. 디스크 비용을 방지 하 고 할당량 한도에 도달 하는 것을 방지 하기 위해 제거 정책을 삭제로 설정 하는 경우 Azure 스팟 가상 머신 확장 집합에서 자동 크기 조정 기능만 사용 하는 것이 좋습니다. 

사용자는 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)를 통해 VM 내 알림을 받도록 옵트인 (opt in) 할 수 있습니다. 이렇게 하면 Vm을 제거 하는 경우에 알림이 표시 되며, 제거 되기 전에 작업을 완료 하 고 종료 작업을 수행 하는 데 30 초 정도 걸립니다. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>& 복원 시도 (미리 보기)

이 새로운 플랫폼 수준 기능은 AI를 사용 하 여 대상 인스턴스 수를 유지 하기 위해 확장 집합 내에서 제거 된 Azure 스폿 가상 머신 인스턴스를 자동으로 복원 하려고 시도 합니다. 

> [!IMPORTANT]
> & 복원은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

& 복원 혜택을 시도 합니다.
- 용량으로 인해 제거 되는 Azure 스팟 Virtual Machines 복원을 시도 합니다.
- 복원 된 Azure 스폿 Virtual Machines는 용량이 트리거된 제거의 확률 보다 더 긴 기간 동안 실행 될 것으로 예상 됩니다.
- Azure 스팟 가상 머신의 수명을 개선 하므로 워크 로드는 더 긴 기간 동안 실행 됩니다.
- 는 종 량 제 Vm에 대해 이미 존재 하는 대상 수 기능을 유지 관리 하는 것과 유사 하 게 Azure 스폿 Virtual Machines의 대상 수를 유지 관리 하는 Virtual Machine Scale Sets 수 있습니다.

[자동 크기 조정을](virtual-machine-scale-sets-autoscale-overview.md)사용 하는 크기 집합에서 복원이 사용 하지 않도록 설정 & 합니다. 크기 집합의 Vm 수는 자동 크기 조정 규칙에 따라 결정 됩니다.

### <a name="register-for-try--restore"></a>& 복원 시도 등록

& 복원 시도 기능을 사용 하려면 먼저 미리 보기에 대 한 구독을 등록 해야 합니다. 등록을 완료 하는 데 몇 분 정도 걸릴 수 있습니다. Azure CLI 또는 PowerShell을 사용 하 여 기능 등록을 완료할 수 있습니다.


**CLI 사용**

[Az feature register](/cli/azure/feature#az-feature-register) 를 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다. 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**PowerShell 사용** 

[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet을 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다. 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>배치 그룹

배치 그룹은 자체 장애 도메인 및 업그레이드 도메인을 포함 하는 Azure 가용성 집합과 비슷한 구문입니다. 기본적으로 확장 집합은 최대 100대의 VM을 갖춘 단일 배치 그룹으로 구성됩니다. 확장 집합 속성이 `singlePlacementGroup` *false* 로 설정 된 경우 확장 집합은 여러 배치 그룹으로 구성 될 수 있으며 범위는 0 ~ 1000 인 vm입니다. 

> [!IMPORTANT]
> HPC와 함께 Infiniband를 사용 하지 않는 경우 확장 집합 속성을 false로 설정 하 여 `singlePlacementGroup` 여러  배치 그룹을 사용 하 여 지역 또는 영역 전체의 크기를 조정 하는 것이 좋습니다. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>확장 집합에 Azure 스팟 Virtual Machines 배포

확장 집합에 Azure 스폿 Virtual Machines을 배포 하려면 새 *우선 순위* *플래그를 지정 합니다.* 확장 집합의 모든 Vm이 지점으로 설정 됩니다. Azure 스폿 Virtual Machines를 사용 하 여 확장 집합을 만들려면 다음 방법 중 하나를 사용 합니다.
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 리소스 관리자 템플릿](#resource-manager-templates)

## <a name="portal"></a>포털

Azure 스폿 Virtual Machines를 사용 하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-portal.md)에 자세히 설명 된 것과 동일 합니다. 확장 집합을 배포할 때 별색 플래그를 설정 하 고 제거 정책: ![ Azure 지점을 사용 하 여 확장 집합 만들기를 선택할 수 있습니다 Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Azure 스폿 Virtual Machines를 사용 하 여 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-cli.md)에 자세히 설명 된 것과 동일 합니다. '--우선 순위 '를 추가 하 고를 추가 하기만 하면 `--max-price` 됩니다. 이 예에서는에를 사용 하 여 `-1` `--max-price` 가격에 따라 인스턴스를 제거 하지 않습니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Azure 스폿 Virtual Machines를 사용 하 여 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-powershell.md)에 자세히 설명 된 것과 동일 합니다.
'-Priority 스폿 '을 추가 하 고 AzVmssConfig에를 제공 `-max-price` 합니다. [](/powershell/module/az.compute/new-azvmssconfig)

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

Azure 스폿 Virtual Machines를 사용 하는 확장 집합을 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows](quick-create-template-windows.md)용 시작 문서에 설명 된 것과 동일 합니다. 

Azure 스팟 가상 머신 템플릿 배포의 경우 이상을 사용 `"apiVersion": "2019-03-01"` 합니다. 

`priority`, 및 속성을 섹션에 추가 하 `evictionPolicy` `billingProfile` `"virtualMachineProfile":` 고 `"singlePlacementGroup": false,` 속성을 `"Microsoft.Compute/virtualMachineScaleSets"` 템플릿의 섹션에 추가 합니다.

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

제거 된 인스턴스를 삭제 하려면 `evictionPolicy` 매개 변수를로 변경 `Delete` 합니다.


## <a name="simulate-an-eviction"></a>제거 시뮬레이션

Azure 스폿 가상 머신의 [제거를 시뮬레이트하여](https://docs.microsoft.com/rest/api/compute/virtualmachines/simulateeviction) 응용 프로그램이 갑작스러운 제거에 얼마나 잘 대응 하는지 테스트할 수 있습니다. 

다음을 사용자의 정보로 바꿉니다. 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` 시뮬레이트된 제거를 성공적으로 완료 했음을 의미 합니다. 

## <a name="faq"></a>FAQ

**Q:** 만든 후에는 Azure 스폿 가상 머신 인스턴스가 표준 인스턴스와 동일 합니까?

**A:** 예. 단, Azure 스폿 Virtual Machines에 대 한 SLA는 없으며 언제 든 지 제거할 수 있습니다.


**Q:** 제거 되었지만 여전히 용량이 필요한 경우 수행할 작업

**A:** 용량이 필요한 경우 Azure 지점 Virtual Machines 대신 표준 Vm을 사용 하는 것이 좋습니다.


**Q:** Azure Virtual Machine에 대 한 할당량은 어떻게 관리 되나요?

**A:** Azure 스폿 가상 머신 인스턴스 및 표준 인스턴스는 별도의 할당량 풀을 갖게 됩니다. Azure 스폿 가상 머신 할당량은 Vm과 확장 집합 인스턴스 간에 공유 됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.


**Q:** Azure 스팟 가상 컴퓨터에 대 한 추가 할당량을 요청할 수 있나요?

**A:** 예, [표준 할당량 요청 프로세스](../azure-portal/supportability/per-vm-quota-requests.md)를 통해 Azure 지점 Virtual Machines의 할당량을 늘리기 위해 요청을 제출할 수 있습니다.


**Q:** 기존 확장 집합을 Azure 스폿 가상 머신 확장 집합으로 변환할 수 있나요?

**A:** 아니요 `Spot` . 플래그 설정은 만들 때에만 지원 됩니다.


**Q:** `low` 낮은 우선 순위의 확장 집합에를 사용 하는 경우를 대신 사용 해야 하나요 `Spot` ?

**A:** 지금은 `low` 및 `Spot` 가 모두 작동 하지만를 사용 하도록 전환 하기 시작 해야 합니다 `Spot` .


**Q:** 일반 Vm과 Azure 스폿 Virtual Machines를 모두 사용 하 여 확장 집합을 만들 수 있나요?

**A:** 아니요. 확장 집합은 둘 이상의 우선 순위 유형을 지원할 수 없습니다.


**Q:**  Azure 스폿 가상 머신 확장 집합에 자동 크기 조정을 사용할 수 있나요?

**A:** 예, Azure 스폿 가상 머신 확장 집합에 자동 크기 조정 규칙을 설정할 수 있습니다. Vm을 제거 하는 경우 자동 크기 조정에서 새 Azure 스폿 Virtual Machines를 만들 수 있습니다. 그렇지만 이 용량은 보장되지 않습니다. 


**Q:**  자동 크기 조정은 제거 정책 (할당 취소 및 삭제) 모두에서 작동 하나요?

**A:** 예. 그러나 자동 크기 조정을 사용 하는 경우 삭제 하도록 제거 정책을 설정 하는 것이 좋습니다. 할당 취소된 인스턴스가 확장 집합에서 용량 수에 따라 계산되기 때문입니다. 자동 크기 조정을 사용할 때 할당 취소되고 제거된 인스턴스로 인해 신속하게 대상 인스턴스 수를 달성할 수 있을 것 같습니다. 또한 크기 조정 작업은 스폿 제거의 영향을 받을 수 있습니다. 예를 들어 가상 머신 확장 집합 인스턴스는 크기 조정 작업 중 여러 지점 제거로 인해 설정 된 최소 개수 보다 작을 수 있습니다. 


**Q:** 어디에서 질문을 게시할 수 있나요?

**A:** `azure-spot` [Q&A](/answers/topics/azure-spot.html)를 사용 하 여 질문을 게시 하 고 태그를 지정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

가격 책정에 대한 자세한 내용은 [가상 머신 확장 집합 가격 페이지](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)를 확인하세요.

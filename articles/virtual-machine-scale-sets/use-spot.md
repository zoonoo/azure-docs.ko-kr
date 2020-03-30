---
title: Azure 스팟 VM(미리 보기)을 사용하는 축척 집합 만들기
description: 스팟 VM을 사용하여 비용을 절감하는 Azure 가상 시스템 크기 집합을 만드는 방법에 대해 알아봅니다.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 37e914fe6bafe9587be525faf3e01c897cdd8230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162687"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>미리 보기: 가상 시스템 규모 집합에 대한 Azure 스팟 VM 

확장 집합에서 Azure Spot을 사용하면 사용하지 않는 용량을 크게 절약할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 Spot 인스턴스를 제거합니다. 따라서 스팟 인스턴스는 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 적합합니다.

사용 가능한 용량의 양은 크기, 지역, 시간대 등에 따라 달라질 수 있습니다. 크기 조정 집합에 스팟 인스턴스를 배포할 때 Azure는 사용 가능한 용량이 있는 경우에만 인스턴스를 할당하지만 이러한 인스턴스에 대한 SLA는 없습니다. 스팟 스케일 세트는 단일 오류 도메인에 배포되며 고가용성 보장을 제공하지 않습니다.

> [!IMPORTANT]
> 스팟 인스턴스는 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="pricing"></a>가격 책정

스팟 인스턴스의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)가격 책정을 참조하십시오. 


가변 가격을 사용하면 최대 소수점 이하 장소를 사용하여 최대 가격(USD)으로 설정할 수 있습니다. 예를 들어 값은 `0.98765`시간당 0.98765 USD의 최대 가격입니다. 최대 가격을 `-1`설정하면 가격에 따라 인스턴스가 제거되지 않습니다. 인스턴스의 가격은 현재 스팟 가격 또는 표준 인스턴스의 가격이 되며, 용량과 할당량을 사용할 수 있는 한 더 적습니다.

## <a name="eviction-policy"></a>퇴거 정책

스팟 축척 세트를 만들 때 제거 정책을 *Deallocate(기본값)* 또는 *삭제로*설정할 수 있습니다. 

*Deallocate* 정책은 제거된 인스턴스를 할당 이정지된 상태로 이동하여 제거된 인스턴스를 다시 배포할 수 있도록 합니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소된 VM은 확장 집합 인스턴스 할당량에 따라 계산되며 기본 디스크에 대한 요금이 청구됩니다. 

스팟 배율의 인스턴스가 제거될 때 삭제하도록 설정하려면 제거 정책을 *설정하여 삭제할*수 있습니다. 삭제하도록 제거 정책을 설정하면 확장 집합 인스턴스 수 속성을 늘려 새 VM을 만들 수 있습니다. 제거된 VM은 기본 디스크와 함께 삭제되므로 스토리지에 대한 요금이 청구되지 않습니다. 확장 집합의 자동 크기 조정 기능을 사용하여 제거된 VM을 자동으로 시도하고 보정할 수 있지만 성공적인 할당을 보장하지는 않습니다. 디스크 비용을 피하고 할당량 한도에 부딪히지 않도록 삭제할 제거 정책을 설정할 때 스팟 스케일 집합에서 자동 크기 조정 기능만 사용하는 것이 좋습니다. 

사용자는 [Azure 예약 이벤트를](../virtual-machines/linux/scheduled-events.md)통해 VM 내 알림을 수신하도록 옵트인할 수 있습니다. 이렇게 하면 VM이 제거되고 있는지 알 수 있으며 30초 동안 작업을 완료하고 제거전에 종료 작업을 수행할 수 있습니다. 


## <a name="deploying-spot-vms-in-scale-sets"></a>스케일 세트에 스팟 VM 배포

배율 집합에 스팟 VM을 배포하려면 새 *우선 순위* 플래그를 *스팟으로*설정할 수 있습니다. 스케일 세트의 모든 VM은 스팟으로 설정됩니다. 스팟 VM을 사용하여 축척 세트를 만들려면 다음 방법 중 하나를 사용합니다.
- [Azure 포털](#portal)
- [Azure CLI](#azure-cli)
- [Azure 파워쉘](#powershell)
- [Azure 리소스 관리자 템플릿](#resource-manager-templates)

## <a name="portal"></a>포털

스팟 VM을 사용하는 축척 집합을 만드는 프로세스는 [시작 문서에서](quick-create-portal.md)자세히 설명한 것과 동일합니다. 축척 세트를 배포할 때 스팟 플래그 및 제거 정책을 설정하도록 선택할 ![수 있습니다.](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

스팟 VM을 사용하여 배율 집합을 만드는 프로세스는 [시작 문서에서](quick-create-cli.md)자세히 설명한 것과 동일합니다. '--우선 순위 스팟'을 추가하고 `--max-price`. 이 예제에서는 가격에 `-1` `--max-price` 따라 인스턴스가 제거되지 않도록 사용합니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

스팟 VM을 사용하여 배율 집합을 만드는 프로세스는 [시작 문서에서](quick-create-powershell.md)자세히 설명한 것과 동일합니다.
그냥 '우선 순위 자리'를 `-max-price` [추가하고, 새로운 AzVmssConfig에](/powershell/module/az.compute/new-azvmssconfig)공급 .

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

스팟 VM을 사용하는 스케일 세트를 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows용](quick-create-template-windows.md)시작 아티클에 자세히 설명된 것과 동일합니다. 

스팟 템플릿 배포의`"apiVersion": "2019-03-01"` 경우 사용 하거나 나중에 사용합니다. 템플릿의 `priority` `evictionPolicy` 섹션에 `billingProfile` `"virtualMachineProfile":` 및 의 속성을 추가합니다. 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

인스턴스가 제거된 후 삭제하려면 매개 변수를 `evictionPolicy` `Delete`로 변경합니다.

## <a name="faq"></a>FAQ

**Q:** 생성된 스팟 인스턴스는 표준 인스턴스와 동일합니까?

**A:** 예. 스팟 VM에 대한 SLA가 없으며 언제든지 제거 할 수 있습니다.


**Q:** 퇴거당할 때 어떻게 해야 하나요, 하지만 여전히 용량이 필요합니까?

**A:** 용량이 필요한 경우 스팟 VM 대신 표준 VM을 사용하는 것이 좋습니다.


**Q:** 스팟에 대한 할당량은 어떻게 관리하나요?

**A:** 스팟 인스턴스와 표준 인스턴스에는 별도의 할당량 풀이 있습니다. 스팟 할당량은 VM과 배율 집합 인스턴스 간에 공유됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)을 참조하세요.


**Q:** 스팟에 대한 추가 할당량을 요청할 수 있나요?

**A:** 예. [표준 할당량 요청 프로세스를](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)통해 스팟 VM에 대한 할당량을 늘리는 요청을 제출할 수 있습니다.


**Q:** 기존 축척 세트를 스팟 축척 세트로 변환할 수 있습니까?

**A:** 아니요, `Spot` 플래그 설정은 생성 시에만 지원됩니다.


**Q:** 우선 순위가 `low` 낮은 배율 집합에 사용하는 경우 대신 `Spot` 사용을 시작해야 합니까?

**A:** 지금은 둘 `low` 다 `Spot` 작동하지만 을 사용하여 `Spot`전환을 시작해야 합니다.


**Q:** 일반 VM과 스팟 VM을 모두 사용하여 스케일 세트를 만들 수 있습니까?

**A:** 아니요, 축척 집합은 두 개 이상의 우선 순위 유형을 지원할 수 없습니다.


**Q:**  스팟 스케일 세트로 자동 배율을 사용할 수 있습니까?

**A:** 예. 스팟 배율 세트에서 자동 크기 조정 규칙을 설정할 수 있습니다. VM이 제거된 경우 자동 크기 조정을 통해 새 스팟 VM을 만들 수 있습니다. 그렇지만 이 용량은 보장되지 않습니다. 


**Q:**  자동 크기 조정이 제거 정책(할당 할당 및 삭제)에서 모두 작동합니까?

**A:** 자동 크기 조정을 사용할 때 삭제되도록 제거 정책을 설정하는 것이 좋습니다. 할당 취소된 인스턴스가 확장 집합에서 용량 수에 따라 계산되기 때문입니다. 자동 크기 조정을 사용할 때 할당 취소되고 제거된 인스턴스로 인해 신속하게 대상 인스턴스 수를 달성할 수 있을 것 같습니다. 


**Q:** 스팟 VM을 지원하는 채널은 무엇입니까?

**A:** 스팟 VM 가용성은 아래 표를 참조하십시오.

<a name="channel"></a>

| Azure 채널               | Azure 스팟 VM 가용성       |
|------------------------------|-----------------------------------|
| 기업 계약         | yes                               |
| 종량제 통화 요금                | yes                               |
| CSP(클라우드 서비스 공급자) | [파트너에게 문의하기](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 이점                     | 사용할 수 없음                     |
| 스폰서                    | 사용할 수 없음                     |
| 평가판                   | 사용할 수 없음                     |


**Q:** 어디에서 질문을 게시할 수 있습니까?

**A:** Q&`azure-spot` [A.](https://docs.microsoft.com/answers/topics/azure-spot.html) 

## <a name="next-steps"></a>다음 단계
스팟 VM을 사용하여 배율 집합을 만들었으니 스팟 을 [사용하여 자동 축척 템플릿을](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri)배포해 보십시오.

가격 책정에 대한 자세한 내용은 [가상 머신 확장 집합 가격 페이지](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)를 확인하세요.

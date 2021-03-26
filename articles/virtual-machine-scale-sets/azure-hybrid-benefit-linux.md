---
title: Linux 가상 머신 확장 집합에 대 한 Azure 하이브리드 혜택
description: Azure에서 실행 되는 Linux 가상 컴퓨터에 비용을 절감할 수 있도록 가상 컴퓨터 확장 집합에 Azure 하이브리드 혜택를 적용할 수 있는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: 09e41b4071ad02120f7d303b32bc87bc0ba39e2c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549300"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Linux 가상 머신 확장 집합에 대 한 Azure 하이브리드 혜택 (공개 미리 보기)

**Linux 가상 머신 확장 집합에 대 한 Azure 하이브리드 혜택 현재 공개 미리 보기 상태** 입니다. AHB 혜택을 통해 RHEL 및 SLES [가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)을 실행 하는 비용을 줄일 수 있습니다.

이 혜택을 통해 확장 집합의 인프라 비용에 대해서만 비용을 지불 하면 됩니다. 이 혜택은 모든 RHEL 및 SLES Marketplace PAYG (종 량 제) 이미지에 사용할 수 있습니다.


>[!NOTE]
> 이 문서에서는 Linux VMSS에 대 한 Azure 하이브리드 혜택 설명 합니다. 2020 년 11 월부터 Azure 고객이 이미 사용할 수 있는 [Linux vm에 대 한](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux)별도의 [문서를 여기 AHB에서 사용할 수 있습니다.

## <a name="benefit-description"></a>혜택 설명
Azure Hybrid를 사용 하면 Red Hat 또는 SUSE의 기존 클라우드 액세스 라이선스를 사용 하 고 가상 머신 확장 집합 인스턴스를 사용자 소유의 구독 (BYOS) 청구로 유연 하 게 변환할 수 있습니다. 

PAYG marketplace 이미지에서 배포 된 가상 머신 확장 집합은 인프라 및 소프트웨어 요금을 모두 청구 합니다. AHB를 사용 하 여 PAYG 인스턴스를 다시 배포 하지 않고 BYOS 청구 모델로 변환할 수 있습니다.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Linux Vm에 대 한 비용 시각화를 Azure 하이브리드 혜택 합니다.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Linux에 대 한 Azure 하이브리드 혜택 자격의 범위
Azure 하이브리드 혜택은 Azure Marketplace의 모든 RHEL 및 SLES PAYG 이미지에 사용할 수 있습니다. RHEL 또는 SLES BYOS 이미지 또는 Azure Marketplace의 사용자 지정 이미지에 대 한 혜택은 아직 제공 되지 않습니다.

Linux Vm에서 혜택을 이미 사용 하 고 있는 경우 Azure 전용 호스트 인스턴스 및 SQL 하이브리드 혜택을 Azure 하이브리드 혜택 수 없습니다.

## <a name="get-started"></a>시작하기

### <a name="red-hat-customers"></a>Red Hat 고객

RHEL에 대 한 Azure 하이브리드 혜택는 다음 조건을 모두 충족 하는 Red Hat 고객에 게 제공 됩니다.

- Azure에서 사용할 수 있는 활성 또는 사용 하지 않는 RHEL 구독 보유
- [Red Hat 클라우드 액세스](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 프로그램을 사용 하 여 Azure에서 사용 하기 위해 이러한 구독을 하나 이상 사용 하도록 설정 했습니다.

> [!IMPORTANT]
> [클라우드 액세스](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 프로그램에서 올바른 구독을 사용 하도록 설정 했는지 확인 합니다.

Red Hat의 혜택 사용을 시작 하려면 다음을 수행 합니다.

1. [Red Hat Cloud Access customer 인터페이스](https://access.redhat.com/management/cloud)를 사용 하 여 Azure에서 적격 RHEL 구독을 사용 하도록 설정 합니다.

   Red Hat Cloud Access 사용 프로세스 중에 제공 하는 Azure 구독은 Azure 하이브리드 혜택 기능을 사용 하도록 허용 됩니다.
1. 기존 및 새 RHEL PAYG 가상 머신 확장 집합에 Azure 하이브리드 혜택을 적용 합니다. Azure Portal 또는 Azure CLI를 사용 하 여 혜택을 사용 하도록 설정할 수 있습니다.
1. RHEL Vm에 대 한 업데이트 원본을 구성 하는 데 권장 되는 [다음 단계](https://access.redhat.com/articles/5419341) 와 RHEL 구독 준수 지침을 따릅니다.


### <a name="suse-customers"></a>SUSE 고객

SUSE에 대 한 혜택 사용을 시작 하려면 다음을 수행 합니다.

1. SUSE 공용 클라우드 프로그램에 등록 합니다.
1. Azure Portal 또는 Azure CLI를 통해 새로 만들었거나 기존 가상 머신 확장 집합에 혜택을 적용 합니다.
1. 별도의 업데이트 원본으로 혜택을 받는 Vm을 등록 합니다.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Azure Portal에서 혜택 사용 및 사용 안 함 
가상 머신 확장 집합에서 AHB를 사용 하거나 사용 하지 않도록 설정 하는 포털 환경을 **현재 사용할 수 없습니다**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Azure CLI를 사용 하 여 혜택 사용 및 사용 안 함

명령을 사용 하 여 `az vmss update` 기존 vm을 업데이트할 수 있습니다. RHEL Vm의 경우 매개 변수를 사용 하 여 명령을 실행 합니다 `--license-type` `RHEL_BYOS` . SLES Vm의 경우 매개 변수를 사용 하 여 명령을 실행 합니다 `--license-type` `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>혜택을 사용 하도록 설정 하는 CLI 예제
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>혜택을 사용 하지 않도록 설정 하는 CLI 예제
이 혜택을 사용 하지 않도록 설정 하려면 다음 값을 사용 합니다 `--license-type` `None` .

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> 확장 집합에는 Vm이 최신 확장 집합 모델을 사용 하 여 최신 상태로 전환 되는 방법을 결정 하는 ["업그레이드 정책"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 이 있습니다. 따라서 VMSS에 ' 자동 ' 업그레이드 정책이 있는 경우 VM 인스턴스가 업데이트 되 면 AHB 혜택이 자동으로 적용 됩니다. VMSS에 예약 된 업데이트를 기반으로 하는 ' 롤링 ' 업그레이드 정책이 있으면 AHB 적용 됩니다.
' 수동 ' 업그레이드 정책의 경우에는 각 기존 VM의 "수동 업그레이드"를 수행 해야 합니다.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>"수동 업그레이드" 정책에 대 한 가상 머신 확장 집합 인스턴스를 업그레이드 하는 CLI 예제 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>가상 머신 확장 집합 만든 시간에 Azure 하이브리드 혜택 적용 
기존 종 량 제 가상 머신 확장 집합에 Azure 하이브리드 혜택를 적용 하는 것 외에도 가상 머신 확장 집합을 만들 때이를 호출할 수 있습니다. 이렇게 하면 다음과 같은 이점이 증가량 됩니다.
- 동일한 이미지와 프로세스를 사용 하 여 PAYG 및 BYOS 가상 머신 확장 집합 인스턴스를 둘 다 프로 비전 할 수 있습니다.
- 이후 라이선스 모드를 변경할 수 있으며 BYOS 전용 이미지에서는 사용할 수 없습니다.
- 가상 머신 확장 집합 인스턴스는 기본적으로 RHUI (Red Hat Update Infrastructure)에 연결 되어 최신 상태를 유지 하 고 안전 하 게 유지 됩니다. 언제 든 지 배포 후 업데이트 된 메커니즘을 변경할 수 있습니다.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>AHB 혜택을 사용 하 여 가상 머신 확장 집합을 만드는 CLI 예제
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>다음 단계
* [을 사용 하 여 Vm을 만들고 업데이트 하 고 Azure 하이브리드 혜택에 대 한 라이선스 유형 (RHEL_BYOS, SLES_BYOS)을 추가 하는 방법에 대해 알아봅니다 Azure CLI](/cli/azure/vmss)

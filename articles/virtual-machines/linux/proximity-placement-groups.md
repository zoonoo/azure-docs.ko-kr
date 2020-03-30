---
title: Linux VM에 근접 배치 그룹 사용
description: Azure의 Linux 가상 컴퓨터에 대한 근접 배치 그룹을 만들고 사용하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73171043"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Azure CLI를 사용하여 근접 배치 그룹에 VM 배포

VM을 가능한 한 가깝게 설정하여 가능한 가장 낮은 대기 시간을 달성하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까이 있는지 확인하는 데 사용되는 논리적 그룹입니다. 근접 배치 그룹은 대기 시간이 짧은 워크로드에 유용합니다.


## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기
을 사용하여 [`az ppg create`](/cli/azure/ppg#az-ppg-create)근접 배치 그룹을 만듭니다. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 목록

[az ppg 목록을](/cli/azure/ppg#az-ppg-list)사용하여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>VM 만들기

새 az vm을 사용하여 근접 배치 그룹 내에서 [VM을](/cli/azure/vm#az-vm-create)만듭니다.

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

[az ppg 쇼를](/cli/azure/ppg#az-ppg-show)사용하여 근접 배치 그룹에서 VM을 볼 수 있습니다.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>가용성 집합
근접 배치 그룹에서 가용성 집합을 만들 수도 있습니다. az `--ppg` [vm 가용성 집합 만들기와](/cli/azure/vm/availability-set#az-vm-availability-set-create) 동일한 매개 변수를 사용하여 가용성 집합을 만들고 가용성 집합의 모든 VM도 동일한 근접 배치 그룹에서 만들어집니다.

## <a name="scale-sets"></a>확장 집합

근접 배치 그룹에서 배율 세트를 만들 수도 있습니다. [az vmss가 만드는](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) 것과 동일한 `--ppg` 매개변수를 사용하여 배율 세트를 만들고 모든 인스턴스가 동일한 근접 배치 그룹에서 생성됩니다.

## <a name="next-steps"></a>다음 단계

근접 배치 그룹에 대한 [Azure CLI](/cli/azure/ppg) 명령에 대해 자세히 알아보세요.

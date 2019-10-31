---
title: Linux Vm에 근접 배치 그룹 사용
description: Azure에서 Linux 가상 머신에 대 한 근접 배치 그룹을 만들고 사용 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171043"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Azure CLI를 사용 하 여 근접 배치 그룹에 Vm 배포

가능한 한 가까운 시간 내에 Vm을 가져오기 위해 가장 낮은 대기 시간을 달성 하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포 해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까운 위치에 있는지 확인 하는 데 사용 되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구 하는 작업에 유용 합니다.


## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기
[`az ppg create`](/cli/azure/ppg#az-ppg-create)를 사용 하 여 근접 배치 그룹을 만듭니다. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 나열

[Az ppg list](/cli/azure/ppg#az-ppg-list)를 사용 하 여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>VM 만들기

[새 az VM](/cli/azure/vm#az-vm-create)을 사용 하 여 근접 배치 그룹 내에 vm을 만듭니다.

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

[Az ppg show](/cli/azure/ppg#az-ppg-show)를 사용 하 여 근접 배치 그룹에서 VM을 확인할 수 있습니다.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>가용성 집합
또한 근접 배치 그룹에 가용성 집합을 만들 수 있습니다. [Az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) 와 동일한 `--ppg` 매개 변수를 사용 하 여 가용성 집합을 만들고 가용성 집합의 모든 vm이 동일한 근접 배치 그룹에도 만들어집니다.

## <a name="scale-sets"></a>확장 집합

또한 근접 배치 그룹에 확장 집합을 만들 수 있습니다. [Az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) 와 동일한 `--ppg` 매개 변수를 사용 하 여 확장 집합을 만들고 모든 인스턴스는 동일한 근접 배치 그룹에 생성 됩니다.

## <a name="next-steps"></a>다음 단계

근접 배치 그룹에 대 한 [Azure CLI](/cli/azure/ppg) 명령에 대해 자세히 알아보세요.

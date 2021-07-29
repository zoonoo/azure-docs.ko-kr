---
title: Azure CLI를 사용하여 Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어
description: 유지 관리 제어 및 Azure CLI를 사용하여 Azure 가상 머신 확장 집합에 자동 OS 이미지 업그레이드가 출시되는 시기를 제어하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e00341b38cca73ccd351b4e16ce71317a8d0c751
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073169"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-cli"></a>Azure CLI를 사용하여 Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어

유지 관리 제어를 통해 가상 머신 확장 집합에 자동 게스트 OS 이미지 업그레이드를 적용하는 시기를 결정할 수 있습니다. 이 항목에서는 Azure CLI의 유지 관리 제어 옵션에 관해 설명합니다. 유지 관리 제어를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 머신 확장 집합에 대한 유지 관리 제어](virtual-machine-scale-sets-maintenance-control.md)를 참조하세요.


## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

`az maintenance configuration create`를 사용하여 유지 관리 구성을 만듭니다. 이 예제에서는 OS 이미지로 범위가 지정된 *myConfig* 라는 유지 관리 구성을 만듭니다. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage\
   --location eastus
```

나중에 사용할 수 있도록 출력에서 구성 ID를 복사합니다.

`--maintenance-scope osimage`를 사용하면 유지 관리 구성이 게스트 OS 업데이트를 제어하는 데 사용됩니다.

다른 위치에서 동일한 이름의 구성을 만들려고 하면 오류가 발생합니다. 구성 이름은 리소스 그룹에서 고유해야 합니다.

`az maintenance configuration list`를 사용하여 사용 가능한 유지 관리 구성을 쿼리할 수 있습니다.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-a-scheduled-window"></a>예약된 기간을 사용하여 유지 관리 구성 만들기
Azure가 리소스에 대한 업데이트를 적용하는 예약된 기간을 선언할 수도 있습니다. 이 예제에서는 매월 네 번째 월요일에 예약된 기간 5시간 동안 *myConfig* 라는 유지 관리 구성을 만듭니다. 예약된 기간을 만든 후에는 더 이상 수동으로 업데이트를 적용하지 않아도 됩니다.

> [!IMPORTANT]
> 유지 관리 **기간** 은 ‘5시간’ 이상이어야 합니다. 유지 관리 **되풀이** 는 ‘일’로 설정해야 합니다.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Day" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

## <a name="assign-the-configuration"></a>구성 할당

`az maintenance assignment create`를 사용하여 가상 머신 확장 집합에 구성을 할당합니다.


## <a name="enable-automatic-os-upgrade"></a>자동 OS 업그레이드 사용

유지 관리 제어를 사용하려는 각 가상 머신 확장 집합에 대해 자동 OS 업그레이드를 사용하도록 설정할 수 있습니다. 가상 머신 확장 집합에서 자동 OS 업그레이드를 사용하도록 설정하는 방법에 관한 자세한 내용은 [Azure 가상 머신 확장 집합 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 실행되는 가상 머신의 유지 관리 및 업데이트에 대해 알아봅니다.](maintenance-and-updates.md)

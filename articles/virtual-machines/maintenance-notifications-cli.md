---
title: CLI를 사용 하 여 유지 관리 알림 가져오기
description: Azure CLI를 사용 하 여 Azure에서 실행 되는 가상 머신에 대 한 유지 관리 알림을 확인 하 고 셀프 서비스 유지 관리를 시작 합니다.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 289733c4cee23a37c26df0b613a470925756f0eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84674842"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Azure CLI를 사용 하 여 계획 된 유지 관리 알림 처리

**이 문서는 Linux 및 Windows를 실행 하는 가상 컴퓨터에 적용 됩니다.**

CLI를 사용 하 여 Vm이 [유지 관리](maintenance-notifications.md)되도록 예약 된 시기를 확인할 수 있습니다. 계획 된 유지 관리 정보는 [az vm get instance-view](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)에서 사용할 수 있습니다.
 
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>유지 관리 시작

이 true로 설정 된 경우 다음 호출은 VM에 대 한 유지 관리를 시작 합니다 `IsCustomerInitiatedMaintenanceAllowed` .

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>클래식 배포

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

클래식 배포 모델을 사용하여 배포된 레거시 VM이 아직 있는 경우 Azure 클래식 CLI를 사용하여 VM을 쿼리하고 유지 관리를 시작할 수 있습니다.

다음을 입력하여 클래식 VM에서 작동하도록 올바른 모드에 있는지 확인합니다.

```
azure config mode asm
```

*myVM*이라는 VM에 대한 유지 관리 상태를 가져오려면 다음을 입력합니다.

```
azure vm show myVM 
``` 

*myService* 서비스 및 *myDeployment* 배포에서 *myVM*이라는 클래식 VM에 대한 유지 관리를 시작하려면 다음을 입력합니다.

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>다음 단계

[Azure PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용 하 여 계획 된 유지 관리를 처리할 수도 있습니다.

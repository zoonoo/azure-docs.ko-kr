---
title: CLI를 사용하여 유지 관리 알림 받기
description: Azure에서 실행 중인 가상 시스템에 대한 유지 관리 알림을 보고 Azure CLI를 사용하여 셀프 서비스 유지 관리를 시작합니다.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920895"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Azure CLI를 사용하여 계획된 유지 관리 알림 처리

**이 문서는 Linux와 Windows를 모두 실행하는 가상 컴퓨터에 적용됩니다.**

CLI를 사용하여 VM이 [유지 관리를](maintenance-notifications.md)위해 예약된 시기를 확인할 수 있습니다. 계획된 유지 관리 정보는 [az vm get-instance-view에서](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)확인할 수 있습니다.
 
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>유지 보수 시작

true로 설정된 경우 `IsCustomerInitiatedMaintenanceAllowed` 다음 호출이 VM에서 유지 관리를 시작합니다.

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

[Azure PowerShell](maintenance-notifications-powershell.md) 또는 [포털을](maintenance-notifications-portal.md)사용하여 계획된 유지 관리를 처리할 수도 있습니다.

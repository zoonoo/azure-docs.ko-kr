---
title: 유지 관리 제어
description: 유지 관리 제어를 사용하여 Azure VM에 유지 관리가 적용되는 시기를 제어하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250180"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>미리 보기: 유지 관리 제어 및 Azure CLI를 통해 업데이트를 제어합니다.

유지 관리 제어를 사용하여 재부팅이 필요하지 않은 플랫폼 업데이트를 관리합니다. Azure는 안정성, 성능, 보안을 개선하거나 새로운 기능을 시작하기 위해 인프라를 자주 업데이트합니다. 대부분의 업데이트는 사용자에게 투명합니다. 게임, 미디어 스트리밍 및 금융 거래와 같은 일부 민감한 워크로드는 유지 관리를 위해 VM 고정 또는 연결을 끊는 몇 초라도 견딜 수 없습니다. 유지 보수 제어를 통해 플랫폼 업데이트를 기다렸다가 35일 롤링 기간 내에 적용할 수 있습니다. 

유지 관리 제어를 사용하면 격리된 VM 및 Azure 전용 호스트에 업데이트를 적용할 시기를 결정할 수 있습니다.

유지 보수 제어를 통해 다음을 수행할 수 있습니다.
- 하나의 업데이트 패키지로 일괄 업데이트합니다.
- 업데이트를 적용하려면 최대 35일까지 기다립니다. 
- Azure 함수를 사용하여 유지 관리 기간의 플랫폼 업데이트를 자동화합니다.
- 유지 관리 구성은 구독 및 리소스 그룹에서 작동합니다. 

> [!IMPORTANT]
> 유지 관리 제어는 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="limitations"></a>제한 사항

- VM은 전용 [호스트에](./linux/dedicated-hosts.md)있거나 [격리된 VM 크기를](./linux/isolation.md)사용하여 만들어야 합니다.
- 35일이 지나면 업데이트가 자동으로 적용됩니다.
- 사용자는 **리소스 기고자** 액세스 권한이 있어야 합니다.


## <a name="install-the-maintenance-extension"></a>유지 보수 확장 설치

[Azure CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli) 로컬로 설치하도록 선택한 경우 버전 2.0.76 이상이 필요합니다.

`maintenance` 미리 보기 CLI 확장을 로컬 또는 클라우드 셸에 설치합니다. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>유지 관리 구성 만들기

유지 `az maintenance configuration create` 관리 구성을 만드는 데 사용합니다. 이 예제는 호스트에 대한 범위로 지정된 *myConfig라는* 유지 관리 구성을 만듭니다. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

나중에 사용할 출력에서 구성 ID를 복사합니다.

호스트에 `--maintenanceScope host` 대한 업데이트를 제어하는 데 유지 관리 구성이 사용되도록 합니다.

이름이 같지만 다른 위치에 있는 구성을 만들려고 하면 오류가 발생합니다. 구성 이름은 구독에 고유해야 합니다.

을 사용하여 `az maintenance configuration list`사용 가능한 유지 관리 구성을 쿼리할 수 있습니다.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>구성 할당

격리된 VM 또는 Azure 전용 호스트에 구성을 할당하는 데 사용합니다. `az maintenance assignment create`

### <a name="isolated-vm"></a>격리된 VM

구성의 ID를 사용하여 VM에 구성을 적용합니다. 에 `--resource-type virtualMachines` 대한 `--resource-name`VM 및 리소스 그룹의 이름을 에 대한 VM `--resource-group`및 에 대한 VM의 위치를 지정하고 `--location`제공합니다. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 구성을 적용하려면 호스트 `--resource-type hosts` `--resource-parent-name` 그룹의 이름과 을 포함해야 `--resource-parent-type hostGroups`합니다. 

매개 `--resource-id` 변수는 호스트의 ID입니다. [az vm 호스트 get-instance-view를](/cli/azure/vm/host#az-vm-host-get-instance-view) 사용하여 전용 호스트의 ID를 얻을 수 있습니다.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>구성 확인

구성이 올바르게 적용되었는지 확인하거나 을 사용하여 `az maintenance assignment list`현재 적용된 구성을 확인할 수 있습니다.

### <a name="isolated-vm"></a>격리된 VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>전용 호스트 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

보류 `az maintenance update list` 중인 업데이트가 있는지 확인하는 데 사용합니다. 업데이트 --구독VM을 포함 하는 구독에 대 한 ID로 합니다.

업데이트가 없는 경우 명령은 텍스트를 포함하는 오류 메시지를 `Resource not found...StatusCode: 404`반환합니다.

업데이트가 있는 경우 보류 중인 업데이트가 여러 개 있더라도 하나만 반환됩니다. 이 업데이트의 데이터는 개체에서 반환됩니다.

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 대한 보류 중인 업데이트를 확인합니다. 이 예제에서는 출력이 가독성을 위해 테이블로 서식이 지정됩니다.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대한 보류 중인 업데이트를 확인합니다. 이 예제에서는 출력이 가독성을 위해 테이블로 서식이 지정됩니다. 리소스의 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>업데이트 적용

보류 `az maintenance apply update` 중인 업데이트를 적용하는 데 사용합니다. 성공하면 이 명령은 업데이트 세부 정보가 포함된 JSON을 반환합니다.

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 업데이트를 적용하도록 요청을 만듭니다.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 업데이트를 적용합니다.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>업데이트 적용 상태 확인 

을 사용하여 `az maintenance applyupdate get`업데이트 진행 률을 확인할 수 있습니다. 

업데이트 이름으로 `default` 사용하여 마지막 업데이트의 결과를 보거나 을 `myUpdateName` 실행했을 `az maintenance applyupdate create`때 반환된 업데이트의 이름으로 바꿀 수 있습니다.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime은 자체 유지 관리 창이 사용되지 않은 경우 귀하 또는 플랫폼에서 시작된 업데이트가 완료된 시간입니다. 유지 관리 제어를 통해 적용된 업데이트가 없는 경우 기본값이 표시됩니다.

### <a name="isolated-vm"></a>격리된 VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>전용 호스트

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>유지 관리 구성 삭제

유지 `az maintenance configuration delete` 관리 구성을 삭제하는 데 사용합니다. 구성을 삭제면 관련 리소스에서 유지 관리 제어가 제거됩니다.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>다음 단계
자세한 내용은 [유지 관리 및 업데이트를](maintenance-and-updates.md)참조하십시오.

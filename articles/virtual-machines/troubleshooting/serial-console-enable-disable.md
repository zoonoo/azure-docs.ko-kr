---
title: Azure 직렬 콘솔 사용 및 비활성화 | 마이크로 소프트 문서
description: Azure 직렬 콘솔 서비스를 활성화 및 사용하지 않도록 설정하는 방법
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451297"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Azure 직렬 콘솔 사용 및 비활성화

다른 리소스와 마찬가지로 Azure 직렬 콘솔을 활성화하고 사용하지 않도록 설정할 수 있습니다. 직렬 콘솔은 전역 Azure의 모든 구독에 대해 기본적으로 활성화됩니다. 현재 직렬 콘솔을 사용하지 않도록 설정하면 전체 구독에 대한 서비스가 비활성화됩니다. 구독에 대해 직렬 콘솔을 사용하지 않도록 설정하거나 다시 사용하도록 설정하려면 구독에서 기여자 수준 이상의 액세스 권한이 필요합니다.

또한 부팅 진단을 사용하지 않도록 설정하여 개별 VM 또는 가상 시스템 크기 집합 인스턴스에 대해 직렬 콘솔을 비활성화할 수도 있습니다. VM/가상 시스템 규모 집합과 부팅 진단 저장소 계정 모두에서 기여자 수준 액세스 또는 이상이 필요합니다.

## <a name="vm-level-disable"></a>VM 수준에서 비활성화
부팅 진단 설정을 사용하지 않도록 설정하여 설정된 특정 VM 또는 가상 시스템 규모에 대해 직렬 콘솔을 사용하지 않도록 설정할 수 있습니다. Azure 포털에서 부팅 진단을 해제하여 VM 또는 가상 시스템 규모 집합에 대한 직렬 콘솔을 사용하지 않도록 설정합니다. 가상 시스템 규모 집합에서 직렬 콘솔을 사용하는 경우 가상 컴퓨터 스케일 세트 인스턴스를 최신 모델로 업그레이드해야 합니다.


## <a name="subscription-level-enabledisable"></a>구독 수준 사용/사용 안 함

> [!NOTE]
> 이 명령을 실행하기 전에 올바른 클라우드(Azure 퍼블릭 클라우드, Azure 미국 정부 클라우드)에 있는지 확인합니다. 을 통해 `az cloud list` 클라우드를 확인하고 `az cloud set -n <Name of cloud>`설정할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 다음 명령을 사용하여 전체 구독에 대해 직렬 콘솔을 사용하지 않도록 설정하고 다시 활성화할 수 있습니다("사용" 단추를 사용하여 명령을 실행할 수 있는 Azure Cloud Shell의 인스턴스를 시작할 수 있음).

구독에 대해 직렬 콘솔을 사용하지 않도록 설정하려면 다음 명령을 사용합니다.
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

구독에 대해 직렬 콘솔을 사용하려면 다음 명령을 사용합니다.
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

구독에 대한 직렬 콘솔의 현재 사용/사용 안 함 상태를 얻으려면 다음 명령을 사용합니다.
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 직렬 콘솔을 활성화하고 비활성화할 수도 있습니다.

구독에 대해 직렬 콘솔을 사용하지 않도록 설정하려면 다음 명령을 사용합니다.
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

구독에 대해 직렬 콘솔을 사용하려면 다음 명령을 사용합니다.
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>다음 단계
* [Linux VM용 Azure 직렬 콘솔에](./serial-console-linux.md) 대해 자세히 알아보기
* [Windows VM용 Azure 직렬 콘솔에](./serial-console-windows.md) 대해 자세히 알아보기
* Azure [직렬 콘솔 내전원 관리 옵션에](./serial-console-power-options.md) 대해 알아보기
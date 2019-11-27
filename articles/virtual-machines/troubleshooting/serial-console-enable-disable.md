---
title: Azure 직렬 콘솔 사용 및 사용 안 함 Microsoft Docs
description: Azure 직렬 콘솔 서비스를 사용 하거나 사용 하지 않도록 설정 하는 방법
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
ms.openlocfilehash: fa400d875a8f39d54d10820c603e12e97f0cd854
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452224"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Azure 직렬 콘솔 사용 및 사용 안 함

다른 리소스와 마찬가지로, Azure 직렬 콘솔을 사용 하도록 설정 하 고 사용 하지 않도록 설정할 수 있습니다. 직렬 콘솔은 글로벌 Azure의 모든 구독에 대해 기본적으로 사용 하도록 설정 됩니다. 현재 직렬 콘솔을 사용 하지 않도록 설정 하면 전체 구독에 대 한 서비스를 사용할 수 없게 됩니다. 구독에 대 한 직렬 콘솔을 사용 하지 않도록 설정 하거나 다시 사용 하도록 설정 하려면 구독에 대 한 참가자 수준 액세스 권한이 필요 합니다.

부팅 진단을 사용 하지 않도록 설정 하 여 개별 VM 또는 가상 머신 확장 집합 인스턴스에 대해 직렬 콘솔을 사용 하지 않도록 설정할 수도 있습니다. V m/가상 머신 확장 집합과 부트 진단 저장소 계정 모두에서 참가자 수준 액세스 이상이 필요 합니다.

## <a name="vm-level-disable"></a>VM 수준에서 비활성화
부팅 진단 설정을 사용 하지 않도록 설정 하 여 특정 VM 또는 가상 머신 확장 집합에 대해 직렬 콘솔을 사용 하지 않도록 설정할 수 있습니다. VM 또는 가상 머신 확장 집합에 대 한 직렬 콘솔을 사용 하지 않도록 설정 하려면 Azure Portal에서 부트 진단을 끕니다. 가상 머신 확장 집합에서 직렬 콘솔을 사용 하는 경우 가상 머신 확장 집합 인스턴스를 최신 모델로 업그레이드 해야 합니다.


## <a name="subscription-level-enabledisable"></a>구독 수준 사용/사용 안 함

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 다음 명령을 사용 하 여 전체 구독을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정할 수 있습니다 .이 단추를 사용 하 여 명령을 실행할 수 있는 Azure Cloud Shell 인스턴스를 시작할 수 있습니다. 직렬 콘솔

구독에 대 한 직렬 콘솔을 사용 하지 않도록 설정 하려면 다음 명령을 사용 합니다.
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

구독에 대 한 직렬 콘솔을 사용 하도록 설정 하려면 다음 명령을 사용 합니다.
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

구독에 대 한 직렬 콘솔의 현재 사용/사용 안 함 상태를 가져오려면 다음 명령을 사용 합니다.
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

> [!NOTE]
> 이 명령을 실행 하기 전에 올바른 클라우드 (Azure 공용 클라우드, Azure 미국 정부 클라우드)에 있는지 확인 하세요. `az cloud list`를 확인 하 고 `az cloud set -n <Name of cloud>`를 사용 하 여 클라우드를 설정할 수 있습니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 직렬 콘솔를 사용 하거나 사용 하지 않도록 설정할 수도 있습니다.

구독에 대 한 직렬 콘솔을 사용 하지 않도록 설정 하려면 다음 명령을 사용 합니다.
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

구독에 대 한 직렬 콘솔을 사용 하도록 설정 하려면 다음 명령을 사용 합니다.
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>다음 단계
* [Linux vm 용 Azure 직렬 콘솔](./serial-console-linux.md) 에 대 한 자세한 정보
* [Windows vm 용 Azure 직렬 콘솔](./serial-console-windows.md) 에 대 한 자세한 정보
* [Azure 직렬 콘솔 내에서 전원 관리 옵션](./serial-console-power-options.md) 에 대 한 자세한 정보
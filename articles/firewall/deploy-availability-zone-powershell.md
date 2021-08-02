---
title: PowerShell을 사용하여 가용성 영역을 통해 Azure Firewall 배포
description: 이 문서에서는 Azure PowerShell을 사용하여 가용성 영역을 통해 Azure Firewall을 배포하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0179c8aebc9410d75b9c0702aeaf37451c89cc3d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693705"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure PowerShell을 사용하여 가용성 영역을 통해 Azure Firewall 배표

Azure Firewall은 가용성을 높이기 위해 여러 가용 영역에 걸쳐 배포하는 동안 구성할 수 있습니다.

이 기능을 사용할 경우 다음 시나리오가 가능합니다.

- 가용성을 작동 시간 99.99%로 늘릴 수 있습니다. 자세한 내용은 Azure Firewall [SLA(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)를 참조하세요. 99.99% 작동 시간 SLA는 둘 이상의 가용성 영역을 선택하는 경우 제공됩니다.
- 서비스 표준 99.95% SLA를 사용하여 근접성을 이유로 특정 영역에 Azure Firewall을 연결할 수도 있습니다.

Azure Firewall 가용성 영역 대한 자세한 내용은 [Azure Firewall이란?](overview.md)을 참조하세요.

다음 Azure PowerShell 예제에서는 가용성 영역을 통해 Azure Firewall을 배포하는 방법을 보여 줍니다.

## <a name="create-a-firewall-with-availability-zones"></a>가용성 영역을 통해 방화벽 만들기

이 예제에서는 영역 1, 2, 3에 방화벽을 만듭니다.

표준 공용 IP 주소가 만들어지면 특정 영역이 지정되지 않습니다. 이렇게 하면 기본적으로 영역 중복 IP 주소가 만들어집니다. 표준 공용 IP 주소는 모든 영역 또는 단일 영역에서 구성할 수 있습니다.

영역 1에 방화벽을 두고 영역 2에 IP 주소를 두는 것이 불가능하므로 이를 알고 있어야 합니다. 그러나 영역 1에 방화벽을 두고 모든 영역에 IP 주소를 두거나, 근접성을 위해 방화벽과 IP 주소를 동일한 단일 영역에 두는 것은 가능합니다.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Firewall 로그 모니터링](./firewall-diagnostics.md)

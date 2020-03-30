---
title: PowerShell을 사용하여 가용성 영역을 사용하여 Azure 방화벽 배포
description: 이 문서에서는 Azure PowerShell을 사용하여 가용성 영역이 있는 Azure 방화벽을 배포하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195918"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure PowerShell을 사용하여 가용성 영역을 통해 Azure Firewall 배표

Azure Firewall은 가용성을 높이기 위해 여러 가용 영역에 걸쳐 배포하는 동안 구성할 수 있습니다.

이 기능을 사용하면 다음과 같은 시나리오를 사용할 수 있습니다.

- 가동 시간을 99.99%로 늘릴 수 있습니다. 자세한 내용은 Azure Firewall [SLA(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)를 참조하세요. 99.99% 작동 시간 SLA는 둘 이상의 가용성 영역을 선택하는 경우 제공됩니다.
- 서비스 표준 99.95% SLA를 사용하여 근접성을 이유로 특정 영역에 Azure Firewall을 연결할 수도 있습니다.

Azure 방화벽 가용성 영역에 대한 자세한 내용은 [Azure 방화벽이란 무엇입니까?](overview.md)

다음 Azure PowerShell 예제에서는 가용성 영역이 있는 Azure 방화벽을 배포하는 방법을 보여 주며 있습니다.

## <a name="create-a-firewall-with-availability-zones"></a>가용성 영역이 있는 방화벽 만들기

이 예제에서는 영역 1, 2 및 3에 방화벽을 만듭니다.

표준 공용 IP 주소를 만들면 특정 영역이 지정되지 않습니다. 이렇게 하면 기본적으로 영역 중복 IP 주소가 만들어집니다. 표준 공용 IP 주소는 모든 영역 또는 단일 영역에서 구성할 수 있습니다.

영역 1에 방화벽이 있고 영역 2에 IP 주소가 없기 때문에 알고 있어야 합니다. 그러나 모든 영역에서 영역 1및 IP 주소에 방화벽을 갖거나 근접성을 위해 동일한 단일 영역에 방화벽과 IP 주소를 가질 수 있습니다.

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
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)

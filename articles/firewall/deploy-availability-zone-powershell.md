---
title: Azure PowerShell을 사용 하 여 가용성 영역을 사용 하 여 Azure 방화벽 배포
description: 이 문서에서는 Azure PowerShell을 사용 하 여 가용성 영역을 사용 하 여 Azure 방화벽을 배포 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276913"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 가용성 영역을 사용 하 여 Azure 방화벽을 배포 합니다.

> [!IMPORTANT]
> Azure 가용성 영역을 사용 하 여 방화벽이 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

가용성을 높이기 위해 여러 가용성 영역에 걸쳐 배포 하는 동안 azure 방화벽을 구성할 수 있습니다.

이 기능을 사용 하면 다음과 같은 경우:

- 가용성 99.99% 작동 시간을 늘릴 수 있습니다. 자세한 내용은 Azure 방화벽을 참조 하세요 [서비스 수준 계약 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)합니다. 99.99% 작동 시간 SLA는 둘 이상의 가용성 영역을 선택 하면 제공 됩니다.
- 연결할 수도 있습니다 Azure 방화벽만 근접 이유로 특정 영역에 서비스 표준 99.95 %SLA 사용 하 여 합니다.

Azure 방화벽 가용성 영역에 대 한 자세한 내용은 참조 하세요. [Azure 방화벽 이란?](overview.md)

다음 Azure PowerShell 예제에서는 가용성 영역을 사용 하 여 Azure 방화벽을 배포 하는 방법을 보여 줍니다.

## <a name="create-a-firewall-with-availability-zones"></a>가용성 영역을 사용 하 여 방화벽 만들기

이 예제에서는 영역 1, 2 및 3에서에서 방화벽을 만듭니다.

표준 공용 IP 주소를 만들면 특정 지정 된 영역이 없습니다. 이 기본적으로 영역 중복 IP 주소를 만듭니다. 모든 영역 또는 단일 영역 표준 공용 IP 주소를 구성할 수 있습니다.

영역 2에에서 영역 1에서에서 방화벽 및 IP 주소를 포함할 수 없으므로 것을 알고 있어야 합니다. 하지만 근접성을 위해 동일한 단일 영역의 영역 1에서에서 방화벽 및 모든 영역에서 IP 주소 또는 방화벽 및 IP 주소를 가질 수 있습니다.

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

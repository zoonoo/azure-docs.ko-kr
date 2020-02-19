---
title: Azure 방화벽 SNAT 개인 IP 주소 범위
description: 방화벽에서 해당 IP 주소에 대 한 트래픽을 SNAT 하지 않도록 IP 주소 개인 범위를 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444460"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 방화벽 SNAT 개인 IP 주소 범위

Azure 방화벽은 대상 IP 주소가 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)당 개인 ip 주소 범위에 있는 경우 SNAT 하지 않습니다. 

조직에서 프라이빗 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 그러나 공용 IP 주소 범위를 SNAT **하지 않도록** Azure 방화벽을 구성할 수 있습니다.

## <a name="configure-snat-private-ip-address-ranges"></a>SNAT 개인 IP 주소 범위 구성

Azure PowerShell를 사용 하 여 방화벽에서 SNAT 하지 않는 IP 주소 범위를 지정할 수 있습니다.

### <a name="new-firewall"></a>새 방화벽

새 방화벽의 경우 Azure PowerShell 명령은 다음과 같습니다.

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges는 Azure 방화벽의 현재 기본값으로 확장 되 고 다른 범위는 추가 됩니다.

자세한 내용은 [AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)를 참조 하세요.

### <a name="existing-firewall"></a>기존 방화벽

기존 방화벽을 구성 하려면 다음 Azure PowerShell 명령을 사용 합니다.

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>템플릿

`additionalProperties` 섹션에 다음을 추가할 수 있습니다.

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.
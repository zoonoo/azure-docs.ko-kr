---
title: Azure 방화벽 SNAT 개인 IP 주소 범위
description: 방화벽이 해당 IP 주소로 의 트래픽을 SNAT하지 않도록 IP 주소 개인 범위를 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064807"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 방화벽 SNAT 개인 IP 주소 범위

대상 IP 주소가 [IANA RFC 1918에](https://tools.ietf.org/html/rfc1918)따라 개인 IP 주소 범위에 있을 때 Azure 방화벽은 네트워크 규칙을 사용하여 SNAT를 지정하지 않습니다. 응용 프로그램 규칙은 항상 대상 IP 주소에 관계없이 [투명 프록시를](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) 사용하여 적용됩니다.

조직에서 개인 네트워크에 대한 공용 IP 주소 범위를 사용하는 경우 Azure 방화벽SNAT는 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 그러나 공용 IP 주소 **not** 범위를 SNAT하지 않도록 Azure 방화벽을 구성할 수 있습니다.

## <a name="configure-snat-private-ip-address-ranges"></a>SNAT 개인 IP 주소 범위 구성

Azure PowerShell을 사용하여 방화벽이 SNAT하지 않는 IP 주소 범위를 지정할 수 있습니다.

### <a name="new-firewall"></a>새 방화벽

새 방화벽의 경우 Azure PowerShell 명령은 다음과 같습니다.

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges는 Azure 방화벽의 현재 기본값으로 확장되고 다른 범위는 추가됩니다.

자세한 내용은 [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)을 참조하십시오.

### <a name="existing-firewall"></a>기존 방화벽

기존 방화벽을 구성하려면 다음 Azure PowerShell 명령을 사용합니다.

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>템플릿

섹션에 다음을 `additionalProperties` 추가할 수 있습니다.

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.
---
title: Azure 방화벽 SNAT 개인 IP 주소 범위
description: SNAT에 대 한 IP 주소 범위를 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508931"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 방화벽 SNAT 개인 IP 주소 범위

Azure 방화벽은 공용 IP 주소에 대 한 모든 아웃 바운드 트래픽에 대해 자동 SNAT를 제공 합니다. 기본적으로 Azure 방화벽은 대상 IP 주소가 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)당 개인 ip 주소 범위에 있는 경우 네트워크 규칙을 사용 하 여 SNAT 하지 않습니다. 응용 프로그램 규칙은 대상 IP 주소에 관계 없이 항상 [투명 프록시](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) 를 사용 하 여 적용 됩니다.

이 논리는 트래픽을 인터넷으로 직접 라우팅하는 경우에 효과적입니다. 그러나 [강제 터널링](forced-tunneling.md)을 사용 하도록 설정한 경우 인터넷 바인딩된 트래픽은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 연결 되어 온-프레미스 방화벽에서 원본을 숨깁니다.

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 그러나 공용 IP 주소 범위를 SNAT **하지 않도록** Azure 방화벽을 구성할 수 있습니다. 예를 들어 개별 IP 주소를 지정 하려면 다음과 같이 지정할 수 있습니다 `192.168.1.10` . IP 주소 범위를 지정 하려면 다음과 같이 지정할 수 있습니다 `192.168.1.0/24` .

- 대상 IP 주소에 관계 없이 Azure 방화벽을 SNAT **하지 않도록** 구성 하려면 개인 ip 주소 범위로 **0.0.0.0/0** 을 사용 합니다. 이 구성을 사용 하는 경우 Azure 방화벽은 트래픽을 인터넷으로 직접 라우팅할 수 없습니다. 

- 대상 주소에 관계 없이 **항상** SNAT를 사용 하도록 방화벽을 구성 하려면 전용 IP 주소 범위로 **255.255.255.255/32** 를 사용 합니다.

> [!IMPORTANT]
> 지정 하는 개인 주소 범위는 네트워크 규칙에만 적용 됩니다. 현재 응용 프로그램 규칙은 항상 SNAT입니다.

> [!IMPORTANT]
> 사용자 고유의 개인 IP 주소 범위를 지정 하 고 기본 IANA RFC 1918 주소 범위를 유지 하려면 사용자 지정 목록에 IANA RFC 1918 범위가 계속 포함 되어 있는지 확인 합니다. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT 개인 IP 주소 범위 구성-Azure PowerShell

Azure PowerShell를 사용 하 여 방화벽에 대 한 개인 IP 주소 범위를 지정할 수 있습니다.

### <a name="new-firewall"></a>새 방화벽

새 방화벽의 경우 Azure PowerShell cmdlet은 다음과 같습니다.

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> 을 사용 하 여 Azure 방화벽 `New-AzFirewall` 을 배포 하려면 기존 VNet 및 공용 IP 주소가 필요 합니다. 전체 배포 가이드는 [Azure PowerShell을 사용 하 여 Azure 방화벽 배포 및 구성](deploy-ps.md) 을 참조 하세요.

> [!NOTE]
> IANAPrivateRanges는 Azure 방화벽의 현재 기본값으로 확장 되 고 다른 범위는 추가 됩니다. Private 범위 사양에 IANAPrivateRanges 기본값을 유지 하려면 `PrivateRange` 다음 예에 표시 된 것 처럼 사양에 유지 되어야 합니다.

자세한 내용은 [AzFirewall](/powershell/module/az.network/new-azfirewall)를 참조 하세요.

### <a name="existing-firewall"></a>기존 방화벽

기존 방화벽을 구성 하려면 다음 Azure PowerShell cmdlet을 사용 합니다.

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT 개인 IP 주소 범위 구성-Azure CLI

Azure CLI를 사용 하 여 방화벽에 대 한 개인 IP 주소 범위를 지정할 수 있습니다.

### <a name="new-firewall"></a>새 방화벽

새 방화벽의 경우 Azure CLI 명령은 다음과 같습니다.

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Azure CLI 명령을 사용 하 여 Azure 방화벽을 배포 `az network firewall create` 하려면 추가 구성 단계를 수행 하 여 공용 ip 주소 및 ip 구성을 만들어야 합니다. 전체 배포 가이드는 [Azure CLI을 사용 하 여 Azure 방화벽 배포 및 구성](deploy-cli.md) 을 참조 하세요.

> [!NOTE]
> IANAPrivateRanges는 Azure 방화벽의 현재 기본값으로 확장 되 고 다른 범위는 추가 됩니다. Private 범위 사양에 IANAPrivateRanges 기본값을 유지 하려면 `PrivateRange` 다음 예에 표시 된 것 처럼 사양에 유지 되어야 합니다.

### <a name="existing-firewall"></a>기존 방화벽

기존 방화벽을 구성 하려면 Azure CLI 명령은 다음과 같습니다.

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT 개인 IP 주소 범위 구성-ARM 템플릿

ARM 템플릿 배포 중에 SNAT를 구성 하려면 다음을 속성에 추가 하면 됩니다 `additionalProperties` .

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT 개인 IP 주소 범위 구성-Azure Portal

Azure Portal를 사용 하 여 방화벽에 대 한 개인 IP 주소 범위를 지정할 수 있습니다.

1. 리소스 그룹을 선택한 다음 방화벽을 선택 합니다.
2. **개요** 페이지의 **개인 IP 범위** 에서 기본값 **IANA RFC 1918** 을 선택 합니다.

   **개인 IP 접두사 편집** 페이지가 열립니다.

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="개인 IP 접두사 편집":::

1. 기본적으로 **IANAPrivateRanges** 가 구성 되어 있습니다.
2. 사용자 환경에 대 한 개인 IP 주소 범위를 편집 하 고 **저장** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 강제 터널링](forced-tunneling.md)에 대해 알아봅니다.
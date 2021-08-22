---
title: Azure Firewall SNAT 개인 IP 주소 범위
description: SNAT에 대한 IP 주소 범위를 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6235e5ec34987c0a383ea776aabf0a00e345ce63
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693685"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT 개인 IP 주소 범위

Azure Firewall은 공용 IP 주소에 대한 모든 아웃바운드 트래픽에 자동 SNAT을 제공합니다. 기본적으로 대상 IP 주소가 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)에 따라 개인 IP 주소 범위 또는 [IANA RFC 6598](https://tools.ietf.org/html/rfc6598)에 따라 공유 주소 공간에 있는 경우, Azure Firewall은 네트워크 규칙이 있는 SNAT가 아닙니다. 애플리케이션 규칙은 대상 IP 주소에 관계없이 항상 [투명 프록시](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)를 사용하여 적용됩니다.

이 논리는 트래픽을 인터넷으로 직접 라우팅하는 경우에 효과적입니다. 하지만 [강제 터널링](forced-tunneling.md)을 사용하도록 설정한 경우 인터넷 바인딩된 트래픽이 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 SNAT이 되어 온-프레미스 방화벽에서 원본을 숨깁니다.

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 공용 IP 주소 범위를 SNAT하지 **않도록** Azure Firewall을 구성할 수 있습니다. 예를 들어, 개별 IP 주소를 지정하려면 `192.168.1.10`과 같이 지정할 수 있습니다. IP 주소 범위를 지정하려면 `192.168.1.0/24`와 같이 지정할 수 있습니다.

- 대상 IP 주소에 관계없이 Azure Firewall을 **절대** SNAT하지 않도록 구성하려면 **0.0.0.0/0** 을 개인 IP 주소 범위로 사용합니다. 이 구성을 사용하면 Azure Firewall이 트래픽을 인터넷으로 직접 라우팅할 수 없습니다. 

- 대상 주소에 관계없이 **항상** SNAT로 방화벽을 구성하려면 **255.255.255.255.255/32** 를 개인 IP 주소 범위로 사용합니다.

> [!IMPORTANT]
> 지정하는 개인 주소 범위는 네트워크 규칙에만 적용됩니다. 현재 애플리케이션 규칙은 항상 SNAT입니다.

> [!IMPORTANT]
> 사용자 고유의 개인 IP 주소 범위를 지정하고 기본 IANA RFC 1918 주소 범위를 유지하려면 사용자 지정 목록에 여전히 IANA RFC 1918 범위가 포함되어 있어야 합니다. 

다음 메서드를 사용하여 SNAT 개인 IP 주소를 구성할 수 있습니다. 구성에 적합한 메서드를 사용하여 SNAT 프라이빗 주소를 구성해야 합니다. 방화벽 정책과 연결된 방화벽은 정책에서 범위를 지정해야 하며 `AdditionalProperties`를 사용하지 않습니다.


|메서드            |클래식 규칙 사용  |방화벽 정책 사용  |
|---------|---------|---------|
|Azure portal     | [지원](#classic-rules-3)| [지원](#firewall-policy-1)|
|Azure PowerShell     |[`PrivateRange` 구성](#classic-rules)|현재 지원되지 않음|
|Azure CLI|[`--private-ranges` 구성](#classic-rules-1)|현재 지원되지 않음|
|ARM 템플릿     |[방화벽 속성에서 `AdditionalProperties` 구성](#classic-rules-2)|[방화벽 정책에서 `snat/privateRanges` 구성](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT 개인 IP 주소 범위 구성 - Azure PowerShell
### <a name="classic-rules"></a>클래식 규칙

Azure PowerShell을 사용하여 방화벽에 대한 개인 IP 주소 범위를 지정할 수 있습니다.

> [!NOTE]
> 방화벽 정책과 연결된 방화벽의 경우 방화벽 `PrivateRange` 속성을 무시합니다. [SNAT 개인 IP 주소 범위 - ARM 템플릿 구성](#firewall-policy)에서 설명한 대로 `firewallPolicies`에서 `SNAT` 속성을 사용해야 합니다.

#### <a name="new-firewall"></a>새 방화벽

클래식 규칙을 사용하는 새 방화벽의 경우 Azure PowerShell cmdlet은 다음과 같습니다.

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
> `New-AzFirewall`을 사용하는 Azure Firewall을 배포하려면 기존 VNet 및 공용 IP 주소가 필요합니다. 전체 배포 가이드는 [Azure PowerShell을 사용하여 Azure Firewall 배포 및 구성](deploy-ps.md)을 참조하세요.

> [!NOTE]
> IANAPrivateRanges는 Azure Firewall의 현재 기본값으로 확장되고 다른 범위는 Azure Firewall에 추가됩니다. IANAPrivateRanges를 개인 범위 사양에서 기본값으로 유지하려면 다음 예와 같이 `PrivateRange` 사양에 남아 있어야 합니다.

자세한 내용은 [New-AzFirewall](/powershell/module/az.network/new-azfirewall)을 참조하세요.

#### <a name="existing-firewall"></a>기존 방화벽

기존 방화벽을 구성하려면 다음 Azure PowerShell cmdlet을 사용합니다.

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT 개인 IP 주소 범위 구성 - Azure CLI
### <a name="classic-rules"></a>클래식 규칙

Azure CLI를 사용하여 방화벽에 대한 개인 IP 주소 범위를 지정할 수 있습니다. 

#### <a name="new-firewall"></a>새 방화벽

클래식 규칙을 사용하는 새 방화벽의 경우 Azure CLI 명령은 다음과 같습니다.

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Azure CLI 명령 `az network firewall create`를 사용하여 Azure Firewall을 배포하려면 공용 IP 주소 및 IP 구성을 생성하려면 추가 구성 단계가 필요합니다. 전체 배포 가이드는 Azure CLI를 사용하여 Azure Firewall 배포 및 구성을 참조합니다. 전체 배포 가이드는 [Azure CLI를 사용하여 Azure Firewall 배포 및 구성](deploy-cli.md)을 참조하세요.

> [!NOTE]
> IANAPrivateRanges는 Azure Firewall의 현재 기본값으로 확장되고 다른 범위는 Azure Firewall에 추가됩니다. IANAPrivateRanges를 개인 범위 사양에서 기본값으로 유지하려면 다음 예와 같이 `private-ranges` 사양에 남아 있어야 합니다.

#### <a name="existing-firewall"></a>기존 방화벽

클래식 규칙을 사용하는 기존 방화벽을 구성하기 위한 Azure CLI 명령은 다음과 같습니다.

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT 개인 IP 주소 범위 구성 - ARM 템플릿
### <a name="classic-rules"></a>클래식 규칙

ARM 템플릿 배포 중에 SNAT를 구성하려면 `additionalProperties` 속성에 다음을 추가할 수 있습니다.

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>방화벽 정책

방화벽 정책과 연결된 Azure Firewall은 2020-11-01 API 버전 이후 SNAT 프라이빗 범위를 지원하고 있습니다. 현재는 템플릿을 사용하여 방화벽 정책에서 SNAT 프라이빗 범위를 업데이트할 수 있습니다. 다음 샘플에서는 **항상** SNAT 네트워크 트래픽을 수행하도록 방화벽을 구성합니다.

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT 개인 IP 주소 범위 구성 - Azure Portal
### <a name="classic-rules"></a>클래식 규칙

Azure Portal을 사용하여 방화벽에 대한 개인 IP 주소 범위를 지정할 수 있습니다.

1. 리소스 그룹을 선택한 다음 방화벽을 선택합니다.
2. **개요** 페이지의 **개인 IP 범위** 에서 기본값 **IANA RFC 1918** 을 선택합니다.

   다음과 같이 **개인 IP 접두사 편집** 페이지가 열립니다.

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="개인 IP 접두사 편집":::

1. 기본적으로 **IANAPrivateRanges** 가 구성됩니다.
2. 사용자 환경에 대한 개인 IP 주소 범위를 편집하고 **저장** 을 선택합니다.

### <a name="firewall-policy"></a>방화벽 정책

1.  리소스 그룹을 선택한 다음, 방화벽을 선택합니다.
2.  **설정** 열에서 **개인 IP 범위(SNAT)** 를 선택합니다.

    기본적으로 **기본 Azure Firewall 정책 SNAT 동작 사용** 이 선택되어 있습니다. 
3. SNAT 구성을 사용자 지정하려면 확인란의 선택을 취소하고 **SNAT 수행** 에서 사용자 환경에 대해 SNAT를 수행할 조건을 선택합니다.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="개인 IP 범위(SNAT)":::


4.   **적용** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Firewall 강제 터널링](forced-tunneling.md)에 대해 알아봅니다.

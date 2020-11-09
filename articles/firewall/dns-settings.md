---
title: Azure 방화벽 DNS 설정
description: DNS 서버 및 DNS 프록시 설정을 사용 하 여 Azure 방화벽을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380949"
---
# <a name="azure-firewall-dns-settings"></a>Azure 방화벽 DNS 설정

사용자 지정 DNS 서버를 구성 하 고 Azure 방화벽에 DNS 프록시를 사용 하도록 설정할 수 있습니다. 이러한 설정은 **DNS 설정** 페이지에서 방화벽을 배포할 때 또는 나중에 구성할 수 있습니다.

## <a name="dns-servers"></a>DNS 서버

DNS 서버는 도메인 이름을 IP 주소에 유지 관리 하 고 확인 합니다. 기본적으로 Azure 방화벽은 이름 확인에 Azure DNS를 사용 합니다. **Dns 서버** 설정을 사용 하 여 Azure 방화벽 이름 확인을 위해 자체 dns 서버를 구성할 수 있습니다. 단일 서버 또는 다중 서버를 구성할 수 있습니다.

> [!NOTE]
> Azure 방화벽 관리자를 사용 하 여 관리 되는 Azure 방화벽의 경우 연결 된 Azure 방화벽 정책에서 DNS 설정이 구성 됩니다.

### <a name="configure-custom-dns-servers---azure-portal"></a>사용자 지정 DNS 서버 구성-Azure Portal

1. Azure 방화벽 **설정** 에서 **DNS 설정** 을 선택 합니다.
2. **Dns 서버** 에서 이전에 Virtual Network에 지정 된 기존 dns 서버를 입력 하거나 추가할 수 있습니다.
3. **저장** 을 선택합니다.
4. 이제 방화벽에서 이름 확인을 위해 DNS 트래픽을 지정 된 DNS 서버로 보냅니다.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS 서버":::

### <a name="configure-custom-dns-servers---azure-cli"></a>사용자 지정 DNS 서버 구성-Azure CLI

다음 예에서는 Azure CLI를 사용 하 여 사용자 지정 DNS 서버를 사용 하 여 Azure 방화벽을 업데이트 합니다.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> 명령을 `az network firewall` 사용 하려면 Azure CLI 확장 `azure-firewall` 을 설치 해야 합니다. 명령을 사용 하 여 설치할 수 있습니다 `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>사용자 지정 DNS 서버 구성-Azure PowerShell

다음 예에서는 Azure PowerShell를 사용 하 여 사용자 지정 DNS 서버를 사용 하 여 Azure 방화벽을 업데이트 합니다.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS 프록시

DNS 프록시 역할을 하도록 Azure 방화벽을 구성할 수 있습니다. Dns 프록시는 클라이언트 가상 컴퓨터에서 DNS 서버로의 DNS 요청에 대 한 중개자 역할을 합니다. 사용자 지정 DNS 서버를 구성 하는 경우 dns 확인이 일치 하지 않도록 DNS 프록시를 사용 하도록 설정 하 고 네트워크 규칙에서 FQDN 필터링을 사용 하도록 설정 해야 합니다.

DNS 프록시를 사용 하지 않는 경우 클라이언트의 DNS 요청이 다른 시간에 DNS 서버로 이동 하거나 방화벽의 경우와 비교 하 여 다른 응답을 반환할 수 있습니다. DNS 프록시는 불일치를 방지 하기 위해 Azure 방화벽을 클라이언트 요청의 경로에 배치 합니다.

Azure 방화벽이 DNS 프록시가 면 발생 하는 두 가지 캐싱 함수 형식이 있습니다.

- 긍정 캐시 – DNS 확인이 성공 합니다. 방화벽은 패킷 또는 개체의 TTL (time-to-live)을 사용 합니다. 

- 네거티브 캐시 – DNS 확인으로 인해 응답이 없거나 해결 되지 않습니다. 방화벽은 1 시간 동안이 정보를 캐시 합니다.

DNS 프록시는 네트워크 규칙에서 Fqdn의 모든 확인 된 IP 주소를 저장 합니다. IP 주소 하나를 확인 하는 Fqdn을 사용 하는 것이 가장 좋습니다.  

### <a name="dns-proxy-configuration"></a>DNS 프록시 구성

DNS 프록시 구성에는 다음 세 단계가 필요 합니다.
1. Azure 방화벽 DNS 설정에서 DNS 프록시를 사용 하도록 설정 합니다.
2. 필요에 따라 사용자 지정 DNS 서버를 구성 하거나 제공 된 기본값을 사용 합니다.
3. 마지막으로, 가상 네트워크 DNS 서버 설정에서 Azure 방화벽의 개인 IP 주소를 사용자 지정 DNS 주소로 구성 해야 합니다. 이렇게 하면 DNS 트래픽이 Azure 방화벽으로 전송 됩니다.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS 프록시 구성-Azure Portal

DNS 프록시를 구성 하려면 방화벽 개인 IP 주소를 사용 하도록 가상 네트워크 DNS 서버 설정을 구성 해야 합니다. 그런 다음 Azure 방화벽 **dns 설정** 에서 dns 프록시를 사용 하도록 설정 합니다.

##### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성 

1. Azure 방화벽을 통해 DNS 트래픽이 라우팅되는 가상 네트워크를 선택 합니다.
2. **설정** 아래에서 **DNS 서버** 를 선택합니다.
3. **DNS 서버** 아래에서 **사용자 지정** 을 선택 합니다.
4. 방화벽의 개인 IP 주소를 입력 합니다.
5. **저장** 을 선택합니다.
6. 가상 네트워크에 연결된 VM을 다시 시작하여 새 DNS 서버 설정을 할당합니다. VM이 다시 시작될 때까지 현재 DNS 설정을 계속 사용합니다.

##### <a name="enable-dns-proxy"></a>DNS 프록시 사용

1. Azure 방화벽을 선택 합니다.
2. **설정** 아래에서 **DNS 설정** 을 선택 합니다.
3. 기본적으로 **DNS 프록시** 는 사용 되지 않습니다. 사용 하도록 설정 되 면 방화벽은 포트 53에서 수신 하 고 DNS 요청을 구성 된 DNS 서버로 전달 합니다.
4. **DNS 서버** 구성을 검토 하 여 해당 설정이 사용자 환경에 적합 한지 확인 합니다.
5. **저장** 을 선택합니다.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS 프록시":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS 프록시 구성-Azure CLI

Azure CLI를 사용 하 여 Azure 방화벽에서 DNS 프록시 설정을 구성 하 고 Azure 방화벽을 DNS 서버로 사용 하도록 가상 네트워크를 업데이트할 수 있습니다.

##### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성

이 예제에서는 Azure 방화벽을 DNS 서버로 사용 하도록 VNet을 구성 합니다.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS 프록시 사용

이 예제에서는 Azure 방화벽에서 DNS 프록시 기능을 사용 하도록 설정 합니다.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS 프록시 구성-Azure PowerShell

Azure PowerShell를 사용 하 여 Azure 방화벽에서 DNS 프록시 설정을 구성 하 고 Azure 방화벽을 DNS 서버로 사용 하도록 가상 네트워크를 업데이트할 수 있습니다.

##### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성

 이 예제에서는 Azure 방화벽을 DNS 서버로 사용 하도록 VNet을 구성 합니다.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS 프록시 사용

이 예제에서는 Azure 방화벽에서 DNS 프록시 기능을 사용 하도록 설정 합니다.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>다음 단계

[네트워크 규칙의 FQDN 필터링](fqdn-filtering-network-rules.md)

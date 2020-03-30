---
title: 잘못된 게이트웨이 오류 해결 - Azure 응용 프로그램 게이트웨이
description: '응용 프로그램 게이트웨이 서버 오류 해결 방법: 502 - 웹 서버게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 받았습니다.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130478"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Application Gateway의 잘못된 게이트웨이 오류 문제 해결

Azure 응용 프로그램 게이트웨이를 사용할 때 받은 잘못된 게이트웨이(502) 오류 문제를 해결하는 방법을 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

응용 프로그램 게이트웨이를 구성한 후 "서버 오류: 502 - 웹 서버가 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 받았다"는 오류 중 하나입니다. 이 오류는 다음과 같은 주요 이유로 발생할 수 있습니다.

* NSG, UDR 또는 사용자 지정 DNS가 백 엔드 풀 멤버에 대한 액세스를 차단하고 있습니다.
* 백 엔드 VM 또는 가상 시스템 크기 집합의 인스턴스는 기본 상태 프로브에 응답하지 않습니다.
* 사용자 지정 상태 프로브의 구성이 잘못되었거나 부적절합니다.
* Azure 응용 프로그램 게이트웨이의 [백 엔드 풀이 구성되거나 비어 있지 않습니다.](#empty-backendaddresspool)
* 가상 머신 확장 집합의 VM 또는 인스턴스가 [모두 정상이 아닙니다](#unhealthy-instances-in-backendaddresspool).
* 사용자 [요청에 대한 시간 시간 제 또는 연결 문제를 요청합니다.](#request-time-out)

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>네트워크 보안 그룹, 사용자 정의 경로 또는 사용자 지정 DNS 문제

### <a name="cause"></a>원인

NSG, UDR 또는 사용자 지정 DNS로 인해 백 엔드에 대한 액세스가 차단된 경우 응용 프로그램 게이트웨이 인스턴스가 백 엔드 풀에 연결할 수 없습니다. 이로 인해 프로브 오류가 발생하여 502개의 오류가 발생합니다.

NSG/UDR은 응용 프로그램 게이트웨이 서브넷 또는 응용 프로그램 VM이 배포되는 서브넷에 있을 수 있습니다.

마찬가지로 VNet에 사용자 지정 DNS가 있으면 문제가 발생할 수도 있습니다. 백 엔드 풀 멤버에 사용되는 FQDN은 VNet에 대해 구성된 사용자가 DNS 서버에서 올바르게 해결하지 못할 수 있습니다.

### <a name="solution"></a>해결 방법

다음 단계를 통해 NSG, UDR 및 DNS 구성을 확인합니다.

* 응용 프로그램 게이트웨이 서브넷과 연결된 NSG를 확인합니다. 백 엔드에 대한 통신이 차단되지 않았는지 확인합니다.
* 응용 프로그램 게이트웨이 서브넷과 연결된 UDR을 확인합니다. UDR이 백 엔드 서브넷에서 트래픽을 멀리 향하지 않는지 확인합니다. 예를 들어 ExpressRoute/VPN을 통해 응용 프로그램 게이트웨이 서브넷에 보급되는 네트워크 가상 어플라이언스 또는 기본 경로로 의 라우팅을 확인합니다.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* 백 엔드 VM과의 유효한 NSG 및 경로를 확인합니다.

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* VNet에 사용자 지정 DNS의 존재 여부를 확인합니다. 출력에서 VNet 속성의 세부 정보를 확인하여 DNS를 확인할 수 있습니다.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
있는 경우 DNS 서버가 백 엔드 풀 멤버의 FQDN을 올바르게 해결할 수 있는지 확인합니다.

## <a name="problems-with-default-health-probe"></a>기본 상태 검색의 문제

### <a name="cause"></a>원인

502 오류는 기본 상태 프로브가 백 엔드 VM에 도달할 수 없다는 빈번한 지표일 수도 있습니다.

응용 프로그램 게이트웨이 인스턴스가 프로비전되면 백엔드HttpSetting의 속성을 사용하여 각 BackendAddressPool에 기본 상태 프로브를 자동으로 구성합니다. 이 프로브를 설정하기 위해 사용자 입력이 필요하지 않습니다. 특히 로드 밸런싱 규칙이 구성되면 백엔드HttpSetting과 백엔드주소풀 간에 연결이 이루어집니다. 기본 프로브는 이러한 각 연결에 대해 구성되며 응용 프로그램 게이트웨이는 BackendHttpSetting 요소에 지정된 포트에서 BackendAddressPool의 각 인스턴스에 대한 정기적인 상태 확인 연결을 시작합니다. 

다음 표에는 기본 상태 프로브와 연결된 값이 나열됩니다.

| 프로브 속성 | 값 | 설명 |
| --- | --- | --- |
| 프로브 URL |`http://127.0.0.1/` |URL 경로 |
| 간격 |30 |프로브 간격(초) |
| 시간 제한 |30 |프로브 시간 제한(초) |
| 비정상 임계값 |3 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

### <a name="solution"></a>해결 방법

* 기본 사이트를 구성하고 127.0.0.1에서 수신 대기 중인지를 확인합니다.
* BackendHttpSetting이 포트 80이 아닌 다른 포트를 지정하는 경우 기본 사이트는 해당 포트에서 수신하도록 구성되어야 합니다.
* `http://127.0.0.1:port` 호출은 HTTP 결과 코드 200을 반환해야 합니다. 이 기간은 30초 시간 내에 반환되어야 합니다.
* 구성된 포트가 열려 있고 구성된 포트에서 들어오는 트래픽이나 나가는 트래픽을 차단하는 방화벽 규칙 또는 Azure Network 보안 그룹이 없는지 확인합니다.
* Azure 클래식 VM 또는 클라우드 서비스가 FQDN 또는 공용 IP와 함께 사용되는 경우 해당 [끝점이](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) 열리는지 확인합니다.
* Azure 리소스 관리자를 통해 VM을 구성하고 응용 프로그램 게이트웨이가 배포된 VNet 외부에 있는 경우 원하는 포트에 대한 액세스를 허용하도록 [네트워크 보안 그룹을](../virtual-network/security-overview.md) 구성해야 합니다.

## <a name="problems-with-custom-health-probe"></a>사용자 지정 상태 검색의 문제

### <a name="cause"></a>원인

사용자 지정 상태 프로브를 사용하면 기본 검사 동작에 추가적인 유연성을 제공할 수 있습니다. 사용자 지정 프로브를 사용하는 경우 백 엔드 풀 인스턴스를 비정상으로 표시하기 전에 프로브 간격, URL, 테스트 경로 및 수락할 실패한 응답 수를 구성할 수 있습니다.

다음과 같은 추가 속성이 추가됩니다.

| 프로브 속성 | 설명 |
| --- | --- |
| 이름 |프로브 이름입니다. 이 이름은 백 엔드 HTTP 설정에서 프로브를 참조하는 데 사용됩니다. |
| 프로토콜 |프로브를 보내는 데 사용하는 프로토콜입니다. 프로브는 백 엔드 HTTP 설정에 정의된 프로토콜을 사용합니다. |
| 호스트 |프로브에 보낼 호스트 이름입니다. 다중 사이트가 응용 프로그램 게이트웨이에서 구성된 경우에만 적용됩니다. VM 호스트 이름과 다릅니다. |
| 경로 |프로브의 상대 경로입니다. 올바른 경로는 '/'부터 시작합니다. 프로브는 \<protocol\>://\<host\>:\<port\>\<path\>로 전송됩니다. |
| 간격 |프로브 간격(초). 연속된 두 프로브 사이의 시간 간격입니다. |
| 시간 제한 |프로브 시간 제한(초) 이 시간 지정 기간 내에 유효한 응답을 받지 못하면 프로브가 실패한 것으로 표시됩니다. |
| 비정상 임계값 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

### <a name="solution"></a>해결 방법

앞의 테이블처럼 사용자 지정 상태 프로브를 올바르게 구성했는지 유효성을 검사합니다. 앞의 문제 해결 단계 외에도 다음 사항을 확인합니다.

* 프로브가 [가이드](application-gateway-create-probe-ps.md)를 기준으로 올바르게 지정되어 있는지 확인합니다.
* 응용 프로그램 게이트웨이가 단일 사이트에 대해 구성된 경우 사용자 지정 `127.0.0.1`프로브에서 달리 구성되지 않는 한 기본적으로 호스트 이름을 로 지정해야 합니다.
* http://\<host\>:\<port\>\<path\>에 대한 호출은 HTTP 결과 코드 200을 반환해야 합니다.
* 간격, 시간 초과 및 Unhealty임계값이 허용 가능한 범위 내에 있는지 확인합니다.
* HTTPS 프로브를 사용하는 경우 백 엔드 서버 자체에서 대체(fallback) 인증서를 구성하여 백 엔드 서버에 SNI가 필요하지 않도록 합니다.

## <a name="request-time-out"></a>요청 시간 초과

### <a name="cause"></a>원인

사용자 요청이 수신되면 응용 프로그램 게이트웨이는 구성된 규칙을 요청에 적용하고 백 엔드 풀 인스턴스로 라우팅합니다. 백 엔드 인스턴스의 응답에 대해 구성 가능한 시간 간격을 기다립니다. 기본적으로 이 간격은 **20초입니다.** 응용 프로그램 게이트웨이가 이 간격으로 백 엔드 응용 프로그램에서 응답을 받지 못하면 사용자 요청에 502 오류가 발생합니다.

### <a name="solution"></a>해결 방법

응용 프로그램 게이트웨이를 사용하면 다른 풀에 적용할 수 있는 BackendHttpSetting을 통해 이 설정을 구성할 수 있습니다. 다른 백 엔드 풀에는 다른 BackendHttpSetting이 있을 수 있으며 다른 요청 시간 아웃이 구성될 수 있습니다.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>비어 있는 BackendAddressPool

### <a name="cause"></a>원인

응용 프로그램 게이트웨이에 백 엔드 주소 풀에 구성된 VM 또는 가상 시스템 크기 집합이 없는 경우 고객 요청을 라우팅할 수 없으며 잘못된 게이트웨이 오류를 보냅니다.

### <a name="solution"></a>해결 방법

백 엔드 주소 풀이 비어 있지 않은지 확인합니다. PowerShell, CLI 또는 포털을 통해 수행할 수 있습니다.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

앞의 cmdlet에서 출력은 비어 있지 않은 백 엔드 주소 풀을 포함해야 합니다. 다음 예제에서는 FQDN 또는 백 엔드 VM에 대한 IP 주소로 구성된 반환된 두 개의 풀을 보여 주십니다. BackendAddressPool의 상태를 프로비전하는 작업은 '성공'해야 합니다.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>BackendAddressPool의 비정상 인스턴스

### <a name="cause"></a>원인

BackendAddressPool의 모든 인스턴스가 비정상이면 응용 프로그램 게이트웨이에 사용자 요청을 라우팅할 백 엔드가 없습니다. 백 엔드 인스턴스가 정상이지만 필요한 응용 프로그램이 배포되지 않은 경우에도 마찬가지일 수 있습니다.

### <a name="solution"></a>해결 방법

인스턴스가 정상이고 애플리케이션이 올바르게 구성되어 있는지 확인합니다. 백 엔드 인스턴스가 동일한 VNet의 다른 VM에서 ping에 응답할 수 있는지 확인합니다. 공용 끝점으로 구성된 경우 웹 응용 프로그램에 대한 브라우저 요청을 서비스 할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓을](https://azure.microsoft.com/support/options/)엽니다.


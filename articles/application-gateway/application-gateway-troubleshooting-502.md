---
title: Azure Application Gateway 잘못 된 게이트웨이 (502) 오류 문제 해결
description: Application Gateway 502 오류를 해결하는 방법을 알아봅니다
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697155"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Application Gateway의 잘못된 게이트웨이 오류 문제 해결

Azure Application Gateway를 사용 하는 경우 수신 하는 잘못 된 게이트웨이 (502) 오류를 해결 하는 방법에 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

Application gateway를 구성한 후 볼 수 있는 오류 중 하나는 "서버 오류: 502 - 웹 서버에서 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 수신했습니다." 이 오류는 다음과 같은 주요 이유로 발생할 수 있습니다.

* NSG, UDR 또는 사용자 지정 DNS 백 엔드 풀 멤버에 대 한 액세스를 차단 됩니다.
* 백 엔드 Vm 또는 가상 머신 확장 집합의 인스턴스는 기본 상태 프로브에 응답 되지 않습니다.
* 사용자 지정 상태 프로브의 구성이 잘못되었거나 부적절합니다.
* Azure Application Gateway [백 엔드 풀 구성 또는 빈 아니라고](#empty-backendaddresspool)합니다.
* 가상 머신 확장 집합의 VM 또는 인스턴스가 [모두 정상이 아닙니다](#unhealthy-instances-in-backendaddresspool).
* 사용자 요청과 관련된 [요청 시간 초과 또는 연결 문제입니다](#request-time-out).

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>네트워크 보안 그룹, 사용자 정의 경로 또는 사용자 지정 DNS 문제

### <a name="cause"></a>원인

백 엔드에 대 한 액세스는 NSG, UDR 또는 사용자 지정 DNS로 인해 차단 된 경우 application gateway 인스턴스에 백 엔드 풀을 연결할 수 없습니다. 이렇게 하면 프로브 오류, 502 오류가 발생 합니다.

응용 프로그램 Vm 배포 된 경우 NSG/UDR은 응용 프로그램 게이트웨이 서브넷 또는 서브넷에 있을 수 있습니다.

마찬가지로, VNet의 사용자 지정 DNS의 존재 문제가 발생할 수 있습니다. 백 엔드 풀 멤버에 사용 되는 FQDN을 확인할 수 없습니다 올바르게 사용자가 구성한 DNS 서버가 VNet에 대 한.

### <a name="solution"></a>해결 방법

다음 단계를 통해 NSG, UDR 및 DNS 구성을 확인합니다.

* Application gateway 서브넷과 Nsg 연결을 확인 합니다. 백 엔드와 통신이 차단 되지 않았는지 확인 합니다.
* Application gateway 서브넷과 연결 된 UDR을 확인 합니다. UDR은 백 엔드 서브넷에서 트래픽이 되지 있는지 확인 합니다. 예를 들어, 네트워크 가상 어플라이언스 또는 ExpressRoute/VPN을 통해 응용 프로그램 게이트웨이 서브넷에 보급 되는 기본 경로 대 한 라우팅을 확인 합니다.

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
있는 경우 DNS 서버가 백 엔드 풀 멤버의 FQDN을 올바르게 확인할 수 있는지 확인 합니다.

## <a name="problems-with-default-health-probe"></a>기본 상태 검색의 문제

### <a name="cause"></a>원인

502 오류는 기본 상태 프로브는 백 엔드 Vm에 연결할 수 없습니다는 부당한 수도 있습니다.

응용 프로그램 게이트웨이 인스턴스를 프로 비전 되 면 자동으로 BackendHttpSetting의 속성을 사용 하 여 각 BackendAddressPool에 기본 상태 프로브를 구성 합니다. 이 프로브를 설정하기 위해 사용자 입력이 필요하지 않습니다. 특히, 부하 분산 규칙을 구성한 경우 연결을 BackendHttpSetting와 BackendAddressPool 간에 이루어집니다. 이러한 연결 각각에 대 한 기본 프로브는 구성 및 application gateway가 BackendHttpSetting 요소에 지정 된 포트에서 BackendAddressPool의 각 인스턴스는 정기 상태 검사 연결을 시작 합니다. 

다음 표에서 기본 상태 프로브를 사용 하 여 관련 된 값을 나열 합니다.

| 프로브 속성 | 값 | 설명 |
| --- | --- | --- |
| 프로브 URL |`http://127.0.0.1/` |URL 경로 |
| 간격 |30 |프로브 간격(초) |
| 시간 제한 |30 |프로브 시간 제한(초) |
| 비정상 임계값 |3 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

### <a name="solution"></a>해결 방법

* 기본 사이트를 구성하고 127.0.0.1에서 수신 대기 중인지를 확인합니다.
* BackendHttpSetting이 포트 80이 아닌 다른 포트를 지정하는 경우 기본 사이트는 해당 포트에서 수신하도록 구성되어야 합니다.
* `http://127.0.0.1:port` 호출은 HTTP 결과 코드 200을 반환해야 합니다. 30 초 제한 시간 내에 반환 되어야 합니다.
* 구성 된 포트가 열려 있고 방화벽 규칙 또는 구성 된 포트에서 들어오거나 나가는 트래픽을 차단 하는 Azure 네트워크 보안 그룹을 확인 합니다.
* Azure 클래식 Vm 또는 클라우드 서비스 공용 IP 또는 FQDN을 사용 하는 경우 확인 하는 해당 [끝점](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) 열립니다.
* Azure Resource Manager를 통해 구성 된 VM은 VNet 외부에 application gateway 배포 되는 위치 경우는 [네트워크 보안 그룹](../virtual-network/security-overview.md) 원하는 포트에 대 한 액세스를 허용 하도록 구성 해야 합니다.

## <a name="problems-with-custom-health-probe"></a>사용자 지정 상태 검색의 문제

### <a name="cause"></a>원인

사용자 지정 상태 프로브를 사용하면 기본 검사 동작에 추가적인 유연성을 제공할 수 있습니다. 사용자 지정 프로브를 사용 하는 경우 프로브 간격, URL, 테스트에 대 한 경로 및 백 엔드 풀 인스턴스를 비정상으로 표시 하기 전에 허용할 실패 응답 횟수를 구성할 수 있습니다.

다음 추가 속성이 추가 되었습니다.

| 프로브 속성 | 설명 |
| --- | --- |
| 이름 |프로브 이름입니다. 이 이름은 백 엔드 HTTP 설정에서 프로브를 참조하는 데 사용됩니다. |
| 프로토콜 |프로브를 보내는 데 사용하는 프로토콜입니다. 프로브는 백 엔드 HTTP 설정에 정의된 프로토콜을 사용합니다. |
| 호스트 |프로브에 보낼 호스트 이름입니다. 다중 사이트 application gateway에 구성 된 경우에 적용할 수 있습니다. VM 호스트 이름과 다릅니다. |
| path |프로브의 상대 경로입니다. 올바른 경로는 '/'부터 시작합니다. 프로브는 \<protocol\>://\<host\>:\<port\>\<path\>로 전송됩니다. |
| Interval |프로브 간격(초). 연속된 두 프로브 사이의 시간 간격입니다. |
| 시간 제한 |프로브 시간 제한(초) 이 시간 제한 기간 내에 올바른 응답이 수신 되지 않습니다, 경우 프로브는 실패로 표시 됩니다. |
| 비정상 임계값 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

### <a name="solution"></a>해결 방법

앞의 테이블처럼 사용자 지정 상태 프로브를 올바르게 구성했는지 유효성을 검사합니다. 앞의 문제 해결 단계 외에도 다음 사항을 확인합니다.

* 프로브가 [가이드](application-gateway-create-probe-ps.md)를 기준으로 올바르게 지정되어 있는지 확인합니다.
* Application gateway를 단일 사이트에 대해 구성 된 경우 기본적으로 호스트 이름으로 지정 해야 `127.0.0.1`사용자 지정 프로브에서 구성 되지 않은 경우.
* http://\<host\>:\<port\>\<path\>에 대한 호출은 HTTP 결과 코드 200을 반환해야 합니다.
* 간격, 제한 시간 및 unhealtythreshold이 허용 가능한 범위 내에 있는지 확인 합니다.
* HTTPS 프로브를 사용하는 경우 백 엔드 서버 자체에서 대체(fallback) 인증서를 구성하여 백 엔드 서버에 SNI가 필요하지 않도록 합니다.

## <a name="request-time-out"></a>요청 시간 초과

### <a name="cause"></a>원인

사용자 요청이 수신 되 면 application gateway는 요청에 구성된 된 규칙을 적용 하 고 백 엔드 풀 인스턴스로 라우팅합니다. 백 엔드 인스턴스의 응답에 대해 구성 가능한 시간 간격을 기다립니다. 기본적으로이 간격은 **20** 시간 (초)입니다. Application gateway는이 간격에서 백 엔드 응용 프로그램에서 응답을 수신 하지 않습니다, 경우 사용자 요청에 502 오류를 가져옵니다.

### <a name="solution"></a>해결 방법

Application Gateway를 사용 하면 다음 다른 풀에 적용할 수 있는 BackendHttpSetting 통해이 설정을 구성할 수 있습니다. 다른 백 엔드 풀에서는 다른 BackendHttpSetting 및 구성 하는 다른 요청 시간 제한이 있을 수 있습니다.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>비어 있는 BackendAddressPool

### <a name="cause"></a>원인

Application gateway에 Vm 또는 가상 머신 확장 집합에 없는 경우 백 엔드 주소 풀에 구성 된 고객의 요청을 라우팅할 수 없습니다 하 고 잘못 된 게이트웨이 오류를 보냅니다.

### <a name="solution"></a>해결 방법

백 엔드 주소 풀을 비어 있지 않은 있는지 확인 합니다. PowerShell, CLI 또는 포털을 통해 수행할 수 있습니다.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

앞의 cmdlet에서 출력은 비어 있지 않은 백 엔드 주소 풀을 포함해야 합니다. 다음 예제에서는 두 개의 풀 반환 하도록 구성 된 백 엔드 Vm에는 IP 주소 또는 FQDN을 사용 하 여 보여 줍니다. BackendAddressPool의 상태를 프로비전하는 작업은 '성공'해야 합니다.

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

BackendAddressPool의 모든 인스턴스가 정상이 아닌 경우 application gateway에 사용자 요청을 라우팅할 백 엔드가 없는 다음입니다. 백 엔드 인스턴스가 정상 이지만 필요한 응용 프로그램 배포에 없는 경우에이 경우 수 있습니다.

### <a name="solution"></a>해결 방법

인스턴스가 정상이고 애플리케이션이 올바르게 구성되어 있는지 확인합니다. 백 엔드 인스턴스가 동일한 VNet의 다른 VM에서 ping에 응답 하는 경우를 확인 합니다. 공용 끝점을 사용 하 여 구성 하는 경우에 웹 응용 프로그램을 브라우저 요청을 서비스할 수를 확인 합니다.

## <a name="next-steps"></a>다음 단계

이전 단계에서 문제를 해결 하지 하는 경우 열을 [지원 티켓](https://azure.microsoft.com/support/options/)합니다.


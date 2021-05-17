---
title: 진단 로그를 사용하여 Azure VPN Gateway 문제 해결
description: 진단 로그를 사용하여 Azure VPN Gateway 문제 해결
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772005"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>진단 로그를 사용하여 Azure VPN Gateway 문제 해결

이 문서는 VPN Gateway 진단에 사용할 수 있는 여러 로그와 이러한 로그를 사용하여 VPN Gateway 문제를 효과적으로 해결하는 방법을 이해하는 데 도움이 됩니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Azure에서 사용할 수 있는 로그는 다음과 같습니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**GatewayDiagnosticLog** | 게이트웨이 구성 이벤트, 기본 변경, 유지 관리 이벤트에 대한 진단 로그를 포함합니다. |
|**TunnelDiagnosticLog** | 터널 상태 변경 이벤트를 포함합니다. 터널 연결/연결 끊기 이벤트에는 상태 변경의 요약된 이유(해당하는 경우)가 있습니다. |
|**RouteDiagnosticLog** | 게이트웨이에서 발생하는 고정 경로 및 BGP 이벤트에 대한 변경 내용을 로그합니다. |
|**IKEDiagnosticLog** | 게이트웨이에서 IKE 제어 메시지 및 이벤트를 로그합니다. |
|**P2SDiagnosticLog** | 게이트웨이에서 사이트 제어 메시지 및 이벤트를 로그합니다. |

이러한 테이블에서 여러 열을 사용할 수 있습니다. 이 문서에서는 로그를 보다 쉽게 사용할 수 있도록 가장 관련성이 높은 항목만 설명합니다.

## <a name="set-up-logging"></a><a name="setup"></a>로깅 설정

Azure Log Analytics를 사용하여 Azure VPN Gateway에서 진단 로그 이벤트를 설정하는 방법을 알아보려면 [VPN Gateway에서 진단 로그 이벤트에 대한 경고 설정](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log)을 참조하세요.

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

구성 변경 내용은 **GatewayDiagnosticLog** 테이블에서 감사됩니다. 실행한 변경 내용이 로그에 반영되기까지 몇 분 정도 걸릴 수 있습니다.

다음 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

**GatewayDiagnosticLog** 에 대한 이 쿼리는 여러 열을 표시합니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임스탬프(UTC 표준 시간대)입니다.|
|**OperationName** |발생한 이벤트입니다. *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration* 중 하나일 수 있습니다.|
|**Message** | 작업의 세부 정보를 표시하고 성공/실패 결과를 나열합니다.|

아래 예제는 새 구성이 적용될 때 로그된 활동을 보여 줍니다.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="GatewayDiagnosticLog에서 표시되는 게이트웨이 설정 작업의 예제.":::


VPN Gateway 또는 로컬 네트워크 게이트웨이에서 일부 구성이 수정될 때마다 SetGatewayConfiguration이 로그됩니다.
**GatewayDiagnosticLog** 테이블의 결과와 **TunnelDiagnosticLog** 테이블의 결과를 교차 참조하면 구성이 변경되거나 유지 관리가 수행될 때 터널 연결 오류가 동시에 시작되었는지 여부를 확인하는 데 도움이 될 수 있습니다. 그렇다면 가능한 근본 원인을 찾는 데 도움이 됩니다.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

**TunnelDiagnosticLog** 테이블은 터널의 과거 연결 상태를 검사하는 데 매우 유용합니다.

다음 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

**TunnelDiagnosticLog** 에 대한 이 쿼리는 여러 열을 표시합니다.


|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임스탬프(UTC 표준 시간대)입니다.|
|**OperationName** | 발생한 이벤트입니다. *TunnelConnected* 또는 *TunnelDisconnected* 일 수 있습니다.|
| **Instance\_s** | 이벤트를 트리거한 게이트웨이 역할 인스턴스입니다. 게이트웨이의 두 인스턴스 이름인 GatewayTenantWorker\_IN\_0 또는 GatewayTenantWorker\_IN\_1일 수 있습니다.|
| **리소스** | VPN 게이트웨이의 이름을 나타냅니다. |
| **ResourceGroup** | 게이트웨이가 있는 리소스 그룹을 나타냅니다.|


예제 출력:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="TunnelDiagnosticLog에 표시되는 터널 연결 이벤트의 예제.":::


**TunnelDiagnosticLog** 는 예기치 않은 VPN 연결 끊김에 관한 이전 이벤트 문제 해결에 매우 유용합니다. 경량이기 때문에 적은 노력으로 여러 날에 걸친 큰 시간 범위를 분석할 수 있습니다.
연결 끊김 타임스탬프를 확인한 후에만 **IKEdiagnosticLog** 테이블에 대한 자세한 분석으로 전환하여 연결 끊김의 이유가 IPsec과 관련이 있는지 자세히 살펴볼 수 있습니다.


아래에는 몇 가지 문제 해결 팁이 나와 있습니다.
- 한 게이트웨이 인스턴스에서 연결 끊김 이벤트가 발생하고 몇 초 후에 **다른** 게이트웨이 인스턴스에서 연결 이벤트가 발생한다면 게이트웨이 장애 조치(failover)입니다. 이는 게이트웨이 인스턴스에 대한 유지 관리로 인해 일반적으로 예상되는 동작입니다. 이 동작에 대해 자세히 알아보려면 [Azure VPN gateway 중복도 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy)를 참조하세요.
- Azure 쪽에서 의도적으로 게이트웨이 재설정을 실행하여 활성 게이트웨이 인스턴스를 다시 부팅하는 경우에도 동일한 동작이 관찰됩니다. 이 동작에 대해 자세히 알아보려면 [VPN Gateway 다시 설정](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic)을 참조하세요.
- 한 게이트웨이 인스턴스에서 연결 끊김 이벤트가 발생하고 몇 초 후에 **동일한** 게이트웨이 인스턴스에서 연결 이벤트가 발생한다면 DPD 시간 초과를 유발하는 네트워크 결함이거나 온-프레미스 디바이스가 잘못 전송한 연결 끊김입니다.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

**RouteDiagnosticLog** 테이블은 BGP를 통해 수신되는 정적으로 수정된 경로 또는 경로에 대한 활동을 추적합니다.

다음 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**RouteDiagnosticLog** 에 대한 이 쿼리는 여러 열을 표시합니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임스탬프(UTC 표준 시간대)입니다.|
|**OperationName** | 발생한 이벤트입니다. *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent* 중 하나일 수 있습니다.|
| **Message** | 실행 중인 작업의 세부 정보입니다.|

출력에는 연결/연결 해제된 BGP 피어 및 교환된 경로에 대한 유용한 정보가 표시됩니다.

예:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="RouteDiagnosticLog에 표시되는 BGP 경로 교환 활동의 예제.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

**IKEDiagnosticLog** 테이블은 IKE/IPsec에 대한 자세한 디버그 로깅을 제공합니다. 연결 끊김 또는 VPN 연결 실패 시나리오 문제를 해결할 때 이 로깅을 검토하면 매우 유용합니다.

다음 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

**IKEDiagnosticLog** 에 대한 이 쿼리는 여러 열을 표시합니다.


|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임스탬프(UTC 표준 시간대)입니다.|
| **RemoteIP** | 온-프레미스 VPN 디바이스의 IP 주소 실제 시나리오에서는 관련 온-프레미스 디바이스가 둘 이상인 경우 해당 디바이스의 IP 주소로 필터링하는 것이 유용합니다. |
|**LocalIP** | 문제를 해결할 VPN Gateway의 IP 주소입니다. 실제 시나리오에서는 구독에 관련 VPN 게이트웨이가 둘 이상 있는 경우 해당 VPN 게이트웨이의 IP 주소로 필터링하는 것이 유용합니다. |
|**이벤트** | 문제 해결에 유용한 진단 메시지를 포함합니다. 일반적으로 키워드로 시작하며 Azure 게이트웨이에서 수행되는 작업을 참조합니다. **\[SEND\]** 는 Azure 게이트웨이에서 보낸 IPSec 패킷으로 인한 이벤트를 나타냅니다.  **\[RECEIVED\]** 는 온-프레미스 디바이스에서 수신한 패킷으로 인한 이벤트를 나타냅니다.  **\[LOCAL\]** 은 Azure 게이트웨이에서 로컬로 수행하는 작업을 나타냅니다. |


RemoteIP, LocalIP, Event 열은 AzureDiagnostics 데이터베이스의 원래 열 목록에는 없지만 ‘Message’ 열의 출력을 구문 분석하여 쿼리에 추가하면 분석이 간소화됩니다.

문제 해결 팁:

- IPSec 협상의 시작을 식별하려면 초기 SA\_INIT 메시지를 찾아야 합니다. 이러한 메시지는 터널의 어느 쪽에서도 전송될 수 있습니다. 첫 번째 패킷을 전송하는 쪽을 IPsec 용어로 ‘개시자’라고 하며, 반대쪽은 ‘응답자’가 됩니다. 첫 번째 SA\_INIT 메시지는 항상 rCookie = 0인 메시지입니다.

- IPsec 터널을 설정하지 못하면 Azure는 몇 초마다 재시도를 계속합니다. 따라서 IKEdiagnosticLog에서 ‘VPN 다운’ 문제를 해결하는 것이 매우 편리합니다. 문제를 재현하기 위해 특정 시간 동안 기다릴 필요가 없기 때문입니다. 또한 시도할 때마다 실패는 이론적으로 항상 동일하기 때문에 언제든지 하나의 ‘샘플’ 협상 실패만 살펴보면 됩니다.

- SA\_INIT에는 피어가 이 IPsec 협상에 사용하려는 IPsec 매개 변수가 포함됩니다. 공식 문서   
[기본 IPsec/IKE 매개 변수](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec)는 기본 설정으로 Azure 게이트웨이에서 지원하는 IPsec 매개 변수를 나열합니다.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

VPN 진단에 사용할 수 있는 마지막 테이블은 **P2SDiagnosticLog** 입니다. 이 테이블은 지점-사이트 간 활동을 추적합니다.

다음 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**P2SDiagnosticLog** 에 대한 이 쿼리는 여러 열을 표시합니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임스탬프(UTC 표준 시간대)입니다.|
|**OperationName** | 발생한 이벤트입니다. *P2SLogEvent* 가 됩니다.|
| **Message** | 실행 중인 작업의 세부 정보입니다.|

출력에는 게이트웨이가 적용한 모든 지점-사이트 간 설정과 적용되는 IPsec 정책이 표시됩니다.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="P2SDiagnosticLog에 표시되는 지점-사이트 간 연결의 예제.":::

또한 클라이언트가 IKEv2 또는 OpenVPN P2S를 통해 연결할 때마다 이 테이블은 패킷 활동, EAP/RADIUS 대화 및 성공/실패 결과를 사용자별로 로그합니다.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="P2SDiagnosticLog에 표시되는 EAP 인증의 예제.":::

## <a name="next-steps"></a>다음 단계

터널 리소스 로그에 대한 경고를 구성하려면 [VPN Gateway 리소스 로그에 대한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)을 참조하세요.


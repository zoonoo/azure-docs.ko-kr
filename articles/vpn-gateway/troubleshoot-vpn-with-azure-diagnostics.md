---
title: 진단 로그를 사용 하 여 Azure VPN Gateway 문제 해결
description: 진단 로그를 사용 하 여 Azure VPN Gateway 문제 해결
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 4e65dcd448a2ab2fad635cab12f41d858416becf
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726071"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>진단 로그를 사용 하 여 Azure VPN Gateway 문제 해결

이 문서에서는 VPN Gateway 진단에 사용할 수 있는 여러 로그와 이러한 로그를 사용 하 여 VPN Gateway 문제를 효과적으로 해결 하는 방법을 설명 합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Azure에서 사용할 수 있는 로그는 다음과 같습니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**GatewayDiagnosticLog** | 게이트웨이 구성 이벤트, 기본 변경 내용 및 유지 관리 이벤트에 대 한 진단 로그를 포함 합니다. |
|**TunnelDiagnosticLog** | 터널 상태 변경 이벤트를 포함 합니다. 해당 하는 경우 터널 연결/연결 끊기 이벤트에 상태 변경에 대 한 요약 된 이유가 있습니다. |
|**RouteDiagnosticLog** | 게이트웨이에서 발생 하는 고정 경로 및 BGP 이벤트에 대 한 변경 내용을 기록 합니다. |
|**IKEDiagnosticLog** | 게이트웨이에서 IKE 제어 메시지 및 이벤트를 로깅합니다. |
|**P2SDiagnosticLog** | 게이트웨이에서 지점 및 사이트 제어 메시지 및 이벤트를 로깅합니다. |

이러한 테이블에는 몇 개의 열을 사용할 수 있습니다. 이 문서에서는 로그 사용을 용이 하 게 하기 위해 가장 관련성이 높은 항목만 제시 합니다.

## <a name="set-up-logging"></a><a name="setup"></a>로깅 설정

Azure Log Analytics를 사용 하 여 Azure VPN Gateway에서 진단 로그 이벤트를 설정 하는 방법을 알아보려면 [VPN Gateway에서 진단 로그 이벤트에 대 한 경고 설정](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log)을 참조 하세요.

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

**GatewayDiagnosticLog** 테이블에서 구성 변경 내용을 감사 합니다. 실행 하는 변경 내용이 로그에 반영 되기까지 몇 분 정도 걸릴 수 있습니다.

여기서는 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

**GatewayDiagnosticLog** 에 대 한이 쿼리는 여러 열을 표시 합니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임 스탬프 (UTC 표준 시간대)|
|**OperationName** |발생 한 이벤트입니다. *Set게이트웨이 구성, Setgatewayconfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration* 중 하나일 수 있습니다.|
|**메시지** | 작업의 세부 정보 및 성공/실패 결과를 나열 합니다.|

아래 예제에서는 새 구성이 적용 될 때 기록 되는 활동을 보여 줍니다.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="게이트웨이 작업 예를 설정 합니다.":::


VPN Gateway 또는 로컬 네트워크 게이트웨이에서 일부 구성이 수정 될 때마다 Setgateway 구성이 기록 됩니다.
**GatewayDiagnosticLog** 테이블에서 **TunnelDiagnosticLog** 테이블의 결과를 교차 참조 하면 구성이 변경 되거나 유지 관리가 수행 될 때 터널 연결 오류가 동시에 시작 되었는지 여부를 확인 하는 데 도움이 될 수 있습니다. 그렇다면 가능한 근본 원인에 대 한 유용한 포인터가 있습니다.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

**TunnelDiagnosticLog** 테이블은 터널의 기록 연결 상태를 검사 하는 데 매우 유용 합니다.

여기서는 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

**TunnelDiagnosticLog** 에 대 한이 쿼리는 여러 열을 표시 합니다.


|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임 스탬프 (UTC 표준 시간대)|
|**OperationName** | 발생 한 이벤트입니다. *TunnelConnected* 또는 *TunnelDisconnected* 일 수 있습니다.|
| **인스턴스 \_ s** | 이벤트를 트리거한 게이트웨이 역할 인스턴스입니다. 이는 \_ \_ \_ \_ 게이트웨이의 두 인스턴스 이름에 해당 하는 0 또는 gatewaytenantworker (1의 경우) 중 하나일 수 있습니다.|
| **리소스** | VPN 게이트웨이의 이름을 나타냅니다. |
| **ResourceGroup** | 게이트웨이가 있는 리소스 그룹을 나타냅니다.|


예제 출력:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="터널 연결 이벤트 예제입니다.":::


**TunnelDiagnosticLog** 는 예기치 않은 VPN 연결이 아닌 이전 이벤트 문제를 해결 하는 데 매우 유용 합니다. 경량 특성은 적은 노력으로 며칠 동안 많은 시간 범위를 분석할 수 있는 가능성을 제공 합니다.
연결 끊기 타임 스탬프를 확인 한 후에만 **IKEdiagnosticLog** 테이블에 대 한 자세한 분석으로 전환 하 여 연결 끊김의 이유를 자세히 살펴볼 수 있습니다.


아래에는 몇 가지 문제 해결 팁이 나와 있습니다.
- 한 게이트웨이 인스턴스에서 연결 끊기 이벤트가 발생 하 고 몇 초 내에 **다른** 게이트웨이 인스턴스에 대 한 연결 이벤트가 발생 하는 경우 게이트웨이 장애 조치 (failover)를 확인 하 게 됩니다. 이는 게이트웨이 인스턴스에 대 한 유지 관리로 인해 일반적으로 예상 되는 동작입니다. 이 동작에 대해 자세히 알아보려면 [AZURE VPN gateway 중복성 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy)를 참조 하세요.
- Azure 측면에서 의도적으로 게이트웨이 재설정을 실행 하 여 활성 게이트웨이 인스턴스를 다시 부팅 하는 경우에도 동일한 동작이 관찰 됩니다. 이 동작에 대해 자세히 알아보려면 [VPN Gateway 다시 설정](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic) 을 참조 하세요.
- 한 게이트웨이 인스턴스에서 연결 끊기 이벤트가 발생 하 고, 몇 초 후에 **동일한** 게이트웨이 인스턴스에 대 한 연결 이벤트가 발생 하는 경우, 네트워크 결함으로 인해 lpd 시간 제한이 발생 하거나 온-프레미스 장치에서 잘못 된 연결이 끊어진 것을 볼 수 있습니다.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

**RouteDiagnosticLog** 테이블은 BGP를 통해 수신 되는 정적으로 수정 된 경로 또는 경로에 대 한 활동을 추적 합니다.

여기서는 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**RouteDiagnosticLog** 에 대 한이 쿼리는 여러 열을 표시 합니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임 스탬프 (UTC 표준 시간대)|
|**OperationName** | 발생 한 이벤트입니다. *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent* 중 하나일 수 있습니다.|
| **메시지** | 수행 되는 작업에 대 한 세부 정보입니다.|

출력에는 연결/연결 끊김 및 교환 된 경로에 대 한 유용한 정보가 표시 됩니다.

예제:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="BGP 경로 예제입니다.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

**IKEDiagnosticLog** 테이블은 IKE/IPsec에 대 한 자세한 디버그 로깅을 제공 합니다. 이 기능은 연결 끊김 문제를 해결 하거나 VPN 시나리오 연결에 실패 하는 경우를 검토 하는 데 매우 유용 합니다.

여기서는 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

**IKEDiagnosticLog** 에 대 한이 쿼리는 여러 열을 표시 합니다.


|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임 스탬프 (UTC 표준 시간대)|
| **RemoteIP** | 온-프레미스 VPN 장치의 IP 주소입니다. 실제 시나리오에서는 관련 온-프레미스 장치의 IP 주소로 필터링 하는 것이 유용 합니다. |
|**LocalIP** | 문제를 해결할 VPN Gateway의 IP 주소입니다. 실제 시나리오에서는 관련 VPN 게이트웨이의 IP 주소를 기준으로 필터링 하는 것이 유용 합니다. |
|**이벤트** | 문제 해결에 유용한 진단 메시지를 포함 합니다. 일반적으로 키워드를 사용 하 여 시작 하 고 Azure 게이트웨이 **\[ 송신 \]** 이 수행한 작업을 참조 합니다. **\[ 수신 \]** 된 azure 게이트웨이에서 보낸 IPSec 패킷으로 인해 발생 한 이벤트는 온-프레미스 장치 **\[ 로컬 \]** 에서 받은 패킷을 통해 azure 게이트웨이에서 로컬로 수행 된 작업을 나타냅니다. |


RemoteIP, LocalIP 및 이벤트 열이 AzureDiagnostics 데이터베이스의 원래 열 목록에는 없지만 "메시지" 열의 출력을 구문 분석 하 여 분석을 단순화 함으로써 쿼리에 추가 되는 방법을 확인 합니다.

문제 해결 팁:

- IPSec 협상의 시작을 식별 하기 위해 초기 SA INIT 메시지를 찾아야 합니다 \_ . 이러한 메시지는 터널의 한쪽에서 전송 될 수 있습니다. 첫 번째 패킷을 전송 하는 사람은 IPsec 용어로 "개시자" 라고 하며, 다른 쪽은 "응답자"가 됩니다. 첫 번째 SA \_ INIT 메시지는 항상 rCookie가 0 인 메시지입니다.

- IPsec 터널을 설정 하지 못하면 Azure는 몇 초 마다 재시도를 계속 합니다. 이러한 이유로 문제를 재현 하기 위해 특정 시간을 기다릴 필요가 없기 때문에 "VPN 다운" 문제를 해결 하는 것은 IKEdiagnosticLog에서 매우 편리 합니다. 또한 오류는 이론적으로 항상 동일 하 게 수행 되므로 언제 든 지 하나의 "샘플" 협상 실패를 확대할 수 있습니다.

- SA \_ INIT는 피어가이 ipsec 협상에 사용 하려고 하는 ipsec 매개 변수를 포함 합니다. 공식 문서   
[기본 ipsec/IKE 매개 변수](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) 기본 설정으로 Azure 게이트웨이에서 지 원하는 ipsec 매개 변수를 나열 합니다.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

VPN 진단에 사용할 수 있는 마지막 테이블은 **P2SDiagnosticLog** 입니다. 지점 및 사이트에 대 한 작업을 추적 합니다.

여기서는 샘플 쿼리를 참조로 사용할 수 있습니다.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**P2SDiagnosticLog** 에 대 한이 쿼리는 여러 열을 표시 합니다.

|***이름** _ | _ *_설명_** |
|---        | ---               |
|**TimeGenerated** | 각 이벤트의 타임 스탬프 (UTC 표준 시간대)|
|**OperationName** | 발생 한 이벤트입니다. *P2SLogEvent* 됩니다.|
| **메시지** | 수행 되는 작업에 대 한 세부 정보입니다.|

출력에는 게이트웨이가 적용 한 모든 지점 및 사이트 설정 및 IPsec 정책이 표시 됩니다.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="P2S 로그 예제.":::

또한 클라이언트가 IKEv2 또는 OpenVPN 지점을 통해 사이트에 연결할 때마다이 테이블은 패킷 작업, EAP/RADIUS 대화 및 사용자에의 한 성공/실패 결과를 기록 합니다.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="EAP 로그 예제.":::

## <a name="next-steps"></a>다음 단계

터널 리소스 로그에 대 한 경고를 구성 하려면 [VPN Gateway 리소스 로그에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)을 참조 하세요.


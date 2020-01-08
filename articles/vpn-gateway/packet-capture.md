---
title: 'Azure VPN Gateway: 패킷 캡처 구성'
description: VPN 게이트웨이에서 사용할 수 있는 패킷 캡처 기능에 대해 알아봅니다.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75353511"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>VPN gateway에 대 한 패킷 캡처 구성

연결 및 성능 관련 문제는 일반적으로 복잡 하며 문제의 원인을 좁히는 데 드는 시간과 노력이 많이 소요 됩니다. 패킷 캡처 기능을 사용 하면 문제 범위를 네트워크의 특정 부분으로 축소 하는 시간을 줄일 수 있습니다. 예를 들어 문제가 네트워크의 고객 측, 네트워크의 Azure 측 또는 그 사이에 있는지 여부 등이 있습니다. 문제를 축소 한 후에는 디버그 하 고 수정 작업을 수행 하는 것이 훨씬 더 효율적입니다.

패킷 캡처에는 일반적으로 사용할 수 있는 몇 가지 도구가 있습니다. 그러나 이러한 도구를 사용 하 여 관련 패킷 캡처를 가져오는 것은 일반적으로 많은 볼륨 트래픽 시나리오에서 작업 하는 경우 다소 번거로울 수 있습니다. VPN gateway 패킷 캡처에서 제공 하는 필터링 기능은 중요 한 차이점입니다. 일반적으로 사용 가능한 패킷 캡처 도구 외에도 VPN gateway 패킷 캡처를 사용할 수 있습니다.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN gateway 패킷 캡처 필터링 기능

VPN gateway 패킷 캡처는 고객의 요구에 따라 게이트웨이에서 또는 특정 연결에서 실행할 수 있습니다. 동시에 여러 터널에서 패킷 캡처를 실행할 수도 있습니다. VPN 게이트웨이에서 필터링과 함께 단일 또는 양방향 트래픽, IKE 및 ESP 트래픽 및 내부 패킷을 캡처할 수 있습니다.

5 개 튜플 필터 (원본 서브넷, 대상 서브넷, 원본 포트, 대상 포트, 프로토콜) 및 TCP 플래그 (SYN, ACK, FIN, URG, PSH, RST)를 사용 하는 것은 대용량 트래픽에 대 한 문제를 격리 하는 데 유용 합니다.

패킷 캡처를 실행 하는 동안 속성 마다 하나의 옵션만 사용할 수 있습니다.

## <a name="setup-packet-capture-using-powershell"></a>PowerShell을 사용 하 여 패킷 캡처 설정

패킷 캡처를 시작 하 고 중지 하는 PowerShell 명령은 아래 예제를 참조 하세요. 매개 변수 옵션 (예: 필터를 만드는 방법)에 대 한 자세한 내용은이 PowerShell [문서](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)를 참조 하세요.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN gateway에 대 한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

선택적 매개 변수 **-filterdata** 는 필터를 적용 하는 데 사용할 수 있습니다.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN gateway에 대 한 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN gateway 연결에 대 한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

선택적 매개 변수 **-filterdata** 는 필터를 적용 하는 데 사용할 수 있습니다.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN gateway 연결에서 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>주요 고려 사항

- 패킷 캡처 실행은 성능에 영향을 줄 수 있습니다. 필요 하지 않은 경우 패킷 캡처를 중지 해야 합니다.
- 제안 된 최소 패킷 캡처 기간은 600 초입니다. 패킷 캡처 기간이 짧으면 경로에 있는 여러 구성 요소 간의 문제를 동기화 하기 때문에 전체 데이터를 제공 하지 못할 수 있습니다.
- 패킷 캡처 데이터 파일은 PCAP 형식으로 생성 됩니다. Wireshark 또는 기타 일반적으로 사용할 수 있는 응용 프로그램을 사용 하 여 PCAP 파일을 엽니다.

## <a name="next-steps"></a>다음 단계

VPN Gateway에 대 한 자세한 내용은 정보 [VPN Gateway](vpn-gateway-about-vpngateways.md) 를 참조 하세요.

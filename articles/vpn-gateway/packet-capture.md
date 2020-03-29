---
title: 'Azure VPN 게이트웨이: 패킷 캡처 구성'
description: VPN 게이트웨이에서 사용할 수 있는 패킷 캡처 기능에 대해 알아봅니다.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353511"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>VPN 게이트웨이용 패킷 캡처 구성

연결 및 성능 관련 문제는 종종 복잡하며 문제의 원인을 좁히기 위해 상당한 시간과 노력이 소요됩니다. 패킷 캡처 기능은 문제가 네트워크의 고객 측인지, 네트워크의 Azure 측인지 또는 그 사이에 있는지 여부와 같이 문제의 범위를 네트워크의 특정 부분으로 좁히는 데 시간을 크게 줄여줍니다. 문제가 좁혀지면 디버깅하고 수정 조치를 취하는 것이 훨씬 더 효율적입니다.

패킷 캡처에 일반적으로 사용할 수 있는 몇 가지 도구가 있습니다. 그러나 이러한 도구를 사용하여 관련 패킷 캡처를 가져오는 것은 특히 대량 트래픽 시나리오로 작업할 때 번거로운 경우가 많습니다. VPN 게이트웨이 패킷 캡처에서 제공하는 필터링 기능은 주요 차별화 사항이 됩니다. 일반적으로 사용 가능한 패킷 캡처 도구 외에도 VPN 게이트웨이 패킷 캡처를 사용할 수 있습니다.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 게이트웨이 패킷 캡처 필터링 기능

VPN 게이트웨이 패킷 캡처는 게이트웨이또는 고객의 요구에 따라 특정 연결에서 실행할 수 있습니다. 동시에 여러 터널에서 패킷 캡처를 실행할 수도 있습니다. 단일 또는 양방향 트래픽, IKE 및 ESP 트래픽, 내부 패킷을 VPN 게이트웨이에서 필터링하는 데 캡처할 수 있습니다.

5개의 투플 필터(소스 서브넷, 대상 서브넷, 소스 포트, 대상 포트, 프로토콜) 및 TCP 플래그(SYN, ACK, FIN, URG, PSH, RST)를 사용하면 대량 트래픽에서 문제를 격리할 때 유용합니다.

패킷 캡처를 실행하는 동안 속성당 하나의 옵션만 사용할 수 있습니다.

## <a name="setup-packet-capture-using-powershell"></a>PowerShell을 사용한 설정 패킷 캡처

패킷 캡처를 시작하고 중지하려면 PowerShell 명령이 보려면 아래 예제를 참조하십시오. 매개 변수 옵션(예: 필터 작성 방법)에 대한 자세한 내용은 이 PowerShell [문서를](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)참조하십시오.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN 게이트웨이의 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

선택적 매개 변수 **-FilterData** 필터를 적용 하는 데 사용할 수 있습니다.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN 게이트웨이의 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN 게이트웨이 연결을 위한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

선택적 매개 변수 **-FilterData** 필터를 적용 하는 데 사용할 수 있습니다.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN 게이트웨이 연결에서 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>주요 고려 사항

- 패킷 캡처를 실행하는 것은 성능에 영향을 줄 수 있습니다. 필요하지 않을 때는 패킷 캡처를 중지해야 합니다.
- 권장되는 최소 패킷 캡처 시간은 600초입니다. 패킷 캡처 기간이 짧아지면 경로의 여러 구성 요소 간에 문제가 동기화되어 전체 데이터가 제공되지 않을 수 있습니다.
- 패킷 캡처 데이터 파일은 PCAP 형식으로 생성됩니다. Wireshark 또는 일반적으로 사용 가능한 다른 응용 프로그램을 사용하여 PCAP 파일을 엽니다.

## <a name="next-steps"></a>다음 단계

VPN 게이트웨이에 대한 자세한 내용은 [VPN 게이트웨이 정보를](vpn-gateway-about-vpngateways.md) 참조하십시오.

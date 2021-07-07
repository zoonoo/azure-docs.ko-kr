---
title: 조직의 Azure Communication Services 네트워크 준비
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 음성 및 화상 통화에 대한 네트워크 요구 사항에 대해 알아봅니다.
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 377d6783fb73c2d03f7e4dde7b352abe1eaef993
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110802"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Azure Communication Services에서 고품질 미디어 보장

이 문서에서는 Azure Communication Services를 사용하여 고품질 멀티미디어 통신 환경을 구축할 때 고려해야 하는 요소 및 모범 사례에 대해 간략히 설명합니다.

## <a name="factors-that-affect-media-quality-and-reliability"></a>미디어 품질 및 안정성에 영향을 주는 요소

Azure Communication Services 실시간 미디어(오디오, 비디오 및 애플리케이션 공유) 품질에 영향을 주는 여러 가지 요소가 있습니다. 여기에는 네트워크 품질, 대역폭, 방화벽, 호스트 및 디바이스 구성이 포함됩니다.


### <a name="network-quality"></a>네트워크 품질

IP를 통한 실시간 미디어의 품질은 기본 네트워크 연결 품질의 영향, 특히 다음 용량의 영향을 크게 받습니다.
* **대기 시간**. IP 패킷을 네트워크의 A 지점에서 B 지점으로 가져오는 데 걸리는 시간입니다. 이 네트워크 전파 지연은 두 지점 사이의 실제 거리와 트래픽이 통과하는 디바이스에서 발생하는 추가 오버헤드에 따라 결정됩니다. 대기 시간은 단방향 또는 RTT(왕복 시간)로 측정됩니다.
* **패킷 손실**. 지정된 시간 동안 손실된 패킷의 백분율입니다. 패킷 손실은 거의 영향을 주지 않는 작은 개별 손실 패킷에서 오디오를 완전히 차단하는 연속 버스트 손실에 이르기까지 오디오 품질에 직접적인 영향을 줍니다.
* **패킷 간 도착 지터 또는 단순히 지터**. 연속 패킷 간의 지연 변화에 대한 평균입니다. Azure Communication Services는 버퍼링을 통해 특정 수준의 지터에 맞게 조정할 수 있습니다. 지터가 버퍼링을 초과하는 경우에만 참가자가 효과를 확인할 수 있습니다.

### <a name="network-bandwidth"></a>네트워크 대역폭

네트워크가 동시 Azure Communication Services 미디어 세션 및 기타 비즈니스 애플리케이션에 필요한 대역폭을 지원하도록 구성되었는지 확인합니다. 멀티미디어 Communication Services 솔루션을 성공적으로 배포하려면 엔드투엔드 네트워크 경로에 대역폭 병목 현상이 발생하는지 테스트하는 것이 매우 중요합니다.

JavaScript SDK의 대역폭 요구 사항은 다음과 같습니다.

|대역폭|시나리오|
|:--|:--|
|40kbps|피어 투 피어 오디오 통화|
|500kbps|피어 투 피어 오디오 통화 및 화면 공유|
|500kbps|피어 투 피어 품질 화상 통화(360p, 30fps)|
|1.2Mbps|피어 투 피어 HD 품질 화상 통화(HD 720p 해상도, 30fps)|
|500kbps|그룹 화상 통화(360p, 30fps)|
|1.2Mbps|HD 그룹 화상 통화(HD 720p 해상도, 30fps)| 

네이티브 Windows, Android 및 iOS SDK의 대역폭 요구 사항은 다음과 같습니다.

|대역폭|시나리오|
|:--|:--|
|30kbps|피어 투 피어 오디오 통화 |
|130kbps|피어 투 피어 오디오 통화 및 화면 공유|
|500kbps|피어 투 피어 품질 화상 통화(360p, 30fps)|
|1.2Mbps|피어 투 피어 HD 품질 화상 통화(HD 720p 해상도, 30fps)|
|1.5Mbps|피어 투 피어 HD 품질 화상 통화(HD 1080p 해상도, 30fps) |
|500kbps/1Mbps|그룹 화상 통화|
|1Mbps/2Mbps|HD 그룹 화상 통화(1080p 화면의 540p 비디오)|

### <a name="firewalls-configuration"></a>방화벽 구성

Azure Communication Services 연결은 고품질 멀티미디어 환경을 제공하기 위해 특정 포트 및 IP 주소에 대한 인터넷 연결이 필요합니다. 이러한 포트 및 IP 주소에 액세스할 수 없는 경우에도 Azure Communication Services가 계속 작동할 수 있습니다. 그러나 권장되는 포트 및 IP 범위가 열려 있으면 최적의 환경이 제공됩니다.

| 범주 | IP 범위 또는 FQDN | 포트 | 
| :-- | :-- | :-- |
| 미디어 트래픽 | [Azure 퍼블릭 클라우드 IP 주소 범위](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478~3481, TCP 포트 443 |
| 신호 보내기, 원격 분석, 등록| *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>네트워크 최적화

다음 작업은 선택 사항이며, Azure Communication Services를 롤아웃하는 데 필요하지 않습니다. 네트워크 및 Azure Communication Services 성능을 최적화하거나 네트워크 제한 사항이 있다고 알고 있는 경우 이 지침을 사용합니다.
추가로 최적화할 수 있는 경우는 다음과 같습니다.
* Azure Communication Services가 느리게 실행됩니다(대역폭이 부족할 수 있음).
* 통화가 계속 끊깁니다(방화벽 또는 프록시 차단기 때문일 수 있음).
* 통화가 멈추거나 끊기거나 로봇과 같은 음성이 들립니다(지터 또는 패킷 손실일 수 있음).

| 네트워크 최적화 작업 | 세부 정보 |
| :-- | :-- |
| 네트워크 계획 | 이 설명서에서는 네트워크에 대한 최소 통화 요구 사항을 확인할 수 있습니다. [네트워크 계획에 대한 Teams 예제](/microsoftteams/tutorial-network-planner-example)를 참조하세요. |
| 외부 이름 확인 | Azure Communication Services SDK를 실행하는 모든 컴퓨터에서 외부 DNS 쿼리를 확인하여 Azure Communication Services에서 제공하는 서비스를 검색할 수 있고 방화벽이 액세스를 차단하지 않는지 확인합니다. SDK에서 *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io 주소를 확인할 수 있는지 확인하세요.  |
| 세션 지속성 유지 관리 | 방화벽이 UDP에 대해 매핑된 NAT(Network Address Translation) 주소 또는 포트를 변경하지 않는지 확인합니다.
NAT 풀 크기 확인 | 사용자 연결에 필요한 NAT(Network Address Translation) 풀 크기를 확인합니다. 여러 사용자 및 디바이스에서 [NAT(Network Address Translation) 또는 PAT(Port Address Translation)](/office365/enterprise/nat-support-with-office-365)를 사용하여 Azure Communication Services에 액세스하는 경우 공개적으로 라우팅할 수 있는 각 IP 주소 뒤에 숨겨진 디바이스가 지원되는 수를 초과하지 않는지 확인합니다. 포트 소모를 방지하기 위해 적절한 공용 IP 주소가 NAT 풀에 할당되었는지 확인합니다. 포트가 소모되면 내부 사용자 및 디바이스에서 Azure Communication Services에 연결할 수 없게 됩니다. |
| 침입 탐지 및 방지 지침 | 아웃바운드 연결에 대한 추가 보안 계층을 위해 배포된 IDS/IPS([침입 탐지](../../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md) 또는 방지 시스템)가 환경에 있는 경우 모든 Azure Communication Services URL을 허용합니다. |
| 분할 터널 VPN 구성 | 일반적으로 [분할 터널 VPN](/windows/security/identity-protection/vpn/vpn-routing)이라고 하는 VPN(가상 사설망)을 무시하는 Teams 트래픽에 대한 대체 경로를 제공하는 것이 좋습니다. 분할 터널링은 Azure Communications Services에 대한 트래픽이 VPN을 통과하지 않고 Azure로 직접 이동한다는 것을 의미합니다. VPN을 무시하면 미디어 품질에 긍정적인 영향을 주며 VPN 디바이스 및 조직의 네트워크에서 발생하는 부하가 줄어듭니다. 분할 터널 VPN을 구현하려면 VPN 공급업체와 협력하세요. VPN을 무시하도록 권장되는 다른 이유는 다음과 같습니다. <ul><li> VPN은 일반적으로 실시간 미디어를 지원하도록 설계되거나 구성되지 않습니다.</li><li> VPN은 UDP(Azure Communication Services에 필요함)를 지원하지 않을 수도 있습니다.</li><li>VPN은 이미 암호화된 미디어 트래픽 위에 추가 암호화 계층도 도입합니다.</li><li>Azure Communication Services에 대한 연결은 VPN 디바이스를 통한 트래픽 hair-pinning으로 인해 효율적이지 않을 수 있습니다.</li></ul>|
| QoS 구현 | [QoS(서비스 품질)를 사용](/microsoftteams/qos-in-teams)하여 패킷 우선 순위를 구성합니다. 이렇게 하면 통화 품질이 향상되고, 통화 품질을 모니터링하고 문제를 해결하는 데 도움이 됩니다. QoS는 관리되는 네트워크의 모든 세그먼트에서 구현해야 합니다. 네트워크에서 대역폭을 적절히 프로비저닝한 경우에도 QoS는 예기치 않은 네트워크 이벤트 발생 시 위험 완화를 제공합니다. QoS를 사용하면 이러한 예기치 않은 이벤트가 품질에 부정적인 영향을 주지 않도록 음성 트래픽의 우선 순위가 지정됩니다. | 
| WiFi 최적화 | VPN과 마찬가지로 WiFi 네트워크는 반드시 실시간 미디어를 지원하도록 설계되거나 구성되지는 않습니다. Azure Communication Services를 지원하기 위해 WiFi 네트워크를 계획하거나 최적화하는 것은 고품질 배포에 대한 중요한 고려 사항입니다. 다음 항목을 고려합니다. <ul><li>QoS 또는 WMM(WiFi 멀티미디어)을 구현하여 미디어 트래픽이 WiFi 네트워크에서 적절하게 우선 순위가 지정되도록 합니다.</li><li>WiFi 밴드 및 액세스 지점 배치를 계획하고 최적화합니다. 2\.4GHz 범위는 액세스 지점 배치에 따라 적절한 환경을 제공할 수 있지만, 액세스 지점은 해당 범위에서 작동하는 다른 소비자 디바이스의 영향을 받는 경우가 많습니다. 5GHz 범위는 밀도가 높은 범위로 인해 실시간 미디어에 더 적합하지만 충분한 적용 범위를 얻으려면 더 많은 액세스 지점이 필요합니다. 또한 엔드포인트는 해당 범위를 지원하고 이에 따라 해당 밴드를 활용하도록 구성해야 합니다.</li><li>이중 밴드 WiFi 네트워크를 사용하는 경우 밴드 조종을 구현하는 것이 좋습니다. 밴드 조종은 5GHz 범위를 사용하도록 이중 밴드 클라이언트에 영향을 주기 위해 WiFi 공급업체에서 구현하는 기술입니다.</li><li>동일한 채널의 액세스 지점이 너무 가까이 있으면 신호가 겹치고 의도하지 않은 경합이 발생하므로 사용자 환경이 저하될 수 있습니다. 서로 옆에 있는 액세스 지점이 겹치지 않는 채널에 있는지 확인합니다.</li></ul> 각 무선 공급업체에는 자체 무선 솔루션 배포에 대한 고유한 권장 사항이 있습니다. 특정 지침은 WiFi 공급업체에 문의하세요.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>운영 체제 및 브라우저(JavaScript SDK의 경우)

Azure Communication Services 음성/비디오 SDK는 특정 운영 체제 및 브라우저를 지원합니다.
[통화 개념 설명서](./calling-sdk-features.md)에서 통화 SDK에서 지원하는 운영 체제 및 브라우저에 대해 알아보세요.

## <a name="next-steps"></a>다음 단계

다음 문서는 사용자에게 유용할 수 있습니다.

- [통화 라이브러리](./calling-sdk-features.md)에 대한 자세한 정보
- [클라이언트-서버 아키텍처](../client-and-server-architecture.md)에 대한 자세한 정보
- [호출 흐름 토폴로지](../call-flows.md)에 대해 알아보기
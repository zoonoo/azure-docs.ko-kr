---
title: 'Azure ExpressRoute: 비대칭 라우팅'
description: 이 문서에서는 대상에 여러 개의 링크가 있는 네트워크에서 비대칭 라우팅을 사용하여 발생할 수 있는 문제를 살펴봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97560511"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>여러 네트워크 경로를 포함한 비대칭 라우팅
이 문서에서는 네트워크 원본과 대상 간에 여러 경로를 사용할 수 있을 때 네트워크 트래픽에서 여러 경로를 사용하는 방법을 설명합니다.

비대칭 라우팅을 이해하기 위해 알아야 할 두 가지 개념이 있습니다. 첫 번째는 여러 네트워크 경로의 영향입니다. 두 번째는 방화벽 같은 디바이스가 상태를 유지하는 방법입니다. 이러한 종류의 디바이스는 상태 저장 디바이스라고 합니다. 두 요소를 결합하면 상태 저장 디바이스에서 네트워크 트래픽이 중단되는 시나리오를 만들 수 있습니다.  트래픽이 디바이스에서 발생한 것을 감지하지 못했으므로 트래픽이 중단됩니다.

## <a name="multiple-network-paths"></a>여러 네트워크 경로
엔터프라이즈 네트워크가 해당 인터넷 서비스 공급자를 통해 인터넷에 대한 하나의 링크를 가진 경우 동일한 경로를 통해 인터넷 내외에서 모든 트래픽이 이동합니다. 회사는 네트워크 작동 시간을 개선하기 위해 중복 경로로 여러 회로를 구입하는 경우가 많습니다. 해당 유형의 구성을 통해 트래픽이 하나의 링크로 나가 인터넷으로 이동하고 다른 링크로 반환될 수 있습니다. 해당 시나리오를 보통 비대칭 라우팅이라고 합니다. 비대칭 라우팅에서 반환 네트워크 트래픽은 외부로 나가는 원래 흐름과 다른 경로를 사용합니다.

![여러 경로가 포함된 네트워크](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

비대칭 라우팅은 일반적으로 인터넷으로 가는 경우에 발생합니다. 여러 경로의 조합이 사용될 때도 발생합니다. 첫 번째 예는 인터넷 경로와 동일한 대상으로 가는 프라이빗 경로가 있는 경우입니다. 두 번째 예는 동일한 대상으로도 가는 여러 프라이빗 경로가 있는 경우입니다.

소스와 대상 간의 경로를 따라 각 라우터는 대상에 도달하는 데 가장 적합한 경로를 컴퓨팅합니다. 라우터는 두 가지 주요 요소를 기반으로 최적의 경로를 결정합니다.

* 외부 네트워크 간의 라우팅은 BGP(경계 게이트웨이 프로토콜), 라우팅 프로토콜을 기반으로 합니다. BGP는 인접 라우터에서 보급하고 의도한 대상에 최상의 경로를 결정하는 여러 단계를 통해 실행합니다. 최상의 경로를 해당 라우팅 테이블에 저장합니다.
* 경로와 연결된 서브넷 마스크의 길이는 라우팅 경로에 영향을 줍니다. 라우터가 동일한 IP 주소에 대한 여러 보급 알림을 수신하는 경우, 서브넷 마스크가 길수록 더 구체적인 경로로 간주되기 때문에 서브넷 마스크가 긴 경로를 선택합니다.

## <a name="stateful-devices"></a>상태 저장 디바이스
라우터는 라우팅을 위해 패킷의 IP 헤더를 살펴봅니다. 패킷 내부를 훨씬 자세히 살펴보는 디바이스가 있습니다. 일반적으로 해당 디바이스는 Layer 4 - TCP(Transmission Control Protocol), UDP(User Datagram Protocol) 또는 심지어 Layer 7(애플리케이션 계층) 헤더를 확인합니다. 이러한 종류의 디바이스는 보안 디바이스 또는 대역폭 최적화 디바이스입니다. 

방화벽은 상태 저장 디바이스의 일반적인 예입니다. 방화벽은 다양한 조건에 따라 해당 인터페이스를 통과하는 패킷을 허용하거나 거부합니다. 조건은 프로토콜, TCP/UDP 포트, URL 헤더가 포함되지만 이에 국한되지는 않습니다. 이 수준의 패킷 검사는 디바이스에 많은 처리 부하를 배치할 수 있습니다. 

방화벽은 성능을 개선하기 위해 흐름의 첫 번째 패킷을 검사합니다. 패킷이 인터페이스를 통화하도록 허용하는 경우 해당 상태 테이블에 흐름 정보를 보관합니다. 그리고 이 흐름에 관련된 모든 후속 패킷은 초기 결정에 따라 허용됩니다. 기존 흐름에 속하는 패킷은 처음에 출발하지 않은 방화벽에 도착할 수 있습니다. 초기 흐름에 대한 이전 상태 정보가 없으므로 방화벽은 패킷을 중단합니다.

## <a name="asymmetric-routing-with-expressroute"></a>ExpressRoute를 포함한 비대칭 라우팅
ExpressRoute를 통해 Microsoft에 연결할 때 네트워크에는 다음과 같은 변경 내용이 발생합니다.

* Microsoft에 대한 여러 링크가 있습니다. 한 가지 링크는 기존 인터넷 연결이고 다른 하나는 ExpressRoute를 통한 연결입니다. Microsoft를 향한 특정 트래픽은 인터넷 연결을 통과할 수 있지만 ExpressRoute 연결을 통해 반환될 수 있습니다. 트래픽이 ExpressRoute를 통과하지만 인터넷 경로를 통해 반환되는 경우에도 마찬가지입니다.
* ExpressRoute 회로에서 더 많은 특정 IP 주소가 수신되었습니다. 따라서 ExpressRoute를 통해 제공되는 서비스를 위해 네트워크의 트래픽이 Microsoft로 이동하는 경우 라우터는 항상 ExpressRoute 연결을 선호합니다.

두 가지 변경 사항이 네트워크에 미치는 영향을 이해하기 위해 몇 가지 시나리오를 고려해 보겠습니다. 예를 들어 인터넷에 대한 회로가 하나이며 인터넷을 통한 모든 Microsoft 서비스를 사용합니다. 네트워크와 Microsoft를 오가는 트래픽은 동일한 인터넷 링크를 탐색하고 방화벽을 통해 전달됩니다. 방화벽은 첫 번째 패킷이 감지되면 흐름을 기록합니다. 흐름이 상태 테이블에 있기 때문에 해당 대화의 모든 후속 패킷이 허용됩니다.

![ExpressRoute를 포함한 비대칭 라우팅](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

그런 다음, ExpressRoute 회로를 활성화하여 ExpressRoute를 통해 Microsoft에서 제공하는 서비스를 사용합니다. Microsoft의 다른 모든 서비스는 인터넷을 통해 사용됩니다. ExpressRoute에 연결된 에지에 별도 방화벽을 배포합니다. Microsoft는 특정 서비스에 대한 ExpressRoute에 네트워크에 대한 구체적인 접두사를 보급합니다. 라우팅 인프라는 해당 접두사에 대한 기본 경로로 ExpressRoute를 선택합니다. 

ExpressRoute를 통해 Microsoft에 공용 IP 주소를 보급하지 않는 경우 Microsoft는 인터넷을 통해 공용 IP 주소와 통신합니다. 네트워크에서 Microsoft로 전송되는 트래픽은 ExpressRoute 연결을 사용하지만 Microsoft의 반환 트래픽은 인터넷 경로를 사용합니다. 에지에 있는 방화벽에서 알지 못하는 흐름에 대한 응답 패킷이 감지되는 경우 해당 패킷은 중단됩니다.

ExpressRoute 및 인터넷에 대해 동일한 NAT(Network Address Translation) 풀을 보급하도록 선택하는 경우 개인 IP 주소에서 네트워크의 클라이언트와 유사한 문제가 발생됩니다. 해당 서비스에 대한 IP 주소가 ExpressRoute를 통해 보급되지 않기 때문에 Windows 업데이트와 같은 서비스에 대한 요청은 인터넷을 통해 이동합니다. 그러나 반환 트래픽은 ExpressRoute를 통해 돌아옵니다. Microsoft가 인터넷 및 ExpressRoute에서 서브넷 마스크가 동일한 IP 주소를 받았기 때문에, 기본 경로는 항상 ExpressRoute입니다. ExpressRoute에 연결되는 네트워크 에지의 방화벽 또는 다른 상태 저장 디바이스에 흐름에 대한 이전 정보가 없으면 해당 패킷이 중단됩니다.

## <a name="asymmetric-routing-solutions"></a>비대칭 라우팅 솔루션
비대칭 라우팅의 문제를 해결하는 데 사용할 수 있는 두 가지 옵션이 있습니다. 첫 번째는 라우팅을 통한 방법이고 두 번째는 SNAT(소스 기반 NAT)를 사용하는 것입니다.

### <a name="routing"></a>라우팅
공용 IP 주소를 적절한 WAN(광역 네트워크) 링크에 보급하도록 합니다. 예를 들어 인증 트래픽에 인터넷을 사용하고 메일 트래픽에 ExpressRoute를 사용하려는 경우입니다. ExpressRoute를 통해 Active Directory Federation Services(AD FS) 공용 IP 주소를 보급하지 마세요. 또한 라우터가 ExpressRoute로 수신하는 IP 주소에 온-프레미스 AD FS 서버를 노출하지 않아야 합니다. ExpressRoute를 통해 수신된 경로는 구체적이므로 ExpressRoute를 Microsoft에 대한 인증 트래픽의 기본 경로로 만듭니다. 네트워크 비대칭 라우팅 문제에서 라우팅이 수행되는 방식에 주의하지 않는 경우 문제가 발생할 수 있습니다.

인증을 위해 ExpressRoute를 사용하려는 경우 NAT 없이 ExpressRoute에 AD FS 공용 IP 주소를 보급해야 합니다. 해당 방식으로 구성된 경우 Microsoft에서 시작되어 온-프레미스 AD FS 서버로 이동하는 트래픽이 ExpressRoute를 통해 이동합니다. 네트워크에서 Microsoft로 가는 반환 트래픽은 인터넷을 통해 선호되는 경로인 ExpressRoute를 사용합니다.

### <a name="source-based-nat"></a>원본 기반 NAT
비대칭 라우팅 문제를 해결하는 또 다른 방법은 SNAT를 사용하는 것입니다. 예를 들어 ExpressRoute를 통해 온-프레미스 SMTP(Simple Mail Transfer Protocol) 서버의 공용 IP 주소를 보급하지 않도록 선택합니다. 대신 해당 유형의 통신에 인터넷을 사용하려고 합니다. Microsoft에서 시작되어 온-프레미스 SMTP 서버로 이동하는 요청은 인터넷을 통해 이동합니다. SNAT는 내부 IP 주소에 대한 들어오는 요청입니다. SMTP 서버의 반환 트래픽은 ExpressRoute 대신 경계면 방화벽(NAT에 사용함)으로 이동합니다. 따라서 반환 트래픽은 인터넷 경로를 취합니다.

![원본 기반 NAT 네트워크 구성](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>비대칭 라우팅 감지
경로 추적은 네트워크 트래픽이 예상된 경로를 탐색하도록 하는 가장 좋은 방법입니다. 온-프레미스 SMTP 서버에서 Microsoft로 전달되는 트래픽이 인터넷 경로를 사용해야 하는 경우 예상된 경로 추적은 SMTP 서버에서 Microsoft 365로 이동합니다. 결과를 통해 트래픽이 네트워크를 ExpressRoute가 아닌 인터넷에 남겨두는지 유효성을 검사합니다.


---
title: 비대칭 라우팅 - Azure ExpressRoute | Microsoft Docs
description: 이 문서에서는 대상에 여러 개의 링크가 있는 네트워크에서 비대칭 라우팅을 사용하여 발생할 수 있는 문제를 살펴봅니다.
documentationcenter: na
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: osamam
ms.custom: seodec18
ms.openlocfilehash: 2b2b678cad50e45660fb763c2a1f9194500edf8d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730196"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>여러 네트워크 경로를 포함한 비대칭 라우팅
이 문서에서는 네트워크 원본과 대상 간에 여러 경로를 사용할 수 있을 때 전달 및 반환 네트워크 트래픽에서 다른 경로를 사용하는 방법을 설명합니다.

비대칭 라우팅을 이해하려면 두 가지 개념을 이해해야 합니다. 하나는 여러 네트워크 경로의 영향입니다. 다른 하나는 방화벽 같은 디바이스가 상태를 유지하는 방법입니다. 이러한 종류의 디바이스는 상태 저장 디바이스라고 합니다. 이러한 두 가지 요인을 조합하면 상태 저장 디바이스가 디바이스 자체로 시작된 트래픽을 감지하지 못하기 때문에 상태 저장 디바이스에서 네트워크 트래픽을 삭제하는 시나리오를 만듭니다.

## <a name="multiple-network-paths"></a>여러 네트워크 경로
엔터프라이즈 네트워크가 해당 인터넷 서비스 공급자를 통해 인터넷에 대한 하나의 링크를 가진 경우 동일한 경로를 통해 인터넷 내외에서 모든 트래픽이 이동합니다. 종종 회사는 네트워크 가동 시간을 개선하기 위해 중복 경로로 여러 회로를 구입합니다. 이러한 경우 네트워크 외부에서 들어오는 트래픽은 하나의 링크를 통해 인터넷으로 이동하고 반환 트래픽은 다른 링크를 통해 이동합니다. 이를 일반적으로 비대칭 라우팅이라고 합니다. 비대칭 라우팅에서 역방향 네트워크 트래픽은 원래 흐름과 다른 경로를 사용합니다.

![여러 경로가 포함된 네트워크](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

인터넷에서 주로 발생하지만 비대칭 라우팅도 다른 조합의 여러 경로에 적용됩니다. 예를 들어 동일한 대상으로 이동하는 인터넷 경로 및 프라이빗 경로와 동일한 대상으로 이동하는 여러 개인 경로에 모두 적용됩니다.

원본에서 대상까지의 과정에서 라우터는 각각 대상에 도달하는 최상의 경로를 계산합니다. 라우터는 두 가지 주요 요소를 기반으로 가능한 최상의 경로를 결정합니다.

* 외부 네트워크 간의 라우팅은 BGP(경계 게이트웨이 프로토콜), 라우팅 프로토콜을 기반으로 합니다. BGP는 인접 라우터에서 보급하고 의도한 대상에 최상의 경로를 결정하는 여러 단계를 통해 실행합니다. 최상의 경로를 해당 라우팅 테이블에 저장합니다.
* 경로와 연결된 서브넷 마스크의 길이는 라우팅 경로에 영향을 줍니다. 라우터가 다른 서브넷 마스크가 아닌 동일한 IP 주소에 대한 여러 보급을 수신하는 경우 해당 라우터는 더 긴 서브넷 마스크를 포함한 보급을 구체적인 경로로 간주하기 때문에 선호합니다.

## <a name="stateful-devices"></a>상태 저장 디바이스
라우터는 라우팅을 위해 패킷의 IP 헤더를 살펴봅니다. 패킷 내부를 훨씬 자세히 살펴보는 디바이스가 있습니다. 일반적으로 이러한 장치는 Layer4(전송 제어 프로토콜 또는 TCP; 또는 사용자 데이터그램 프로토콜 또는 UDP) 또는 심지어 Layer7(애플리케이션 계층) 헤더를 확인합니다. 이러한 종류의 디바이스는 보안 디바이스 또는 대역폭 최적화 디바이스입니다. 

방화벽은 상태 저장 디바이스의 일반적인 예입니다. 방화벽은 패킷이 프로토콜, TCP/UDP 포트, URL 헤더 등 다양한 필드에 따라 해당 인터페이스를 통과하도록 허용하거나 거부합니다. 이 수준의 패킷 검사는 디바이스에 많은 처리 부하를 배치합니다. 방화벽은 성능을 개선하기 위해 흐름의 첫 번째 패킷을 검사합니다. 패킷이 진행되도록 허용하는 경우 해당 상태 테이블에 흐름 정보를 보관합니다. 이 흐름에 관련된 모든 후속 패킷은 초기 결정에 따라 허용됩니다. 기존 흐름의 일부인 패킷은 방화벽에 도달할 수 있습니다. 방화벽에 이전 상태 정보가 없는 경우 방화벽은 패킷을 삭제합니다.

## <a name="asymmetric-routing-with-expressroute"></a>ExpressRoute를 포함한 비대칭 라우팅
ExpressRoute를 통해 Microsoft에 연결할 때 네트워크에는 다음과 같은 변경 내용이 발생합니다.

* Microsoft에 대한 여러 링크가 있습니다. 한 가지 링크는 기존 인터넷 연결이고 다른 하나는 ExpressRoute를 통한 연결입니다. Microsoft에 대한 일부 트래픽은 인터넷을 통과할 수 있지만 ExpressRoute를 통해 돌아옵니다. 혹은 그 반대입니다.
* ExpressRoute를 통해 구체적인 IP 주소를 수신합니다. 따라서 항상 ExpressRoute를 통해 제공되는 서비스에 대해 네트워크에서 Microsoft로 전달되는 트래픽의 경우 라우터는 ExpressRoute를 선호합니다.

이러한 두 가지 변경 사항이 네트워크에 미칠 영향을 이해하기 위해 몇 가지 시나리오를 고려해 보겠습니다. 예를 들어 인터넷에 대한 회로가 오직 하나이며 인터넷을 통한 모든 Microsoft 서비스를 사용합니다. 네트워크에서 Microsoft로 전달되는 트래픽은 동일한 인터넷 링크를 탐색하고 방화벽을 통해 전달됩니다. 첫 번째 패킷을 확인하면 방화벽이 흐름을 기록하고 흐름이 상태 테이블에 존재하면 반환 패킷이 허용됩니다.

![ExpressRoute를 포함한 비대칭 라우팅](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

그런 다음, ExpressRoute를 켜고 ExpressRoute를 통해 Microsoft에서 제공하는 서비스를 사용합니다. Microsoft의 다른 모든 서비스는 인터넷을 통해 사용됩니다. ExpressRoute에 연결된 에지에 별도 방화벽을 배포합니다. Microsoft는 특정 서비스에 대한 ExpressRoute에 네트워크에 대한 구체적인 접두사를 보급합니다. 라우팅 인프라는 해당 접두사에 대한 기본 경로로 ExpressRoute를 선택합니다. ExpressRoute에 걸쳐 Microsoft에 대한 공용 IP 주소를 보급하지 않는 경우 Microsoft는 인터넷을 통해 공용 IP 주소와 통신합니다. 네트워크에서 Microsoft로 전달되는 트래픽은 ExpressRoute를 사용하고 Microsoft의 역방향 트래픽은 인터넷을 사용합니다. 에지의 방화벽이 상태 테이블에서 찾을 수 없는 흐름에 대한 응답 패킷을 확인하는 경우 반환 트래픽을 삭제합니다.

인터넷 및 ExpressRoute에 대 한 동일한 네트워크 주소 변환 (NAT) 풀을 보급 하려는 경우 개인 IP 주소에 네트워크의 클라이언트와 유사한 문제를 표시 됩니다. 이러한 서비스에 대한 IP 주소가 ExpressRoute를 통해 보급되지 않기 때문에 Windows 업데이트와 같은 서비스에 대한 요청은 인터넷을 통해 이동합니다. 그러나 반환 트래픽은 ExpressRoute를 통해 다시 돌아옵니다. Microsoft가 인터넷 및 ExpressRoute에서 동일한 서브넷 마스크를 가진 IP 주소를 수신하는 경우 인터넷에 대한 ExpressRoute를 선호합니다. 네트워크 에지에서 ExpressRoute에 연결된 방화벽이나 기타 상태 저장 디바이스에 흐름에 대한 이전 정보가 없는 경우 해당 흐름에 속하는 패킷을 삭제합니다.

## <a name="asymmetric-routing-solutions"></a>비대칭 라우팅 솔루션
비대칭 라우팅의 문제를 해결하는 두 가지 주요 옵션이 있습니다. 하나는 라우팅을 통한 방법이고 다른 하나는 SNAT(원본 기반 NAT)를 사용하는 것입니다.

### <a name="routing"></a>라우팅
공용 IP 주소를 적절한 WAN(광역 네트워크) 링크에 보급하도록 합니다. 예를 들어 메일 트래픽에 대한 인증 트래픽 및 ExpressRoute의 인터넷을 사용하려는 경우 ExpressRoute를 통해 AD FS(Active Directory Federation Services) 공용 IP 주소를 보급하지 않아야 합니다. 마찬가지로 라우터가 ExpressRoute로 수신하는 IP 주소에 온-프레미스 AD FS 서버를 노출하지 않아야 합니다. ExpressRoute를 통해 수신된 경로는 구체적이므로 ExpressRoute를 Microsoft에 대한 인증 트래픽의 기본 경로로 만듭니다. 이렇게 하면 비대칭 라우팅이 생겨납니다.

인증을 위해 ExpressRoute를 사용하려는 경우 NAT 없이 ExpressRoute에 AD FS 공용 IP 주소를 보급해야 합니다. 이 방법으로 Microsoft에서 시작되고 온-프레미스 AD FS 서버로 이동하는 트래픽은 ExpressRoute를 통해 이동합니다. 고객으로부터 Microsoft에 대한 반환 트래픽은 인터넷을 통한 기본 경로이기 때문에 ExpressRoute를 사용합니다.

### <a name="source-based-nat"></a>원본 기반 NAT
비대칭 라우팅 문제를 해결하는 다른 방법은 SNAT를 사용하는 것입니다. 예를 들어 이러한 유형의 통신에 인터넷을 사용하려고 하기 때문에 ExpressRoute에 온-프레미스 SMTP(단순 메일 전송 프로토콜) 서버의 공용 IP 주소를 보급하지 않습니다. Microsoft로 시작하고 온-프레미스 SMTP 서버로 이동하는 요청은 인터넷을 탐색합니다. SNAT는 내부 IP 주소에 대한 들어오는 요청입니다. SMTP 서버의 역방향 트래픽은 ExpressRoute 대신 에지 방화벽(NAT에 사용함)으로 이동합니다. 반환 트래픽은 인터넷을 통해 돌아갑니다.

![원본 기반 NAT 네트워크 구성](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>비대칭 라우팅 감지
경로 추적은 네트워크 트래픽이 예상된 경로를 탐색하도록 하는 가장 좋은 방법입니다. 온-프레미스 SMTP 서버에서 Microsoft로 전달되는 트래픽이 인터넷 경로를 사용해야 하는 경우 예상된 경로 추적은 SMTP 서버에서 Office 365로 이동합니다. 결과를 통해 트래픽이 네트워크를 ExpressRoute가 아닌 인터넷에 남겨두는지 유효성을 검사합니다.


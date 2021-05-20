---
title: 컨테이너 보안
titleSuffix: Azure Cognitive Services
description: 컨테이너를 보호하는 방법 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 4b106ebc5606c4e5a290e12728d4e2011e80f6a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97861812"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services 컨테이너 보안

애플리케이션을 개발하는 경우 보안이 주요 관심사여야 합니다. 보안의 중요성이 성공의 지표입니다. Cognitive Services 컨테이너를 포함하는 소프트웨어 솔루션을 설계하는 경우 사용할 수 있는 제한 및 기능을 이해하는 것이 중요합니다. 네트워크 보안에 대한 자세한 내용은 [Azure Cognitive Services 가상 네트워크 구성][az-security]을 참조하세요.

> [!IMPORTANT]
> 기본적으로 Cognitive Services 컨테이너 API에는 *보안이 없습니다*. 그 이유는 대부분의 컨테이너가 네트워크 브리지에 의해 외부로부터 보호되는 Pod의 일부로 실행되기 때문입니다. 그러나 [클라우드 기반 Cognitive Services][request-authentication]에 액세스할 때 사용되는 인증과 동일하게 작동하는 인증을 사용하도록 설정할 수 있습니다.

아래 다이어그램은 기본 및 **비보안** 접근 방식을 보여줍니다.

![컨테이너 보안](../media/container-security.svg)

대안적이고 *안전한* 접근 방식으로, Cognitive Services 컨테이너의 소비자는 컨테이너 엔드포인트를 비공개로 유지하면서 전면 구성 요소가 있는 컨테이너를 확장할 수 있습니다. [Istio][istio]를 수신 게이트웨이로 사용하는 시나리오를 고려해 보겠습니다. Istio는 HTTPS/TLS 및 클라이언트 인증서 인증을 지원합니다. 이 시나리오에서 Istio 프런트 엔드는 컨테이너 액세스를 노출하여 Istio로 사전에 승인된 클라이언트 인증서를 표시합니다.

[Nginx][nginx]는 동일한 범주에서 널리 사용되는 다른 옵션입니다. Istio와 Nginx는 모두 서비스 메시 역할을 하며 부하 분산, 라우팅 및 속도 제어와 같은 항목을 포함하여 추가 기능을 제공합니다.

### <a name="container-networking"></a>컨테이너 네트워킹

Cognitive Services 컨테이너는 청구를 위해 계량 정보를 제출하는 데 필요합니다. Cognitive Services 컨테이너에서 사용하는 다양한 네트워크 채널을 나열하지 않으면 컨테이너가 작동하지 않습니다.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Cognitive Services 도메인 및 포트 목록 허용

호스트에서 **443 포트** 및 다음 도메인을 나열하도록 허용해야 합니다.

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>딥 패킷 검사 사용 안 함

[DPI(딥 패킷 검사)](https://en.wikipedia.org/wiki/Deep_packet_inspection)는 컴퓨터 네트워크를 통해 전송되는 데이터를 자세히 검사하고, 이에 따라 일반적으로 차단, 다시 라우팅 또는 로깅하여 작업을 수행하는 데이터 처리 유형입니다.

Cognitive Services 컨테이너가 Microsoft 서버에 만드는 보안 채널에서 DPI를 비활성화합니다. 이렇게 하지 않으면 컨테이너가 제대로 작동하지 않습니다.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md

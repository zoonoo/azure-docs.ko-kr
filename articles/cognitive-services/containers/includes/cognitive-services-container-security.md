---
title: 컨테이너 보안
titleSuffix: Azure Cognitive Services
description: 컨테이너 보안 방법 알아보기
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272648"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 코그너티브 서비스 컨테이너 보안

응용 프로그램을 개발할 때마다 보안이 주요 초점이 되어야 합니다. 보안의 중요성은 성공의 척도입니다. 코그너티브 서비스 컨테이너가 포함된 소프트웨어 솔루션을 설계할 때는 사용 가능한 제한 사항과 기능을 이해하는 것이 중요합니다. 네트워크 보안에 대한 자세한 내용은 [Azure Cognitive Services 가상 네트워크 구성을][az-security]참조하십시오.

> [!IMPORTANT]
> 기본적으로 코그너티브 서비스 컨테이너 API에는 *보안이 없습니다.* 그 이유는 대부분 컨테이너가 네트워크 브리지에 의해 외부에서 보호되는 포드의 일부로 실행됩니다. 그러나 [클라우드 기반 코그너티브 서비스에][request-authentication]액세스할 때 사용되는 인증과 동일하게 작동하는 인증을 활성화할 수 있습니다.

아래 다이어그램은 기본 및 **비보안** 방법을 보여 줍니다.

![컨테이너 보안](../media/container-security.svg)

대안적이고 *안전한* 접근 방식으로 코그너티브 서비스 컨테이너의 소비자는 전면 구성 요소가 있는 컨테이너를 보강하여 컨테이너 끝점을 비공개로 유지할 수 있습니다. [Istio를][istio] 받는 게이트웨이로 사용하는 시나리오를 살펴보겠습니다. Istio는 HTTPS/TLS 및 클라이언트 인증서 인증을 지원합니다. 이 시나리오에서 Istio 프런트 엔드는 컨테이너 액세스를 노출하여 Istio와 함께 미리 화이트리스트에 등록된 클라이언트 인증서를 제공합니다.

[Nginx는][nginx] 같은 카테고리에서 또 다른 인기있는 선택입니다. Istio와 Nginx는 모두 서비스 메시역할을 하며 로드 밸런싱, 라우팅 및 속도 제어와 같은 추가 기능을 제공합니다.

### <a name="container-networking"></a>컨테이너 네트워킹

코그너티브 서비스 컨테이너는 청구 목적으로 계량 정보를 제출해야 합니다. 유일한 예외는 다른 청구 방법을 따르는 *오프라인 컨테이너입니다.* 코그너티브 서비스 컨테이너가 의존하는 다양한 네트워크 채널을 나열하지 않으면 컨테이너가 작동하지 않습니다.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>목록 인지 서비스 도메인 및 포트 허용

호스트는 목록 **포트 443** 및 다음 도메인을 허용해야 합니다.

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>깊은 패킷 검사를 사용하지 않도록 설정

> [딥 패킷](https://en.wikipedia.org/wiki/Deep_packet_inspection) 검사(DPI)는 컴퓨터 네트워크를 통해 전송되는 데이터를 자세히 검사하는 데이터 처리 유형으로, 일반적으로 차단, 재라우팅 또는 그에 따라 로깅하여 조치를 취합니다.

코그너티브 서비스 컨테이너가 Microsoft 서버에 만드는 보안 채널에서 DPI를 사용하지 않도록 설정합니다. 이렇게 하지 않으면 컨테이너가 올바르게 작동하지 않습니다.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md

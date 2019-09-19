---
title: 컨테이너 보안
titleSuffix: Azure Cognitive Services
description: 컨테이너를 보호 하는 방법 알아보기
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994930"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services 컨테이너 보안

응용 프로그램을 개발 하는 경우 보안은 기본 포커스를 두어야 합니다. 보안의 중요도는 성공에 대 한 메트릭입니다. Cognitive Services 컨테이너를 포함 하는 소프트웨어 솔루션을 설계 하는 경우 사용할 수 있는 제한 및 기능을 이해 하는 것이 중요 합니다. 자세한 내용은 [Azure 보안][az-security]을 참조 하세요.

> [!IMPORTANT]
> 기본적으로 Cognitive Services 컨테이너 API에 대 한 *보안은 없습니다* . 그 이유는 컨테이너는 네트워크 브리지가 외부에서 보호 되는 pod의 일부로 실행 되기 때문입니다. 그러나 [클라우드 기반 Cognitive Services][request-authentication]에 액세스할 때 사용 되는 인증과 동일 하 게 작동 하는 인증을 사용 하도록 설정할 수 있습니다.

아래 다이어그램은 기본 및 **안전** 하지 않은 방법을 보여 줍니다.

![컨테이너 보안](../media/container-security.svg)

다른 방법으로 *보안* 을 유지 하는 방법으로 Cognitive Services 컨테이너의 소비자는 컨테이너 끝점을 비공개로 유지 하는 프런트 엔드 구성 요소가 있는 컨테이너를 확장할 수 있습니다. [Istio][istio] 를 수신 게이트웨이로 사용 하는 시나리오를 살펴보겠습니다. Istio는 HTTPS/SSL 및 클라이언트 인증서 인증을 지원 합니다. 이 시나리오에서 Istio 프런트 엔드는 컨테이너 액세스를 노출 하 여 Istio로 미리 허용 목록 된 클라이언트 인증서를 표시 합니다.

[Nginx][nginx] 는 동일한 범주에서 널리 사용 되는 다른 옵션입니다. Istio와 Nginx는 모두 서비스 메시 역할을 하며 부하 분산, 라우팅 및 요율 제어와 같은 항목을 포함 하 여 추가 기능을 제공 합니다.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md

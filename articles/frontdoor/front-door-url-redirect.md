---
title: Azure 정문 - URL 리디렉션 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Front Door가 구성된 경우 해당 경로에 대한 URL 리디렉션을 지원하는 방법을 이해하는 데 도움이 됩니다.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295470"
---
# <a name="url-redirect"></a>URL 리디렉션
Azure 정문을 사용하여 트래픽을 리디렉션할 수 있습니다. 여러 수준(프로토콜, 호스트 이름, 경로, 쿼리 문자열)에서 트래픽을 리디렉션할 수 있으며 리디렉션이 경로 기반이므로 개별 마이크로 서비스에 대해 모든 기능을 구성할 수 있습니다. 이를 통해 애플리케이션 구성이 간소화되고, 리소스 사용이 최적화되고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다.
</br>

![Azure 정문 URL 리디렉션][1]

## <a name="redirection-types"></a>리디렉션 유형
리디렉션 유형은 클라이언트가 리디렉션의 목적을 이해하기 위해 응답 상태 코드를 설정합니다. 다음과 같은 리디렉션 유형이 지원됩니다.

- **301 (영구적으로 이동)**: 대상 리소스에 새 영구 URI가 할당되었으며 이 리소스에 대한 향후 참조는 동봉된 URI 중 하나를 사용해야 한다는 것을 나타냅니다. HTTPS 리디렉션에 HTTP에 대한 301 상태 코드를 사용합니다. 
- **302 (Found)**: 대상 리소스가 다른 URI 아래에 일시적으로 상주하고 있음을 나타냅니다. 리디렉션이 경우에 따라 변경될 수 있기 때문에 클라이언트는 이후 요청에 대해 유효 요청 URI를 계속 사용해야 합니다.
- **307 (임시 리디렉션)**: 대상 리소스가 다른 URI 아래에 일시적으로 상주하고 사용자 에이전트가 해당 URI로 자동 리디렉션을 수행하는 경우 요청 메서드를 변경하지 않아야 함을 나타냅니다. 리디렉션은 시간이 지남에 따라 변경될 수 있기 때문에 클라이언트는 이후 요청에 원래 유효 요청 URI를 계속 사용해야 합니다.
- **308 (영구 리디렉션)**: 대상 리소스에 새 영구 URI가 할당되었으며 이 리소스에 대한 향후 참조는 동봉된 URI 중 하나를 사용해야 한다는 것을 나타냅니다. 링크 편집 기능이 있는 클라이언트는 가능한 경우 서버에서 보낸 하나 이상의 새 참조에 대한 유효 요청 URI에 대한 참조를 자동으로 다시 연결해야 합니다.

## <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용할 프로토콜을 설정할 수 있습니다. 이렇게 하면 HTTP를 HTTPS 리디렉션으로 설정하는 리디렉션 기능의 가장 일반적인 사용 사례 중 하나가 허용됩니다.

- **HTTPS 만**: HTTP에서 HTTPS로 트래픽을 리디렉션하려는 경우에만 프로토콜을 HTTPS로 설정합니다. Azure 정문은 항상 HTTPS로만 리디렉션을 설정하는 것이 좋습니다.
- **HTTP 만**: 수신 요청을 HTTP로 리디렉션합니다. 암호화되지 않은 트래픽 HTTP를 유지하려는 경우에만 이 값을 사용합니다.
- **요청 일치**: 이 옵션은 들어오는 요청에 의해 사용되는 프로토콜을 유지합니다. 따라서 HTTP 요청은 HTTP로 남아 있고 HTTPS 요청은 HTTPS 게시물 리디렉션으로 유지됩니다.

## <a name="destination-host"></a>대상 호스트
리디렉션 라우팅을 구성하는 과정에서 리디렉션 요청에 대한 호스트 이름 또는 도메인을 변경할 수도 있습니다. 리디렉션에 대 한 URL에서 호스트 이름을 변경 하거나 들어오는 요청에서 호스트 이름을 유지 하도록이 필드를 설정할 수 있습니다. 따라서 이 필드를 사용하여 전송된 모든 `https://www.contoso.com/*` 요청을 `https://www.fabrikam.com/*`로 리디렉션할 수 있습니다.

## <a name="destination-path"></a>대상 경로
리디렉션의 일부로 URL의 경로 세그먼트를 바꾸려는 경우 이 필드를 새 경로 값으로 설정할 수 있습니다. 그렇지 않으면 리디렉션의 일부로 경로 값을 유지하도록 선택할 수 있습니다. 따라서 이 필드를 사용하여 전송된 모든 요청을 `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`로 리디렉션할 수 있습니다.

## <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
리디렉션된 URL에서 쿼리 문자열 매개 변수를 바꿀 수도 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 바꾸려면 이 필드를 '바꾸기'로 설정한 다음 적절한 값을 설정합니다. 그렇지 않으면 필드를 '보존'으로 설정하여 쿼리 문자열의 원래 집합을 유지할 수 있습니다. 예를 들어 이 필드를 사용하여 전송된 모든 트래픽을 `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`로 리디렉션할 수 있습니다. 

## <a name="destination-fragment"></a>대상 조각
대상 조각은 '#' 이후URL의 일부로, 일반적으로 브라우저에서 페이지의 특정 섹션에 착륙하는 데 사용됩니다. 리디렉션 URL에 조각을 추가하도록 이 필드를 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png
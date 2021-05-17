---
title: Azure Front Door - URL 리디렉션 | Microsoft Docs
description: 이 문서는 Azure Front Door가 라우팅 규칙에 대한 URL 리디렉션을 지원하는 방법을 이해하는 데 도움이 됩니다.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91442059"
---
# <a name="url-redirect"></a>URL 리디렉션
Azure Front Door는 프로토콜, 호스트 이름, 경로, 쿼리 문자열 수준에서 트래픽을 리디렉션할 수 있습니다. 리디렉션은 경로 기반이므로 개별 마이크로서비스에 대해 이러한 기능을 구성할 수 있습니다. 이는 리소스 사용을 최적화하여 애플리케이션 구성을 간소화할 수 있고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure Front Door URL 리디렉션":::

## <a name="redirection-types"></a>리디렉션 유형
리디렉션 유형은 클라이언트가 리디렉션 용도를 파악할 수 있도록 응답 상태 코드를 설정합니다. 지원되는 리디렉션 유형은 다음과 같습니다.

- **301(영구적 이동)** : 대상 리소스가 새 영구 URI에 할당되었음을 나타냅니다. 이후 이 리소스에 대한 모든 참조는 포함된 URI 중 하나를 사용합니다. HTTP에서 HTTPS로 리디렉션에 301 상태 코드를 사용합니다. 
- **302(찾음)** : 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 리디렉션은 수시로 변경될 수 있으므로 클라이언트는 이후 요청에 유효한 요청 URI를 계속 사용해야 합니다.
- **307(일시적 리디렉션)** : 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 사용자 에이전트는 해당 URI로 자동 리디렉션을 수행하는 경우 요청 메서드를 변경하지 않아야 합니다. 리디렉션은 시간이 지나면서 변경될 수 있으므로 클라이언트는 이후 요청에 원래의 유효한 요청 URI를 계속 사용해야 합니다.
- **308(영구적 리디렉션)** : 대상 리소스가 새 영구 URI에 할당되었음을 나타냅니다. 이후 이 리소스에 대한 모든 참조는 포함된 URI 중 하나를 사용해야 합니다.

## <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용되는 프로토콜을 설정할 수 있습니다. 리디렉션 기능의 가장 일반적인 사용 사례는 HTTP에서 HTTPS로의 리디렉션을 설정하는 것입니다.

- **HTTPS만 사용**: HTTP에서 HTTPS로 트래픽을 리디렉션하는 경우 프로토콜을 HTTPS만 사용으로 설정합니다. Azure Front Door는 항상 리디렉션을 HTTPS로만 설정할 것을 권장합니다.
- **HTTP만 사용**: 들어오는 요청을 HTTP로 리디렉션합니다. 트래픽을 HTTP(암호화되지 않음)로 유지하려는 경우에만 이 값을 사용합니다.
- **요청 일치**: 이 옵션은 들어오는 요청에서 사용하는 프로토콜을 유지합니다. 따라서 리디렉션 후 HTTP 요청은 HTTP로 유지되고 HTTPS 요청은 HTTPS로 유지됩니다.

## <a name="destination-host"></a>대상 호스트
리디렉션 라우팅을 구성하는 과정에서 리디렉션 요청에 대한 호스트 이름 또는 도메인을 변경할 수도 있습니다. 이 필드를 리디렉션 URL의 호스트 이름으로 변경할 수도 있고, 들어오는 요청의 호스트 이름을 유지할 수도 있습니다. 따라서 이 필드를 사용하여 `https://www.contoso.com/*`으로 전송되는 모든 요청을 `https://www.fabrikam.com/*`으로 리디렉션할 수 있습니다.

## <a name="destination-path"></a>대상 경로
리디렉션의 일부로 URL의 경로 세그먼트를 바꾸려는 경우 이 필드를 새 경로 값으로 설정할 수 있습니다. 그렇지 않으면 리디렉션의 일부로 경로 값을 유지하도록 선택할 수 있습니다. 따라서 이 필드를 사용하여 `https://www.contoso.com/\*`로 전송되는 모든 요청을 `https://www.contoso.com/redirected-site`로 리디렉션할 수 있습니다.

## <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
리디렉션되는 URL의 쿼리 문자열 매개 변수를 바꿀 수도 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 바꾸려면 이 필드를 '바꾸기'로 설정하고 적절한 값을 설정합니다. 그렇지 않으면 이 필드를 '유지'로 설정하여 원래 쿼리 문자열 집합을 유지할 수 있습니다. 예를 들어 이 필드를 사용하여 `https://www.contoso.com/foo/bar`로 전송되는 모든 트래픽을 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`로 리디렉션할 수 있습니다. 

## <a name="destination-fragment"></a>대상 조각
대상 조각은 브라우저가 웹 페이지의 특정 섹션을 표시하는 데 사용되는 URL의 '#' 이후 부분입니다. 이 필드를 설정하여 리디렉션 URL에 조각을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

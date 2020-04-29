---
title: Azure 전면 도어-URL 리디렉션 | Microsoft Docs
description: 이 문서는 Azure Front 도어가 구성 된 경우 해당 경로에 대 한 URL 리디렉션을 지 원하는 방법을 이해 하는 데 도움이 됩니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295470"
---
# <a name="url-redirect"></a>URL 리디렉션
Azure Front 도어를 사용 하 여 트래픽을 리디렉션할 수 있습니다. 여러 수준 (프로토콜, 호스트 이름, 경로, 쿼리 문자열)에서 트래픽을 리디렉션할 수 있으며 리디렉션이 경로 기반 이므로 개별 마이크로 서비스에 대 한 모든 기능을 구성할 수 있습니다. 이를 통해 애플리케이션 구성이 간소화되고, 리소스 사용이 최적화되고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다.
</br>

![Azure Front 도어 URL 리디렉션][1]

## <a name="redirection-types"></a>리디렉션 유형
리디렉션 유형은 클라이언트에 대 한 응답 상태 코드를 설정 하 여 리디렉션의 용도를 파악 합니다. 지원 되는 리디렉션 유형은 다음과 같습니다.

- **301 (영구적으로 이동)**: 대상 리소스가 새 영구 URI에 할당 되었으며이 리소스에 대 한 이후의 모든 참조에서 포함 된 uri 중 하나를 사용 해야 함을 나타냅니다. HTTP에서 HTTPS로 리디렉션에 301 상태 코드를 사용 합니다. 
- **302 (찾음)**: 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 경우에 따라 리디렉션이 변경 될 수 있으므로 클라이언트는 이후 요청에 유효한 요청 URI를 계속 사용 해야 합니다.
- **307 (임시 리디렉션)**: 대상 리소스가 일시적으로 다른 uri 아래에 있으며 사용자 에이전트가 해당 uri로 자동 리디렉션을 수행 하는 경우 요청 메서드를 변경 하지 않아야 함을 나타냅니다. 리디렉션은 시간이 지남에 따라 변경 될 수 있으므로 클라이언트는 이후 요청에 대해 원래 유효 요청 URI를 계속 사용 해야 합니다.
- **308 (영구 리디렉션)**: 대상 리소스가 새 영구 URI에 할당 되었으며이 리소스에 대 한 이후의 모든 참조에서 포함 된 uri 중 하나를 사용 해야 함을 나타냅니다. 링크 편집 기능이 있는 클라이언트는 가능한 경우 서버에서 보낸 하나 이상의 새 참조에 유효한 요청 URI에 대 한 참조를 자동으로 다시 연결 합니다.

## <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용 되는 프로토콜을 설정할 수 있습니다. 이를 통해 리디렉션 기능의 가장 일반적인 사용 사례 중 하나를 사용할 수 있습니다. 즉, HTTP에서 HTTPS로의 리디렉션을 설정 합니다.

- **Https만**: HTTP에서 https로 트래픽을 리디렉션하는 경우에만 프로토콜을 https로 설정 합니다. Azure 전면 도어는 항상 리디렉션을 HTTPS로 설정 하는 것이 좋습니다.
- **Http만**: 들어오는 요청을 http로 리디렉션합니다. 트래픽 HTTP (암호화 되지 않음)를 유지 하려는 경우에만이 값을 사용 합니다.
- **일치 요청**:이 옵션은 들어오는 요청에서 사용 하는 프로토콜을 유지 합니다. 따라서 HTTP 요청은 HTTP로 유지 되 고 HTTPS 요청은 HTTPS 사후 리디렉션으로 유지 됩니다.

## <a name="destination-host"></a>대상 호스트
리디렉션 라우팅을 구성 하는 과정에서 리디렉션 요청에 대 한 호스트 이름 또는 도메인을 변경할 수도 있습니다. 이 필드를 설정 하 여 리디렉션의 URL의 호스트 이름을 변경 하거나, 들어오는 요청에서 호스트 이름을 유지할 수 있습니다. 따라서이 필드를 사용 하 여에 전송 되는 모든 `https://www.contoso.com/*` 요청 `https://www.fabrikam.com/*`을로 리디렉션할 수 있습니다.

## <a name="destination-path"></a>대상 경로
URL의 경로 세그먼트를 리디렉션의 일부로 바꾸려는 경우이 필드를 새 경로 값으로 설정할 수 있습니다. 그렇지 않으면 경로의 일부로 경로 값을 유지 하도록 선택할 수 있습니다. 따라서이 필드를 사용 하 여로 `https://www.contoso.com/\*` 전송 되는 모든 요청을 `https://www.contoso.com/redirected-site`로 리디렉션할 수 있습니다.

## <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
리디렉션된 URL의 쿼리 문자열 매개 변수를 대체할 수도 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 바꾸려면이 필드를 ' Replace '로 설정한 다음 적절 한 값을 설정 합니다. 그렇지 않으면 필드를 ' 유지 '로 설정 하 여 원래 쿼리 문자열 집합을 유지할 수 있습니다. 예를 들어이 필드를 사용 하 여로 `https://www.contoso.com/foo/bar` 전송 된 모든 트래픽을로 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`리디렉션할 수 있습니다. 

## <a name="destination-fragment"></a>대상 조각
대상 조각은 일반적으로 브라우저에서 페이지의 특정 섹션에 배치 하는 데 사용 되는 URL의 일부입니다. 이 필드를 설정 하 여 리디렉션 URL에 조각을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png
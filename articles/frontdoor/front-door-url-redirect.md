---
title: Azure 전면 도어-URL 리디렉션 | Microsoft Docs
description: 이 문서는 Azure Front 도어가 라우팅 규칙에 대 한 URL 리디렉션을 지 원하는 방법을 이해 하는 데 도움이 됩니다.
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
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442059"
---
# <a name="url-redirect"></a>URL 리디렉션
Azure 전면 도어는 프로토콜, 호스트 이름, 경로, 쿼리 문자열의 각 수준에서 트래픽을 리디렉션할 수 있습니다. 리디렉션이 경로 기반 이므로 개별 마이크로 서비스에 대해 이러한 기능을 구성할 수 있습니다. 이렇게 하면 리소스 사용을 최적화 하 여 응용 프로그램 구성을 간소화할 수 있으며 전역 및 경로 기반 리디렉션을 비롯 한 새로운 리디렉션 시나리오를 지원할 수 있습니다.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure Front 도어 URL 리디렉션":::

## <a name="redirection-types"></a>리디렉션 유형
리디렉션 유형은 클라이언트에 대 한 응답 상태 코드를 설정 하 여 리디렉션의 용도를 파악 합니다. 지원 되는 리디렉션 유형은 다음과 같습니다.

- **301 (영구적으로 이동)**: 대상 리소스가 새 영구 URI에 할당 되었음을 나타냅니다. 이 리소스에 대 한 이후의 모든 참조는 포함 된 Uri 중 하나를 사용 합니다. HTTP에서 HTTPS로 리디렉션에 301 상태 코드를 사용 합니다. 
- **302 (찾음)**: 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 경우에 따라 리디렉션이 변경 될 수 있으므로 클라이언트는 이후 요청에 유효한 요청 URI를 계속 사용 해야 합니다.
- **307 (임시 리디렉션)**: 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 사용자 에이전트는 해당 URI로의 자동 리디렉션을 수행 하는 경우 요청 메서드를 변경 하지 않아야 합니다. 리디렉션은 시간이 지남에 따라 변경 될 수 있으므로 클라이언트는 이후 요청에 대해 원래 유효 요청 URI를 계속 사용 해야 합니다.
- **308 (영구 리디렉션)**: 대상 리소스에 새 영구 URI가 할당 되었음을 나타냅니다. 이 리소스에 대 한 이후의 모든 참조는 포함 된 Uri 중 하나를 사용 해야 합니다.

## <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용 되는 프로토콜을 설정할 수 있습니다. 리디렉션 기능의 가장 일반적인 사용 사례는 HTTP를 HTTPS 리디렉션으로 설정 하는 것입니다.

- **Https만**: HTTP에서 https로 트래픽을 리디렉션하는 경우에만 프로토콜을 https로 설정 합니다. Azure 전면 도어는 항상 리디렉션을 HTTPS로 설정 하는 것이 좋습니다.
- **Http만**: 들어오는 요청을 HTTP로 리디렉션합니다. 트래픽 HTTP (암호화 되지 않음)를 유지 하려는 경우에만이 값을 사용 합니다.
- **일치 요청**:이 옵션은 들어오는 요청에서 사용 하는 프로토콜을 유지 합니다. 따라서 HTTP 요청은 HTTP로 유지 되 고 HTTPS 요청은 HTTPS 사후 리디렉션으로 유지 됩니다.

## <a name="destination-host"></a>대상 호스트
리디렉션 라우팅을 구성 하는 과정에서 리디렉션 요청에 대 한 호스트 이름 또는 도메인을 변경할 수도 있습니다. 이 필드를 설정 하 여 리디렉션의 URL의 호스트 이름을 변경 하거나, 들어오는 요청에서 호스트 이름을 유지할 수 있습니다. 따라서이 필드를 사용 하 여에 전송 되는 모든 요청을로 리디렉션할 수 있습니다 `https://www.contoso.com/*` `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>대상 경로
URL의 경로 세그먼트를 리디렉션의 일부로 바꾸려는 경우이 필드를 새 경로 값으로 설정할 수 있습니다. 그렇지 않으면 경로의 일부로 경로 값을 유지 하도록 선택할 수 있습니다. 따라서이 필드를 사용 하 여로 전송 되는 모든 요청을로 리디렉션할 수 있습니다 `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
리디렉션된 URL의 쿼리 문자열 매개 변수를 대체할 수도 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 바꾸려면이 필드를 ' Replace '로 설정 하 고 적절 한 값을 설정 합니다. 그렇지 않으면 필드를 ' 유지 '로 설정 하 여 원래 쿼리 문자열 집합을 유지할 수 있습니다. 예를 들어이 필드를 사용 하 여로 전송 된 모든 트래픽을로 리디렉션할 수 있습니다 `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>대상 조각
대상 조각은 브라우저에서 웹 페이지의 특정 섹션에 배치 하는 데 사용 되는 ' # ' 이후의 URL 부분입니다. 이 필드를 설정 하 여 리디렉션 URL에 조각을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

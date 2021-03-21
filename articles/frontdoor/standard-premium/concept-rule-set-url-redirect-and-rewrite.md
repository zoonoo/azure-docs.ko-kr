---
title: Azure 전면 도어 표준/프리미엄 (미리 보기)을 사용 하 여 URL 리디렉션 및 URL 재작성
description: 이 문서는 azure front 도어가 Azure Front 도어 규칙 집합을 사용 하 여 URL 리디렉션 및 URL 재작성을 지 원하는 방법을 이해 하는 데 도움이 됩니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100007"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)을 사용 하 여 URL 리디렉션 및 URL 재작성

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서는 Azure Front 도어 Standard/Premium에서 규칙 집합에 사용 되는 URL 리디렉션 및 URL 재작성을 지 원하는 방법을 이해 하는 데 도움이 됩니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="url-redirect"></a>URL 리디렉션

Azure 전면 도어는 프로토콜, 호스트 이름, 경로, 쿼리 문자열 및 조각 등의 각 수준에서 트래픽을 리디렉션할 수 있습니다. 리디렉션이 경로 기반 이므로 개별 마이크로 서비스에 대해 이러한 기능을 구성할 수 있습니다. URL 리디렉션을 사용 하면 리소스 사용을 최적화 하 여 응용 프로그램 구성을 간소화할 수 있으며 전역 및 경로 기반 리디렉션을 비롯 한 새로운 리디렉션 시나리오를 지원할 수 있습니다.

규칙 집합을 통해 URL 리디렉션을 구성할 수 있습니다.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="규칙 집합을 사용 하 여 url 리디렉션을 만드는 스크린샷" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>리디렉션 유형
리디렉션 유형은 클라이언트에 대 한 응답 상태 코드를 설정 하 여 리디렉션의 용도를 파악 합니다. 지원 되는 리디렉션 유형은 다음과 같습니다.

* **301 (영구적으로 이동)**: 대상 리소스가 새 영구 URI에 할당 되었음을 나타냅니다. 이 리소스에 대 한 이후의 모든 참조는 포함 된 Uri 중 하나를 사용 합니다. HTTP에서 HTTPS로 리디렉션에 301 상태 코드를 사용 합니다.
* **302 (찾음)**: 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 경우에 따라 리디렉션이 변경 될 수 있으므로 클라이언트는 이후 요청에 유효한 요청 URI를 계속 사용 해야 합니다.
* **307 (임시 리디렉션)**: 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 사용자 에이전트는 해당 URI로의 자동 리디렉션을 수행 하는 경우 요청 메서드를 변경 하지 않아야 합니다. 리디렉션은 시간이 지남에 따라 변경 될 수 있으므로 클라이언트는 이후 요청에 대해 원래 유효 요청 URI를 계속 사용 해야 합니다.
* **308 (영구 리디렉션)**: 대상 리소스에 새 영구 URI가 할당 되었음을 나타냅니다. 이 리소스에 대 한 이후의 모든 참조는 포함 된 Uri 중 하나를 사용 해야 합니다.

### <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용 되는 프로토콜을 설정할 수 있습니다. 리디렉션 기능의 가장 일반적인 사용 사례는 HTTP를 HTTPS 리디렉션으로 설정 하는 것입니다.

* **Https만**: HTTP에서 https로 트래픽을 리디렉션하는 경우에만 프로토콜을 https로 설정 합니다. Azure 전면 도어는 항상 리디렉션을 HTTPS로 설정 하는 것이 좋습니다.
* **Http만**: 들어오는 요청을 HTTP로 리디렉션합니다. 트래픽 HTTP (암호화 되지 않음)를 유지 하려는 경우에만이 값을 사용 합니다.
* **일치 요청**:이 옵션은 들어오는 요청에서 사용 하는 프로토콜을 유지 합니다. 따라서 HTTP 요청은 HTTP로 유지 되 고 HTTPS 요청은 HTTPS 사후 리디렉션으로 유지 됩니다.

### <a name="destination-host"></a>대상 호스트
리디렉션 라우팅을 구성 하는 과정에서 리디렉션 요청에 대 한 호스트 이름 또는 도메인을 변경할 수도 있습니다. 이 필드를 설정 하 여 리디렉션의 URL의 호스트 이름을 변경 하거나, 들어오는 요청에서 호스트 이름을 유지할 수 있습니다. 따라서이 필드를 사용 하 여에 전송 되는 모든 요청을로 리디렉션할 수 있습니다 `https://www.contoso.com/*` `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>대상 경로
URL의 경로 세그먼트를 리디렉션의 일부로 바꾸려는 경우이 필드를 새 경로 값으로 설정할 수 있습니다. 그렇지 않으면 경로의 일부로 경로 값을 유지 하도록 선택할 수 있습니다. 따라서이 필드를 사용 하 여로 전송 되는 모든 요청을로 리디렉션할 수 있습니다 `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
리디렉션된 URL의 쿼리 문자열 매개 변수를 대체할 수도 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 바꾸려면이 필드를 ' Replace '로 설정 하 고 적절 한 값을 설정 합니다. 그렇지 않으면 필드를 ' 유지 '로 설정 하 여 원래 쿼리 문자열 집합을 유지할 수 있습니다. 예를 들어이 필드를 사용 하 여로 전송 된 모든 트래픽을로 리디렉션할 수 있습니다 `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>대상 조각
대상 조각은 브라우저에서 웹 페이지의 특정 섹션에 배치 하는 데 사용 되는 ' # ' 이후의 URL 부분입니다. 이 필드를 설정 하 여 리디렉션 URL에 조각을 추가할 수 있습니다.

## <a name="url-rewrite"></a>URL 다시 쓰기

Azure 전면 도어는 URL 재작성을 지원 하 여 원본으로 라우팅하는 요청의 경로를 다시 작성 합니다. URL 재작성을 사용 하면 특정 조건이 충족 될 때만 URL 또는 지정 된 헤더를 다시 작성할 수 있도록 조건을 추가할 수 있습니다. 이러한 조건은 요청 및 응답 정보를 기반으로 합니다.

이 기능을 사용 하면 시나리오, 장치 유형 및 요청 된 파일 유형에 따라 사용자를 다른 원본으로 리디렉션할 수 있습니다.

규칙 집합을 통해 URL 리디렉션을 구성할 수 있습니다.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="규칙 집합을 사용 하 여 url 재작성을 만드는 스크린샷" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>원본 패턴

원본 패턴은 바꾸려는 원본 요청의 URL 경로입니다. 현재 소스 패턴은 접두사 기반 일치를 사용 합니다. 모든 URL 경로를 일치 시키려면 소스 패턴 값으로 슬래시 (/)를 사용 합니다.

### <a name="destination"></a>대상

재작성에 사용할 대상 경로를 정의할 수 있습니다. 대상 경로는 원본 패턴을 덮어씁니다.

### <a name="preserve-unmatched-path"></a>일치 하지 않는 경로 유지

일치 하지 않는 경로 유지를 사용 하면 소스 패턴 뒤의 나머지 경로를 새 경로에 추가할 수 있습니다.

예를 들어 **일치 하지 않는 경로 유지를 예로 설정 하** 는 경우입니다.
* 들어오는 요청이 인 경우 소스 패턴은로 설정 되 고 `www.contoso.com/sub/1.jpg` `/` , 대상 가져오기는로 설정 `/foo/` 되며, `/foo/sub/1` 원본에서 .jpg의 콘텐츠가 제공 됩니다.

* 들어오는 요청이 인 경우 소스 패턴은로 설정 되 고 `www.contoso.com/sub/image/1.jpg` `/sub/` , 대상 가져오기는로 설정 되 고 `/foo/` , `/foo/image/1.jpg` 원본은 원본에서 제공 됩니다.

예를 들어 **일치 하지 않는 경로 유지를 아니요로 설정 하** 는 경우입니다.
* 들어오는 요청이 인 경우 소스 패턴은로 설정 되 고 `www.contoso.com/sub/image/1.jpg` `/sub/` , 대상 가져오기는로 설정 되며 `/foo/2.jpg` , `/foo/2.jpg` 다음에 나오는 경로에 관계 없이 원본에서 콘텐츠가 항상 제공 됩니다 `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>다음 단계

* [Azure Front 도어 표준/프리미엄 규칙 집합](concept-rule-set.md)에 대해 자세히 알아보세요.

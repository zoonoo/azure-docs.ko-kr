---
title: Azure Front Door 표준/프리미엄 경로란?
description: 이 문서는 Azure Front Door 표준/프리미엄이 들어오는 요청에 사용할 라우팅 규칙과 일치하는 방식을 이해하는 데 도움이 됩니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100050"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Azure Front Door 표준/프리미엄(미리 보기) 경로란?

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door 표준/프리미엄 경로는 들어오는 요청이 Azure Front Door 환경에 도착할 때 트래픽이 처리되는 방식을 정의합니다. 경로 설정을 통해 도메인과 백 엔드 원본 그룹 간에 연결이 정의됩니다. Pattern 대 Mach, 규칙 집합과 같은 고급 기능을 켜면 트래픽을 보다 세밀하게 제어할 수 있습니다.

Front Door 표준/프리미엄 라우팅 구성은 크게 “왼쪽”과 “오른쪽” 부분으로 이루어집니다. 들어오는 요청을 경로의 왼쪽에 일치시키고 오른쪽은 요청을 처리하는 방법을 정의합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="incoming-match-left-hand-side"></a>들어오는 매칭(왼쪽)

다음은 들어오는 요청이 회람 규칙(또는 왼쪽)과 매칭되는지 여부를 결정하는 속성입니다.

* **HTTP 프로토콜**(HTTP/HTTPS)
* **호스트**(예: www\.foo.com, \*.bar.com)
* **경로**(예: /\*, /users/\*, /file.gif)

이러한 속성은 각 프로토콜/호스트/경로 조합이 가능한 매칭 집합이 될 수 있도록 내부적으로 확장됩니다.

### <a name="route-data-right-hand-side"></a>경로 데이터(오른쪽)

요청을 처리하는 방법은 경로에 캐싱을 사용할지 여부에 따라 달라집니다. 캐시된 응답을 사용할 수 없는 경우 요청이 적절한 백 엔드로 전달됩니다.

## <a name="route-matching"></a>경로 매칭

이 섹션에서는 지정된 Front Door 회람 규칙에 매칭하는 방법을 중점적으로 설명합니다. 기본 개념은 항상 "왼쪽"만 보면서 **가장 구체적인 첫 번째 매칭** 에 매칭하는 것입니다.  HTTP 프로토콜, 프런트 엔드 호스트, 경로 순서로 매칭합니다.

### <a name="frontend-host-matching"></a>프런트 엔드 호스트 매칭

프런트 엔드 호스트를 일치시킬 때는 다음과 같은 논리를 사용합니다.

1. 정확히 일치하는 호스트가 있는 회람 규칙을 찾습니다.
2. 정확히 일치하는 프런트 엔드 호스트가 없을 경우 요청을 거부하고 400 잘못된 요청 오류를 보냅니다.

이 프로세스를 자세히 설명하기 위해 Front Door 경로(왼쪽만 해당)의 구성 예제를 살펴보겠습니다.

| 라우팅 규칙 | 프런트 엔드 호스트 | 경로 |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| b | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Front Door에 다음과 같은 요청이 전송된 경우, 이러한 요청은 위의 회람 규칙 중 다름 규칙과 일치합니다.

| 수신 프런트 엔드 호스트 | 일치하는 회람 규칙 |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | 오류 400: 잘못된 요청 |
| foo.adventure-works.com | C |
| contoso.com | 오류 400: 잘못된 요청 |
| www\.adventure-works.com | 오류 400: 잘못된 요청 |
| www\.northwindtraders.com | 오류 400: 잘못된 요청 |

### <a name="path-matching"></a>경로 매칭

Azure Front Door 표준/프리미엄이 특정 프런트 엔드 호스트를 결정한 후 해당 프런트 엔드 호스트를 사용하여 가능한 라우팅 규칙을 경로에만 필터링합니다. 그다음 Front Door가 요청 경로를 기반으로 라우팅 규칙을 필터링합니다. 프런트 엔드 호스트와 유사한 논리를 사용하겠습니다.

1. 정확히 일치하는 경로가 있는 회람 규칙을 찾습니다.
2. 정확히 일치하는 경로가 없을 경우 일치하는 와일드카드 경로가 있는 회람 규칙을 찾습니다.
3. 일치하는 경로가 있는 회람 규칙이 없을 경우 요청을 거부하고 400: 잘못된 요청 오류 HTTP 응답을 반환합니다.

>[!NOTE]
> 와일드카드가 없는 모든 경로는 정확히 일치하는 경로로 간주됩니다. 경로가 슬래시로 끝나는 경우에도 정확한 일치 항목으로 간주됩니다.

자세한 설명을 위해 또 다른 예제 집합을 살펴보겠습니다.

| 라우팅 규칙 | 프런트 엔드 호스트    | 경로     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| b     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

해당 구성을 고려할 때 다음과 같이 일치하는 테이블 예제의 결과는 다음과 같습니다.

| 들어오는 요청    | 일치하는 경로 |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | b             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | b             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | b             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | b             |

>[!WARNING]
> </br> 모두 캐치 라우팅 경로(`/*`)와 정확히 일치하는 프런트 엔드 호스트에 대한 회람 규칙이 없을 경우 회람 규칙과 일치하는 항목이 없는 것입니다.
>
> 예제 구성:
>
> | 경로 | 호스트             | 경로    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> 일치하는 테이블:
>
> | 들어오는 요청       | 일치하는 경로 |
> |------------------------|---------------|
> | profile.domain.com/other | 없음 오류 400: 잘못된 요청 |

### <a name="routing-decision"></a>라우팅 의사 결정

Azure Front Door 표준/프리미엄이 단일 라우팅 규칙과 일치한 후에는 요청을 처리하는 방법을 선택해야 합니다. 일치하는 라우팅 규칙에 사용 가능한 캐시된 응답이 Azure Front Door 표준/프리미엄에 있을 경우 요청이 클라이언트에 다시 제공됩니다. Azure Front Door 표준/프리미엄이 다음으로 평가하는 것은 일치하는 라우팅 규칙에 대한 규칙 집합이 있는지 여부입니다. 정의된 규칙 집합이 없는 경우 요청은 백 엔드 풀에 그대로 전달됩니다. 그러지 않으면 규칙 집합이 구성된 순서대로 실행됩니다.

## <a name="next-steps"></a>다음 단계

[Front Door 표준/프리미엄 만드는](create-front-door-portal.md) 방법 알아보기.

---
title: Azure 정문 - 와일드카드 도메인 지원
description: 이 문서에서는 Azure Front Door가 사용자 지정 도메인 목록에서 와일드카드 도메인매핑 및 관리를 지원하는 방법을 이해하는 데 도움이 됩니다.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768311"
---
# <a name="wildcard-domains"></a>와일드카드 도메인

정점 도메인 및 하위 도메인 이외에 Azure Front Door 프로필의 프런트 엔드 호스트 또는 사용자 지정 도메인 목록에 와일드카드 도메인 이름을 매핑할 수 있습니다. Azure Front Door 구성에 와일드카드 도메인이 있으면 동일한 라우팅 규칙에서 API, 응용 프로그램 또는 웹 사이트에 대한 여러 하위 도메인에 대한 트래픽 라우팅 동작이 간소화됩니다. 각 하위 도메인을 별도로 추가하거나 지정하기 위해 구성을 수정할 필요가 없습니다. `customer1.contoso.com`예를 들어 에 `customer2.contoso.com`대한 라우팅을 정의하고 `customerN.contoso.com` 동일한 라우팅 규칙을 사용하고 와일드카드 도메인을 `*.contoso.com`추가하여 .

와일드카드 도메인을 지원하여 개선된 주요 시나리오는 다음과 같습니다.

- Azure Front Door 프로필의 각 하위 도메인을 온보온한 다음 HTTPS를 사용하여 각 하위 도메인에 대한 인증서를 바인딩할 필요가 없습니다.
- 응용 프로그램이 새 하위 도메인을 추가하는 경우 프로덕션 Azure 정문 구성을 더 이상 변경할 필요가 없습니다. 이전에는 하위 도메인을 추가하고, 인증서를 바인딩하고, WAF(웹 응용 프로그램 방화벽) 정책을 연결한 다음 다른 라우팅 규칙에 도메인을 추가해야 했습니다.

> [!NOTE]
> 현재 와일드카드 도메인은 API, PowerShell 및 Azure CLI를 통해서만 지원됩니다. Azure 포털에서 와일드카드 도메인 추가 및 관리에 대한 지원을 사용할 수 없습니다.

## <a name="adding-wildcard-domains"></a>와일드카드 도메인 추가

프런트 엔드 호스트 또는 도메인섹션 아래에 와일드카드 도메인을 추가할 수 있습니다. 하위 도메인과 마찬가지로 Azure Front Door는 와일드카드 도메인에 대한 CNAME 레코드 매핑이 있는지 확인합니다. 이 DNS 매핑은 에 매핑된 `*.contoso.com` 것과 같은 `contoso.azurefd.net`직접 CNAME 레코드 매핑일 수 있습니다. 또는 afdverify 임시 매핑을 사용할 수 있습니다. 예를 들어 `afdverify.contoso.com` 와일드카드에 `afdverify.contoso.azurefd.net` 대한 CNAME 레코드 맵의 유효성을 검사하도록 매핑됩니다.

> [!NOTE]
> Azure DNS는 와일드카드 레코드를 지원합니다.

프런트 엔드 호스트에 와일드카드 도메인의 단일 레벨 하위 도메인을 최대 프런트 엔드 호스트까지 추가할 수 있습니다. 이 기능은 다음의 경우 필요할 수 있습니다.

- 하위 도메인에 대한 다른 경로를 와일드카드 도메인에서 나머지 도메인과 정의합니다.

- 특정 하위 도메인에 대해 다른 WAF 정책을 갖는 경우 예를 들어 `*.contoso.com` 도메인 `foo.contoso.com` 소유권을 다시 증명하지 않고도 추가할 수 있습니다. 그러나 `*.contoso.com`의 단일 `foo.bar.contoso.com` 수준 하위 도메인이 아니기 때문에 허용되지 않습니다. 추가 `foo.bar.contoso.com` 도메인 소유권 유효성 `*.bar.contosonews.com` 검사 없이 추가하려면 추가해야 합니다.

와일드카드 도메인과 하위 도메인을 다음과 같은 제한 사항으로 추가할 수 있습니다.

- 와일드카드 도메인이 Azure 정문 프로필에 추가된 경우:
  - 와일드카드 도메인은 다른 Azure 정문 프로필에 추가할 수 없습니다.
  - 와일드카드 도메인의 첫 번째 하위 도메인은 다른 Azure 정문 프로필 또는 Azure 콘텐츠 배달 네트워크 프로필에 추가할 수 없습니다.
- 와일드카드 도메인의 하위 도메인이 Azure 정문 프로필 또는 Azure 콘텐츠 배달 네트워크 프로필에 추가된 경우 와일드카드 도메인은 다른 Azure 정문 프로필에 추가할 수 없습니다.
- 두 프로필(Azure Front Door 또는 Azure 콘텐츠 배달 네트워크)에 루트 도메인의 다양한 하위 도메인이 있는 경우 와일드카드 도메인을 프로필 중 하나에 추가할 수 없습니다.

## <a name="certificate-binding"></a>인증서 바인딩

와일드카드 도메인에서 HTTPS 트래픽을 수락하려면 와일드카드 도메인에서 HTTPS를 사용하도록 설정해야 합니다. 와일드카드 도메인에 대한 인증서 바인딩에는 와일드카드 인증서가 필요합니다. 즉, 인증서의 주체 이름에와일드카드 도메인도 있어야 합니다.

> [!NOTE]
> 현재 와일드카드 도메인에 대해 HTTPS를 사용하도록 설정하는 데 는 사용자 지정 SSL 인증서 옵션만 사용할 수 있습니다. Azure 정문 관리 인증서는 와일드카드 도메인에 사용할 수 없습니다.

Azure 키 자격 증명 모음또는 하위 도메인에 대해 Azure Front Door 관리 인증서에서 동일한 와일드카드 인증서를 사용하도록 선택할 수 있습니다.

이미 연결된 인증서가 있는 와일드카드 도메인에 대해 하위 도메인이 추가된 경우 하위 도메인에 대한 HTTPS를 비활성화할 수 없습니다. 하위 도메인은 다른 Key Vault 또는 Azure Front Door 관리 인증서가 이를 재정의하지 않는 한 와일드카드 도메인에 대한 인증서 바인딩을 사용합니다.

## <a name="waf-policies"></a>WAF 정책

WAF 정책은 다른 도메인과 마찬가지로 와일드카드 도메인에 연결할 수 있습니다. 와일드카드 도메인의 하위 도메인에 다른 WAF 정책을 적용할 수 있습니다. 하위 도메인의 경우 와일드카드 도메인과 동일한 정책인 경우에도 사용할 WAF 정책을 지정해야 합니다. 하위 도메인은 와일드카드 도메인에서 WAF 정책을 자동으로 *상속하지 않습니다.*

하위 도메인에 대해 WAF 정책을 실행하지 않으려면 관리되는 규칙이나 사용자 지정 규칙 집합이 없는 빈 WAF 정책을 만들 수 있습니다.

## <a name="routing-rules"></a>라우팅 규칙

라우팅 규칙을 구성할 때 와일드카드 도메인을 프런트 엔드 호스트로 선택할 수 있습니다. 와일드카드 도메인 및 하위 도메인에 대해 서로 다른 경로 동작을 가질 수도 있습니다. [Azure Front Door가 라우팅 일치를 수행하는 방법에](front-door-route-matching.md)설명된 대로 런타임에 다른 라우팅 규칙에서 도메인에 대한 가장 구체적인 일치가 선택됩니다.

> [!IMPORTANT]
> 라우팅 규칙 에서 일치하는 경로 패턴이 있어야하거나 클라이언트에 오류가 표시됩니다. 예를 들어 국도 1(백`*.foo.com/*` 엔드 풀 A에 매핑) 및 Route 2(백`bar.foo.com/somePath/*` 엔드 풀 B에 매핑)와 같은 두 개의 라우팅 규칙이 있습니다. 그런 다음 에 대한 `bar.foo.com/anotherPath/*`요청이 도착합니다. Azure Front Door는 경로 간에 일치하는 경로 패턴을 찾기 위해보다 구체적인 도메인 일치를 기반으로 경로 2를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 정문 프로필을 만드는](quickstart-create-front-door.md)방법에 대해 알아봅니다.
- Azure 정문 에서 [사용자 지정 도메인을 추가하는](front-door-custom-domain.md)방법에 대해 알아봅니다.
- [사용자 지정 도메인에서 HTTPS를 사용하도록 설정하는](front-door-custom-domain-https.md)방법에 대해 알아봅니다.

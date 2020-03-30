---
title: Azure 정문 - 와일드카드 도메인 지원
description: 이 문서에서는 Azure Front Door가 사용자 지정 도메인 목록에서 와일드카드 도메인 매핑 및 관리를 지원하는 방법을 이해하는 데 도움이 됩니다.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537441"
---
# <a name="wildcard-domains"></a>와일드카드 도메인

정점 도메인 및 하위 도메인 이외에 프런트 엔드 호스트 또는 정문 프로필의 사용자 지정 도메인 목록에 와일드카드 도메인 이름을 매핑할 수도 있습니다. Front Door 구성에 와일드카드 도메인이 있으면 각 하위 도메인을 개별적으로 추가 및/또는 지정하기 위해 구성을 수정할 필요 없이 동일한 라우팅 규칙에서 API, 응용 프로그램 또는 웹 사이트에 대한 여러 하위 도메인에 대한 트래픽 라우팅 동작이 간소화됩니다. `customer1.contoso.com`예를 들어, 와일드카드 도메인을 `customer2.contoso.com` `customerN.contoso.com` `*.contoso.com`추가하여 에 대한 라우팅및 동일한 라우팅 규칙을 사용할 수 있습니다.

와일드카드 도메인에 대한 지원으로 해결되는 몇 가지 주요 시나리오는 다음과 같습니다.

- 더 이상 정문에서 각 하위 도메인을 온보딩한 다음 HTTPS에서 각 하위 도메인에 대한 인증서를 바인딩할 필요가 없습니다.
- 응용 프로그램이 새 하위 도메인을 추가하는 경우 더 이상 프로덕션 프론트 도어 구성을 변경할 필요가 없습니다. 그렇지 않으면 이전에 하위 도메인을 추가하고 인증서를 바인딩하고 WAF(웹 응용 프로그램 방화벽) 정책을 연결하고 다른 라우팅 규칙에 도메인을 추가해야 했습니다.

> [!NOTE]
> 현재 와일드카드 도메인은 API, PowerShell 및 CLI를 통해서만 지원됩니다. Azure 포털을 통해 와일드카드 도메인 관리를 추가하는 데 대한 지원을 사용할 수 없습니다.

## <a name="adding-wildcard-domains"></a>와일드카드 도메인 추가

프런트 엔드 호스트 또는 도메인 섹션에서 와일드카드 도메인을 온보딩할 수 있습니다. 하위 도메인과 마찬가지로 Front Door는 와일드카드 도메인에 대한 CNAME 매핑도 있는지 확인합니다. 이 DNS 매핑은 와일드카드에 대한 `*.contoso.com` CNAME `contoso.azurefd.net` 맵의 유효성을 검사하기 `afdverify.contoso.com` 위해 `afdverify.contoso.azurefd.net` 매핑된 것과 같은 afdverify 임시 매핑에 매핑되거나 매핑된 것과 같은 직접 CNAME 매핑일 수 있습니다(Azure DNS는 와일드카드 레코드를 지원합니다).

최대값에 도달하지 않는 경우 프런트 엔드 호스트에 와일드카드 도메인의 단일 수준 하위 도메인을 최대한 추가할 수도 있습니다. 프런트 엔드 호스트의 제한. 이 기능은 나머지 도메인(와일드카드 도메인)과 다른 하위 도메인에 대해 다른 경로를 정의하거나 특정 하위 도메인에 대해 다른 WAF 정책을 갖는 데 필요할 수 있습니다. 따라서 도메인 소유권을 다시 증명하지 않고도 `foo.bar.contoso.com` 추가할 `foo.contoso.com` 수 있지만 `*.contoso.com`의 단일 수준 하위 도메인이 아닙니다. `*.contoso.com` 추가 `foo.bar.contoso.com` 도메인 소유권 유효성 `*.bar.contosonews.com` 검사 없이 추가하려면 추가해야 합니다.

### <a name="limitations"></a>제한 사항

1. 와일드카드 도메인이 지정된 정문 프로필에 추가된 경우 다른 정문 프로필에 와일드카드 도메인을 추가할 수 없습니다. 
2. 와일드카드 도메인이 지정된 정문 프로필에 추가된 경우 해당 와일드카드 도메인의 하위 도메인은 다른 정문 또는 Microsoft 프로필의 Azure CDN에 추가할 수 없습니다.
3. 와일드카드 도메인의 하위 도메인이 프런트 도어 프로필 또는 Microsoft 프로필의 Azure CDN에 추가된 경우 와일드카드 도메인은 다른 Front Door 프로필에 추가할 수 없습니다. 
4. 두 프로필(Microsoft의 정문 또는 Azure CDN)에 루트 도메인의 다양한 하위 도메인이 있는 경우 와일드카드 도메인은 프로필 중 하나에 추가할 수 없습니다.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>와일드카드 도메인 및 하위 도메인에 대한 인증서 바인딩

와일드카드 도메인에서 HTTPS 트래픽을 수락하려면 와일드카드 도메인에서 HTTPS를 사용하도록 설정해야 합니다. 와일드카드 도메인에 대한 인증서 바인딩에는 와일드카드 인증서, 즉 인증서의 주체 이름도 와일드카드 도메인이 있어야 합니다.

> [!NOTE]
> 현재 와일드카드 도메인에 대해 HTTPS를 사용하도록 설정하는 데 는 사용자 지정 SSL 인증서 옵션만 사용할 수 있습니다. 와일드카드 도메인에는 Front Door 관리 인증서를 사용할 수 없습니다. 

하위 도메인에 키 볼트에서 동일한 와일드카드 인증서를 사용하도록 선택하거나 하위 도메인에 대한 정문 관리 인증서사용도 지원됩니다.
와일드카드 도메인에 대한 하위 도메인이 추가되고 와일드카드 도메인에 인증서가 이미 연결되어 있는 경우 이 하위 도메인에 대한 HTTPS를 비활성화할 수 없습니다. 하위 도메인은 기본적으로 다른 Key Vault 인증서 또는 Front Door 관리 인증서에 의해 재정의되지 않는 한 와일드카드 도메인의 인증서 바인딩을 사용합니다.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>와일드카드 도메인 및 하위 도메인에 대한 웹 응용 프로그램 방화벽

WAF 정책은 다른 도메인과 유사한 와일드카드 도메인에 연결할 수 있습니다. 와일드카드 도메인의 하위 도메인에 다른 WAF 정책을 적용할 수 있습니다. 하위 도메인의 경우 사용할 WAF 정책을 명시적으로 지정해야 하며 와일드카드 도메인과 동일한 정책인 경우에도 사용해야 합니다. 하위 도메인은 와일드카드 도메인에서 WAF 정책을 자동으로 **상속하지 않습니다.**

하위 도메인에 대해 WAF를 실행하지 않으려는 시나리오가 있는 경우 관리되는 규칙이나 사용자 지정 규칙 집합이 없는 빈 WAF 정책을 만들 수 있습니다.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>와일드카드 도메인 및 하위 도메인에 대한 라우팅 규칙

라우팅 규칙을 구성할 때 와일드카드 도메인을 프런트 엔드 호스트로 선택할 수 있습니다. 와일드카드 도메인과 하위 도메인에 대해 서로 다른 경로 동작을 가질 수도 있습니다. [Front Door가 라우트 일치를 수행하는 방법에](front-door-route-matching.md)설명된 대로 런타임에 다른 라우팅 규칙에서 도메인에 대한 가장 구체적인 일치가 선택됩니다.

> [!WARNING]
> **Route 1** `*.foo.com/*` : 백엔드 풀 A및 **Route 2에**매핑된 `bar.foo.com/somePath/*` 두 개의 라우팅 규칙이 있는 경우 백엔드 `bar.foo.com/anotherPath/*`풀 B에 매핑되고 요청이 도착하면 정문이 두 경로 모두에서 일치하는 일치를 찾지 못하므로 클라이언트가 실패를 볼 수 있습니다. 이는 [경로 일치 알고리즘에](front-door-route-matching.md)따라 Front Door는 보다 구체적인 도메인 일치에 따라 Route 2를 선택하지만 일치하는 경로 패턴이 없음을 찾기 때문입니다. 


## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [정문 에서 사용자 지정 도메인을 추가하는](front-door-custom-domain.md)방법에 대해 알아봅니다.
- [사용자 지정 도메인에서 HTTPS를 사용하도록 설정하는](front-door-custom-domain-https.md)방법에 대해 알아봅니다.

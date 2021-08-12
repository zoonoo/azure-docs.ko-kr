---
title: Azure Front Door - 와일드카드 도메인 지원
description: 이 문서는 Azure Front Door에서 사용자 지정 도메인 목록의 와일드카드 도메인 매핑 및 관리를 지원하는 방식을 이해하는 데 도움이 됩니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94425765"
---
# <a name="wildcard-domains"></a>와일드카드 도메인

apex 도메인 및 하위 도메인 외에도 Azure Front Door 프로필에 대한 프런트 엔드 호스트 또는 사용자 지정 도메인에 와일드카드 도메인을 매핑할 수도 있습니다. Azure Front Door 구성에 와일드카드 도메인이 있으면 동일한 라우팅 규칙의 API, 애플리케이션 또는 웹 사이트에 대한 여러 하위 도메인의 트래픽 라우팅 동작이 간소화됩니다. 각 하위 도메인을 개별적으로 추가하거나 지정하기 위해 구성을 수정할 필요가 없습니다. 예를 들어, 동일한 라우팅 규칙을 사용하고 와일드카드 도메인 `*.contoso.com`을 추가하여 `customer1.contoso.com`, `customer2.contoso.com` 및 `customerN.contoso.com`에 대한 라우팅을 정의할 수 있습니다.

와일드카드 도메인에 대한 지원으로 개선된 주요 시나리오는 다음과 같습니다.

- Azure Front Door 프로필에서 각 하위 도메인을 온보딩한 다음, HTTPS가 각 하위 도메인에 대한 인증서를 바인딩하도록 설정할 필요가 없습니다.
- 애플리케이션이 새 하위 도메인을 추가하는 경우 프로덕션 Azure Front Door 구성을 변경할 필요가 없습니다. 이전에는 하위 도메인을 추가하고, 인증서를 바인딩하고, WAF(웹 애플리케이션 방화벽) 정책을 연결한 다음, 도메인을 다른 라우팅 규칙에 추가해야 했습니다.

> [!NOTE]
> 현재 Azure DNS를 통해 와일드카드 도메인을 추가하는 것은 API, PowerShell 및 Azure CLI를 통해서만 지원됩니다. Azure Portal의 와일드카드 도메인 추가 및 관리에 대한 지원은 사용할 수 없습니다.

## <a name="adding-wildcard-domains"></a>와일드카드 도메인 추가

프런트 엔드 호스트 또는 도메인에 대한 섹션 아래에 와일드카드 도메인을 추가할 수 있습니다. 하위 도메인과 마찬가지로 Azure Front Door는 와일드카드 도메인에 대한 CNAME 레코드 매핑이 있는지 확인합니다. 이 DNS 매핑은 `contoso.azurefd.net`에 매핑된 `*.contoso.com`과 같은 직접 CNAME 레코드 매핑일 수 있습니다. 또는 afdverify 임시 매핑을 사용할 수 있습니다. 예를 들어 `afdverify.contoso.azurefd.net`에 매핑된 `afdverify.contoso.com`은 와일드카드에 대한 CNAME 레코드 맵의 유효성을 검사합니다.

> [!NOTE]
> Azure DNS는 와일드카드 레코드를 지원합니다.

프런트 엔드 호스트의 최대 한도까지 프런트 엔드 호스트에서 와일드카드 도메인의 단일 수준 하위 도메인을 추가할 수 있습니다. 다음과 같은 경우에 이 기능이 필요할 수 있습니다.

- 하위 도메인에 대해 와일드카드 도메인의 나머지 도메인과는 다른 경로 정의

- 특정 하위 도메인에 대해 다른 WAF 정책 유지. 예를 들어 `*.contoso.com`은 도메인 소유권을 다시 증명하지 않고도 `foo.contoso.com`을 추가할 수 있도록 합니다. 그러나 `foo.bar.contoso.com`은 `*.contoso.com`의 단일 수준 하위 도메인이 아니기 때문에 허용되지 않습니다. 추가 도메인 소유권 유효성 검사 없이 `foo.bar.contoso.com`을 추가하려면 `*.bar.contosonews.com`을 추가해야 합니다.

다음과 같은 특정 제한 사항에 따라 와일드카드 도메인 및 해당 하위 도메인을 추가할 수 있습니다.

- 와일드카드 도메인이 Azure Front Door 프로필에 추가되는 경우:
  - 와일드카드 도메인을 다른 Azure Front Door 프로필에 추가할 수 없습니다.
  - 와일드카드 도메인의 첫 번째 수준 하위 도메인은 다른 Azure Front Door 프로필 또는 Azure Content Delivery Network 프로필에 추가할 수 없습니다.
- 와일드카드 도메인의 하위 도메인이 이미 Azure Front Door 프로필 또는 Azure Content Delivery Network 프로필에 추가된 경우 다른 Azure Front Door 프로필에 해당 와일드카드 도메인을 사용할 수 없습니다.
- 두 프로필(Azure Front Door 또는 Azure Content Delivery Network)에 루트 도메인의 여러 하위 도메인이 있는 경우 와일드카드 도메인을 프로필 중 하나에 추가할 수 없습니다.

## <a name="certificate-binding"></a>인증서 바인딩

와일드카드 도메인에서 HTTPS 트래픽을 허용하려면 와일드카드 도메인에서 HTTPS를 사용하도록 설정해야 합니다. 와일드카드 도메인에 대한 인증서 바인딩에는 와일드카드 인증서가 필요합니다. 즉, 인증서의 주체 이름에 와일드카드 도메인도 있어야 합니다.

> [!NOTE]
> 현재는 와일드카드 도메인에 대해 HTTPS를 사용하도록 설정하기 위해 고유한 사용자 지정 SSL 인증서 옵션만 사용할 수 있습니다. 와일드카드 도메인에는 Azure Front Door 관리 인증서를 사용할 수 없습니다.

Azure Key Vault의 동일한 와일드카드 인증서를 사용하거나 하위 도메인에 대한 Azure Front Door 관리형 인증서를 사용하도록 선택할 수 있습니다.

이미 연결된 인증서가 있는 와일드카드 도메인에 하위 도메인이 추가된 경우 하위 도메인에 대해 HTTPS를 사용하지 않도록 설정할 수 없습니다. 다른 Key Vault 또는 Azure Front Door 관리형 인증서로 재정의되지 않는 한, 하위 도메인은 와일드카드 도메인에 대한 인증서 바인딩을 사용합니다.

## <a name="waf-policies"></a>WAF 정책

WAF 정책은 다른 도메인과 유사하게 와일드카드 도메인에 연결될 수 있습니다. 와일드카드 도메인의 하위 도메인에 다른 WAF 정책을 적용할 수 있습니다. 하위 도메인의 경우 와일드카드 도메인과 동일한 정책이더라도 사용할 WAF 정책을 지정해야 합니다. 하위 도메인은 와일드카드 도메인에서 WAF 정책을 자동으로 상속하지 않습니다.

하위 도메인에 대해 WAF 정책을 실행하지 않으려면 관리형 또는 사용자 지정 규칙 세트가 없는 빈 WAF 정책을 만들 수 있습니다.

## <a name="routing-rules"></a>라우팅 규칙

라우팅 규칙을 구성할 때 와일드카드 도메인을 프런트 엔드 호스트로 선택할 수 있습니다. 와일드카드 도메인 및 하위 도메인에 대해 다른 경로 동작을 유지할 수도 있습니다. [Azure Front Door에서 경로 일치를 수행하는 방법](front-door-route-matching.md)에 설명된 대로 런타임에 여러 라우팅 규칙 중에서 도메인과 가장 일치하는 경로가 선택됩니다.

> [!IMPORTANT]
> 라우팅 규칙에서 일치하는 경로 패턴이 있어야 합니다. 그렇지 않으면 클라이언트에 오류가 표시됩니다. 예를 들어, 경로 1(백 엔드 풀 A에 매핑된 `*.foo.com/*`) 및 경로 2(백 엔드 풀 B에 매핑된 `/bar.foo.com/somePath/*`)와 같은 두 개의 라우팅 규칙이 있습니다. 그런 다음, `bar.foo.com/anotherPath/*`에 대한 요청이 도착합니다. Azure Front Door는 보다 구체적으로 일치하는 도메인을 기준으로 경로 2를 선택하며, 경로 간에 일치하는 경로 패턴을 찾지 못합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Front Door 프로필을 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Azure Front Door에서 사용자 지정 도메인을 추가](front-door-custom-domain.md)하는 방법을 알아봅니다.
- [사용자 지정 도메인에 HTTPS를 사용하는 방법](front-door-custom-domain-https.md)을 알아봅니다.

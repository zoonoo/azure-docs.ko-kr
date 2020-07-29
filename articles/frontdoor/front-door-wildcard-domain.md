---
title: Azure 전면 도어-와일드 카드 도메인 지원
description: 이 문서는 Azure Front 도어가 사용자 지정 도메인 목록에서 와일드 카드 도메인의 매핑 및 관리를 지 원하는 방식을 이해 하는 데 도움이 됩니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81768311"
---
# <a name="wildcard-domains"></a>와일드 카드 도메인

Apex 도메인 및 하위 도메인을 제외 하 고, 와일드 카드 도메인 이름을 Azure Front 도어 프로필의 프런트 엔드 호스트 또는 사용자 지정 도메인 목록에 매핑할 수 있습니다. Azure 프런트 도어 구성에 와일드 카드 도메인이 있으면 동일한 라우팅 규칙에서 API, 응용 프로그램 또는 웹 사이트에 대 한 여러 하위 도메인에 대 한 트래픽 라우팅 동작이 간소화 됩니다. 각 하위 도메인을 별도로 추가 하거나 지정 하도록 구성을 수정할 필요가 없습니다. 예를 들어 `customer1.contoso.com` `customer2.contoso.com` `customerN.contoso.com` 동일한 라우팅 규칙을 사용 하 고 와일드 카드 도메인을 추가 하 여, 및에 대 한 라우팅을 정의할 수 있습니다 `*.contoso.com` .

와일드 카드 도메인에 대 한 지원으로 향상 된 주요 시나리오는 다음과 같습니다.

- Azure 프런트 도어 프로필에서 각 하위 도메인을 등록 한 다음 HTTPS에서 각 하위 도메인에 대 한 인증서를 바인딩할 수 있도록 설정 하지 않아도 됩니다.
- 응용 프로그램이 새 하위 도메인을 추가 하는 경우 더 이상 프로덕션 Azure Front 도어 구성을 변경할 필요가 없습니다. 이전에는 하위 도메인을 추가 하 고, 여기에 인증서를 바인딩하고, WAF (웹 응용 프로그램 방화벽) 정책을 연결한 후 다른 라우팅 규칙에 도메인을 추가 해야 했습니다.

> [!NOTE]
> 현재 와일드 카드 도메인은 API, PowerShell 및 Azure CLI 통해서만 지원 됩니다. Azure Portal에서 와일드 카드 도메인 추가 및 관리에 대 한 지원을 사용할 수 없습니다.

## <a name="adding-wildcard-domains"></a>와일드 카드 도메인 추가

프런트 엔드 호스트 또는 도메인의 섹션 아래에 와일드 카드 도메인을 추가할 수 있습니다. 하위 도메인과 마찬가지로 Azure Front 도어는 와일드 카드 도메인에 대 한 CNAME 레코드 매핑이 있는지 확인 합니다. 이 DNS 매핑은에 매핑되는 것과 같은 직접 CNAME 레코드 매핑이 될 수 있습니다 `*.contoso.com` `contoso.azurefd.net` . 또는 afdverify 임시 매핑을 사용할 수 있습니다. 예를 들어 `afdverify.contoso.com` 로 매핑하면는 `afdverify.contoso.azurefd.net` 와일드 카드에 대 한 CNAME 레코드 맵의 유효성을 검사 합니다.

> [!NOTE]
> Azure DNS는 와일드카드 레코드를 지원합니다.

프런트 엔드 호스트의 최대 한도까지 프런트 엔드 호스트에서 와일드 카드 도메인의 단일 수준 하위 도메인을 추가할 수 있습니다. 이 기능은 다음과 같은 경우에 필요할 수 있습니다.

- 하위 도메인에 대 한 다른 경로를 정의 하는 것은 와일드 카드 도메인의 나머지 도메인입니다.

- 특정 하위 도메인에 대해 다른 WAF 정책을 보유 하 고 있습니다. 예를 들어는 `*.contoso.com` `foo.contoso.com` 도메인 소유권을 다시 증명 하지 않고도를 추가할 수 있습니다. 그러나 `foo.bar.contoso.com` 의 단일 수준 하위 도메인이 아니기 때문에 허용 되지 않습니다 `*.contoso.com` . 추가 `foo.bar.contoso.com` 도메인 소유권 유효성 검사 없이 추가 하려면를 추가 해야 `*.bar.contosonews.com` 합니다.

특정 제한 사항이 있는 와일드 카드 도메인 및 하위 도메인을 추가할 수 있습니다.

- 와일드 카드 도메인이 Azure Front 문 프로필에 추가 되는 경우:
  - 와일드 카드 도메인은 다른 Azure Front 문 프로필에 추가할 수 없습니다.
  - 와일드 카드 도메인의 첫 번째 수준 하위 도메인은 다른 Azure Front 문 프로필 또는 Azure Content Delivery Network 프로필에 추가할 수 없습니다.
- 와일드 카드 도메인의 하위 도메인을 Azure 프런트 도어 프로필 또는 Azure Content Delivery Network 프로필에 추가 하는 경우 다른 Azure Front 문 프로필에 와일드 카드 도메인을 추가할 수 없습니다.
- 두 프로필 (Azure Front 문이나 Azure Content Delivery Network)에 루트 도메인의 여러 하위 도메인이 있는 경우 와일드 카드 도메인을 프로필 중 하나에 추가할 수 없습니다.

## <a name="certificate-binding"></a>인증서 바인딩

와일드 카드 도메인에서 HTTPS 트래픽을 허용 하려면 와일드 카드 도메인에서 HTTPS를 사용 하도록 설정 해야 합니다. 와일드 카드 도메인에 대 한 인증서 바인딩에는 와일드 카드 인증서가 필요 합니다. 즉, 인증서의 주체 이름에 와일드 카드 도메인도 있어야 합니다.

> [!NOTE]
> 현재는 고유한 사용자 지정 SSL 인증서 옵션만 사용 하 여 와일드 카드 도메인에 대해 HTTPS를 사용 하도록 설정할 수 있습니다. 와일드 카드 도메인에는 Azure Front 도어 관리 인증서를 사용할 수 없습니다.

Azure Key Vault에서와 동일한 와일드 카드 인증서를 사용 하거나 하위 도메인에 대 한 Azure Front 도어가 관리 되는 인증서를 사용 하도록 선택할 수 있습니다.

이미 연결 된 인증서가 있는 와일드 카드 도메인에 대해 하위 도메인을 추가 하는 경우 하위 도메인에 대 한 HTTPS를 사용 하지 않도록 설정할 수 없습니다. 다른 Key Vault 또는 Azure Front 도어 관리 인증서가 재정의 되지 않는 한 하위 도메인은 와일드 카드 도메인에 대 한 인증서 바인딩을 사용 합니다.

## <a name="waf-policies"></a>WAF 정책

WAF 정책은 다른 도메인과 비슷하게 와일드 카드 도메인에 연결할 수 있습니다. 와일드 카드 도메인의 하위 도메인에는 다른 WAF 정책을 적용할 수 있습니다. 하위 도메인의 경우 와일드 카드 도메인과 동일한 정책 이더라도 사용할 WAF 정책을 지정 해야 합니다. 하위 도메인은 와일드 카드 도메인에서 WAF 정책을 자동으로 상속 *하지 않습니다* .

하위 도메인에 대해 WAF 정책을 실행 하지 않으려는 경우에는 관리 되거나 사용자 지정 규칙 집합을 사용 하지 않고 빈 WAF 정책을 만들 수 있습니다.

## <a name="routing-rules"></a>라우팅 규칙

라우팅 규칙을 구성 하는 경우 와일드 카드 도메인을 프런트 엔드 호스트로 선택할 수 있습니다. 와일드 카드 도메인 및 하위 도메인에 대해 다른 경로 동작을 사용할 수도 있습니다. [Azure Front 도어가 경로 일치를 수행 하는 방법](front-door-route-matching.md)에 설명 된 대로 여러 라우팅 규칙에서 도메인에 대 한 가장 구체적인 일치 항목이 런타임에 선택 됩니다.

> [!IMPORTANT]
> 라우팅 규칙에 일치 하는 경로 패턴이 있어야 합니다. 그렇지 않으면 클라이언트에 오류가 표시 됩니다. 예를 들어 경로 1 ( `*.foo.com/*` 백 엔드 풀 A에 매핑됨)과 경로 2 ( `bar.foo.com/somePath/*` 백 엔드 풀 B로 매핑됨)와 같은 라우팅 규칙이 두 개 있습니다. 그런 다음에 대 한 요청이 도착 `bar.foo.com/anotherPath/*` 합니다. Azure 전면 도어는 더 구체적인 도메인 일치를 기반으로 경로 2를 선택 합니다. 경로 전체에서 일치 하는 경로 패턴을 찾을 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Front 도어 프로필을 만드는](quickstart-create-front-door.md)방법에 대해 알아봅니다.
- [Azure Front 문에 사용자 지정 도메인을 추가](front-door-custom-domain.md)하는 방법에 대해 알아봅니다.
- [사용자 지정 도메인에서 HTTPS를 사용 하도록 설정](front-door-custom-domain-https.md)하는 방법을 알아봅니다.

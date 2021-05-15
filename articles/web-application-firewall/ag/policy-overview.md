---
title: Azure WAF(웹 애플리케이션 방화벽) 정책 개요
description: 이 문서는 WAF(웹 애플리케이션 방화벽) 글로벌, 사이트별 및 URI별 정책에 대한 개요입니다.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518739"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Azure WAF(웹 애플리케이션 방화벽) 정책 개요

Web Application Firewall 정책에는 모든 WAF 설정 및 구성이 포함됩니다. 여기에는 제외, 사용자 지정 규칙, 관리 규칙 등이 포함됩니다. 해당 정책은 애플리케이션 게이트웨이(글로벌), 수신기(사이트별) 또는 경로 기반 규칙(URI별)에 연결되어 적용됩니다.

만들 수 있는 정책의 수에는 제한이 없습니다. 정책을 만들 때 적용하려면 애플리케이션 게이트웨이에 연결해야 합니다. 애플리케이션 게이트웨이, 수신기 및 경로 기반 규칙의 모든 조합과 연결할 수 있습니다.

## <a name="global-waf-policy"></a>글로벌 WAF 정책

WAF 정책을 글로벌로 연결하면 애플리케이션 게이트웨이 WAF 뒤의 모든 사이트가 동일한 관리 규칙, 사용자 지정 규칙, 제외 및 기타 구성된 설정으로 보호됩니다.

단일 정책을 모든 사이트에 적용하려면 정책을 애플리케이션 게이트웨이와 연결할 수 있습니다. 자세한 내용은 [애플리케이션 게이트웨이에 대한 Web Application Firewall 정책 만들기](create-waf-policy-ag.md)를 참조하여 Azure Portal을 사용하여 WAF 정책을 만들고 적용하세요. 

## <a name="per-site-waf-policy"></a>사이트별 WAF 정책

사이트별 WAF 정책을 사용하면 사이트별 정책을 사용하여 단일 WAF 뒤에 서로 다른 보안 요구 사항으로 여러 사이트를 보호할 수 있습니다. 예를 들어 WAF 뒤에 5개의 사이트가 있는 경우 5개의 개별 WAF 정책(각 수신기에 대해 하나)을 사용하여 각 사이트에 대한 제외, 사용자 지정 규칙, 관리 규칙 집합 및 기타 모든 WAF 설정을 사용자 지정할 수 있습니다.

Application Gateway에 글로벌 정책이 적용되었다고 가정해 보겠습니다. 그런 다음 해당 Application Gateway의 수신기에 다른 정책을 적용합니다. 이제 수신기의 정책이 해당 수신기에만 적용됩니다. Application Gateway의 글로벌 정책은 특정 정책이 할당되지 않은 다른 모든 수신기 및 경로 기반 규칙에 계속 적용됩니다.

## <a name="per-uri-policy"></a>URI별 정책

URI 수준까지 더 많은 사용자 지정을 위해 WAF 정책을 경로 기반 규칙과 연결할 수 있습니다. 단일 사이트 내에 다른 정책이 필요한 특정 페이지가 있으면 지정된 URI에만 영향을 주는 WAF 정책을 변경할 수 있습니다. 이는 WAF 뒤의 다른 사이트보다 훨씬 더 구체적인 WAF 정책이 필요한 결제 또는 로그인 페이지나 기타 모든 URI에 적용할 수 있습니다.

사이트별 WAF 정책과 마찬가지로 더 구체적인 정책이 덜 구체적인 정책보다 더 중요합니다. 즉, URL 경로 맵의 URI별 정책이 그 위의 사이트별 또는 글로벌 WAF 정책보다 중요함을 의미합니다.

### <a name="example"></a>예제

동일한 애플리케이션 게이트웨이 뒤에 contoso.com, fabrikam.com 및 adatum.com의 세 사이트가 있다고 가정해보겠습니다. 세 사이트 모두에 WAF를 적용하려 하지만 adatum.com은 고객이 제품을 방문, 검색 및 구매하는 곳이기 때문에 추가 보안이 필요합니다.

일부 가양성이 트래픽을 차단하는 것을 중지하기 위해 필요한 경우 몇 가지 기본 설정, 제외 또는 사용자 지정 규칙을 사용하여 WAF에 글로벌 정책을 적용할 수 있습니다. 이 경우 fabrikam.com 및 contoso.com은 SQL 백 엔드가 없는 고정적인 페이지이므로 글로벌 SQL 삽입 규칙을 실행할 필요가 없습니다. 따라서 글로벌 정책에서 해당 규칙을 사용하지 않도록 설정할 수 있습니다.

글로벌 정책은 contoso.com 및 fabrikam.com에는 적합하지만 로그인 정보 및 결제가 처리되는 adatum.com에서는 주의가 더 필요합니다. adatum 수신기에 사이트별 정책을 적용하고 SQL 규칙을 계속 실행할 수 있습니다. 또한 일부 트래픽을 차단하는 쿠키가 있다고 가정하면 해당 쿠키에 대한 제외를 만들어 가양성을 중지할 수 있습니다. 

adatum.com/payments URI에는 주의를 기울여야 합니다. 따라서 해당 URI에 다른 정책을 적용하고 모든 규칙을 사용하도록 설정된 상태로 두고 모든 제외도 제거합니다.

이 예제에서는 두 사이트에 적용되는 글로벌 정책이 있습니다. 하나의 사이트에 적용되는 사이트별 정책과 하나의 특정 경로 기반 규칙에 적용되는 URI별 정책이 있습니다. 이 예제의 해당 PowerShell은 [Azure PowerShell을 사용하여 사이트별 WAF 정책 구성](per-site-policies.md)을 참조하세요.

## <a name="existing-waf-configurations"></a>기존 WAF 구성

모든 새 웹 애플리케이션 방화벽의 WAF 설정(사용자 지정 규칙, 관리되는 규칙 집합 구성, 제외 등)이 WAF 정책에 존재합니다. 기존 WAF가 있는 경우 해당 설정은 WAF 구성에 여전히 남아있을 수 있습니다. 새 WAF 정책으로 이동하는 방법에 대한 자세한 내용은 [WAF 구성을 WAF 정책으로 마이그레이션](./migrate-policy.md)을 참조하세요. 


## <a name="next-steps"></a>다음 단계

- [Azure PowerShell를 사용하여 사이트별 및 URI별 정책을 만들기](per-site-policies.md).

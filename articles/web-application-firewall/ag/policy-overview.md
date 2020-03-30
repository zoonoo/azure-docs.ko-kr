---
title: AZURE 웹 응용 프로그램 방화벽(WAF) 정책 개요
description: 이 문서에서는 WAF(웹 응용 프로그램 방화벽) 전역, 사이트별 및 URI별 정책에 대한 개요입니다.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060265"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>AZURE 웹 응용 프로그램 방화벽(WAF) 정책 개요

웹 응용 프로그램 방화벽 정책에는 모든 WAF 설정 및 구성이 포함됩니다. 여기에는 제외, 사용자 지정 규칙, 관리되는 규칙 등이 포함됩니다. 그런 다음 이러한 정책은 응용 프로그램 게이트웨이(전역), 수신기(사이트별) 또는 URI당 경로 기반 규칙(URI당)에 연결됩니다.

> [!NOTE]
> 사이트별 및 URI별 정책은 공용 미리 보기에 있는 Azure 웹 응용 프로그램 방화벽(WAF)입니다.
> 
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능이 지원되지 않거나, 기능이 제한되거나, 일부 Azure 위치에 제공되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

만들 수 있는 정책 수에는 제한이 없습니다. 정책을 만들 때 적용하려면 응용 프로그램 게이트웨이에 연결해야 합니다. 응용 프로그램 게이트웨이, 리스너 및 경로 기반 규칙의 조합과 연결할 수 있습니다.

## <a name="global-waf-policy"></a>글로벌 WAF 정책

WAF 정책을 전 세계에 연결하면 응용 프로그램 게이트웨이 WAF 뒤에 있는 모든 사이트가 동일한 관리되는 규칙, 사용자 지정 규칙, 제외 및 기타 구성된 설정으로 보호됩니다.

단일 정책을 모든 사이트에 적용하려면 정책을 응용 프로그램 게이트웨이와 연결할 수 있습니다. 자세한 내용은 Azure 포털을 사용하여 WAF 정책을 만들고 적용하는 [응용 프로그램 게이트웨이에 대한 웹 응용 프로그램 방화벽 정책 만들기를](create-waf-policy-ag.md) 참조하십시오. 

## <a name="per-site-waf-policy"></a>사이트별 WAF 정책

사이트별 WAF 정책을 사용하면 사이트별 정책을 사용하여 단일 WAF 뒤에 서로 다른 보안 요구 사항이 있는 여러 사이트를 보호할 수 있습니다. 예를 들어 WAF 뒤에 5개의 사이트가 있는 경우 각 사이트에 대한 제외, 사용자 지정 규칙, 관리되는 규칙 집합 및 기타 모든 WAF 설정을 사용자 지정하기 위해 5개의 별도의 WAF 정책(각 수신기에 대해 하나씩)을 가질 수 있습니다.

응용 프로그램 게이트웨이에 글로벌 정책이 적용되어 있다고 가정해 보세요. 그런 다음 해당 응용 프로그램 게이트웨이의 수신기에 다른 정책을 적용합니다. 이제 수신기의 정책이 해당 수신기에만 적용됩니다. 응용 프로그램 게이트웨이의 전역 정책은 여전히 다른 모든 수신기 및 할당된 특정 정책이 없는 경로 기반 규칙에 적용됩니다.

## <a name="per-uri-policy"></a>URI별 정책

URI 수준까지 더 많은 사용자 지정을 위해 WAF 정책을 경로 기반 규칙과 연결할 수 있습니다. 다른 정책이 필요한 단일 사이트 내에 특정 페이지가 있는 경우 지정된 URI에만 영향을 주는 WAF 정책을 변경할 수 있습니다. 이는 결제 또는 로그인 페이지 또는 WAF 뒤에 있는 다른 사이트보다 훨씬 더 구체적인 WAF 정책이 필요한 다른 URI에 적용될 수 있습니다.

사이트별 WAF 정책과 마찬가지로 보다 구체적인 정책은 덜 구체적인 정책보다 우선합니다. 즉, URL 경로 맵의 URI당 정책이 사이트별 또는 전역 WAF 정책을 재정의합니다.

## <a name="example"></a>예제

동일한 응용 프로그램 게이트웨이 뒤에 contoso.com, fabrikam.com 및 adatum.com 세 개의 사이트가 있다고 가정해 보세요. 세 사이트 모두에 WAF를 적용하려고 하지만 고객이 제품을 방문하고 탐색하고 구매하는 곳이기 때문에 adatum.com 보안을 강화해야 합니다.

일부 잘못된 긍정이 트래픽을 차단하는 것을 막기 위해 필요한 경우 몇 가지 기본 설정, 제외 또는 사용자 지정 규칙을 사용하여 WAF에 전역 정책을 적용할 수 있습니다. 이 경우 fabrikam.com 및 contoso.com SQL 백엔드가 없는 정적 페이지이기 때문에 전역 SQL 삽입 규칙을 실행할 필요가 없습니다. 따라서 전역 정책에서 이러한 규칙을 사용하지 않도록 설정할 수 있습니다.

이 글로벌 정책은 contoso.com 및 fabrikam.com 적합하지만 로그인 정보 및 결제가 처리되는 adatum.com 더 주의해야 합니다. adatum 수신기에 사이트별 정책을 적용하고 SQL 규칙을 실행 상태로 둘 수 있습니다. 또한 일부 트래픽을 차단하는 쿠키가 있다고 가정하여 해당 쿠키에 대한 제외를 만들어 거짓 긍정을 중지할 수 있습니다. 

adatum.com/payments URI는 주의해야 할 곳입니다. 따라서 해당 URI에 다른 정책을 적용하고 모든 규칙을 사용하도록 설정하고 모든 제외를 제거합니다.

이 예제에서는 두 사이트에 적용되는 전역 정책이 있습니다. 한 사이트에 적용되는 사이트별 정책과 하나의 특정 경로 기반 규칙에 적용되는 URI별 정책이 있습니다. 이 예제에서는 해당 PowerShell에 대한 사이트별 및 URI당 정책을 만드는 방법(있는 경우 여기에 링크 삽입)을 참조하십시오.

## <a name="existing-waf-configurations"></a>기존 WAF 구성

모든 새 웹 응용 프로그램 방화벽의 WAF 설정(사용자 지정 규칙, 관리되는 규칙 집합 구성, 제외 등)은 WAF 정책에 있습니다. 기존 WAF가 있는 경우 이러한 설정은 WAF 구성에 계속 존재할 수 있습니다. 새 WAF 정책으로 이동하는 것에 대한 자세한 내용은 [WAF 구성을 WAF 정책으로 마이그레이션합니다.](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy) 


## <a name="next-steps"></a>다음 단계

Azure PowerShell을 사용하여 사이트별 및 URI별 정책을 만듭니다.
